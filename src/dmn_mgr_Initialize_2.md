DomainManager::Initialize()

```cpp [79: 82-84|89-92|110-117|119-182 ]
// -------------------------------------------------------------------------- //
// Function:    Initialize
// Description: Uses the parser to retrieve information about the domain
//              plugins as well as the domains execution groups
// -------------------------------------------------------------------------- //
bool DomainManager::Initialize(const DomainConfigInitJSON &config_init)
{
  if (mdb_mgr_ == nullptr)
  {
    return false;
  }

  bool initialized = true;

  // Add MDB Domain object(s) to the Domain Manager
  MDBManager::MDBDomainsList vec;
  mdb_mgr_->GetMDBDomains(vec);
  if (std::any_of(vec.begin(),
                  vec.end(),
                  [&](std::shared_ptr< wosa_api::Domain_API > mdb)
                  { return !this->AddDomain(std::move(mdb)); }))
  {
    initialized = false;
  }

  if (plugin_mgr_ != nullptr)
  {
    // Retrieve configuration info for the dynamic plugins
    DomainConfigInitJSON::DomainPluginsConfigInfo plugins_info;
    if (config_init.GetPluginsConfigInfo(plugins_info))
    {
      // Attempt to load the plugins
      for (auto const &info : plugins_info)
      {
        DynamicPluginManager::PluginInfo plugin;
        plugin.name_ = info.plugin_filename_;
        plugin.registration_key_ = info.registration_key_;
        if (!plugin_mgr_->Load(plugin))
        {
          SimpleString< 64 > out_string;
          SPrintf(out_string, "Unable to load plugin: %s") << plugin.name_.c_str();
          ReportErrorCode("DomainManager", out_string.CStr());
          initialized = false;
        }
        ReportEvent(plugin_mgr_->GetLoadStatus().c_str());
      }
    }
  }

  // Retrieve configuration info for the domain instances
  DomainConfigInitJSON::DomainInstancesConfigInfo instances_info;
  if (config_init.GetInstancesConfigInfo(instances_info))
  {
    WOSACommonTypes::DIEUnion unique_id;

    wosa_api::DomainIdType domain_identifier;
    wosa_api::DomainConfigurationType domain_config;

    // For each domain instance
    for (auto const &di_info : instances_info)
    {
      domain_identifier.domain_name_ = di_info.domain_name_;
      domain_identifier.die_id_.domain_id_ = di_info.die_id_.domain_id_;
      domain_identifier.die_id_.instance_id_ = di_info.die_id_.instance_id_;
      domain_identifier.die_id_.element_id_ = di_info.die_id_.element_id_;
      domain_config.file_name_ = di_info.tdp_file_path_name_;

      // Use union to create unique ID for use as map key
      unique_id.id_.domain_id_ = di_info.die_id_.domain_id_;
      unique_id.id_.instance_id_ = di_info.die_id_.instance_id_;
      unique_id.id_.element_id_ = di_info.die_id_.element_id_;

      // Retrieve the appropriate MDB Interface instance slated for this domain, as more than
      // one MDB can be present for a system/processor.
      std::shared_ptr< wosa_api::MDB_API > mdb_api =
        mdb_mgr_->GetMDBInterface(di_info.connecting_mdb_instance_id_,
                                  di_info.connecting_mdb_element_id_,
                                  domain_identifier.die_id_);

      // All plugins self-register themselves with the Domain Factory. Let's use
      // the Domain_Factory to create the desired individual domains.
      std::shared_ptr< wosa_api::Domain_API > obj(DomainFactory::Instance().Create(
        di_info.registration_key_, domain_identifier, mdb_api, domain_config, nullptr, nullptr));
      if (obj != nullptr)
      {
        ReportEventSPrintf("DmnMgr: Created %s (%08X)")
          << domain_identifier.domain_name_.c_str() << unique_id.combined_id_;

        // std::map emplace function will not overwrite an existing element. Its
        // returned type of pair<iterator,bool> is used to determine if the
        // insertion was successful or not.
        if (!domain_objects_.emplace(unique_id.combined_id_, std::move(obj)).second)
        {
          SimpleString< 64 > out_string;
          SPrintf(out_string, "Failed to store %s") << domain_identifier.domain_name_.c_str();
          ReportErrorCode("DomainManager", out_string.CStr(), unique_id.combined_id_);
          initialized = false;
        }
      }
      else
      {
        SimpleString< 64 > out_string;
        SPrintf(out_string, "Failed to create %s") << domain_identifier.domain_name_.c_str();
        ReportErrorCode("DomainManager", out_string.CStr(), unique_id.combined_id_);
        initialized = false;
      }
    }
  }

  // Loop through and call Initialize on the available domain objects
  for (auto const &obj : domain_objects_)
  {
    if (obj.second != nullptr)
    {
      obj.second->Initialize();
    }
  }

  // Retrieve Domain Execution Groups configuration info
  uint8_t num_groups = config_init.GetNumExecutionGroups();
  if (num_groups > 0)
  {
    WOSACommonTypes::DIEUnion unique_id;
    for (uint8_t i(0); i < num_groups; ++i)
    {
      DomainConfigInitJSON::GroupConfigInfo tmp_exec_group_info;
      if (config_init.GetExecutionGroupInfo(i + 1, tmp_exec_group_info))
      {
        GroupConfigurationInfo group_info;
        group_info.group_id_ = tmp_exec_group_info.group_id_;
        group_info.group_rate_ = tmp_exec_group_info.group_rate_;
        for (auto seq_di : tmp_exec_group_info.group_sequence_)
        {
          // Use union to create unique ID for use as map key
          unique_id.id_.domain_id_ = seq_di.die_id_.domain_id_;
          unique_id.id_.instance_id_ = seq_di.die_id_.instance_id_;
          unique_id.id_.element_id_ = seq_di.die_id_.element_id_;

          // Check to see if the instance requested exists
          auto element = domain_objects_.find(unique_id.combined_id_);
          if (element != domain_objects_.end())
          {
            SequencedDomainInstance seq_element;
            seq_element.instance_ = element->second;
            seq_element.task_ = seq_di.task_;
            group_info.sequenced_dmn_instances_.push_back(seq_element);
          }
          else
          {
            ReportErrorCode(
              "DomainManager", "Requested group member does not exist", unique_id.combined_id_);
            initialized = false;
          }
        }

        if (!group_info.sequenced_dmn_instances_.empty())
        {
          grouped_domain_objects_.push_back(group_info);
        }
        else
        {
          ReportErrorCode("DomainManager",
                          "Invalid group configuration",
                          static_cast< unsigned int >(group_info.sequenced_dmn_instances_.size()));
          initialized = false;
        }
      }
    }
  }
  else
  {
    ReportErrorCode("DomainManager", "No execution groups defined", num_groups);
    initialized = false;
  }

  if (domain_objects_.empty() || grouped_domain_objects_.empty())
  {
    initialized = false;
  }

  return initialized;
}

```

Note:
slide 6.11  
- Once the domain obj has been created we verify its not null and  
- try to place it into the container of Domains (same one we did addDomain for MDB earlier )  
- Then we iterate through that container and initialize each domain  
- Finally place domains into "execution groups" per the json config  