#### domain_manager.cpp

```cpp [36: ]
// -------------------------------------------------------------------------- //
// Function:    DomainManager
// Description: Constructor
// -------------------------------------------------------------------------- //
DomainManager::DomainManager(std::shared_ptr< MDBManager > mdb_mgr,
                             std::shared_ptr< DynamicPluginManager > plugin_mgr)
  : mdb_mgr_(std::move(mdb_mgr)),
    plugin_mgr_(std::move(plugin_mgr))
{}

// -------------------------------------------------------------------------- //
// Function:    ~DomainManager
// Description: Destructor
// -------------------------------------------------------------------------- //
DomainManager::~DomainManager()
{
  // Call Finalize on the instantiated domain objects
  for (auto const &obj : domain_objects_)
  {
    if (obj.second != nullptr)
    {
      // Execute the domains Finalize routine
      obj.second->Finalize();

      // No need to delete the created domain objects as it is handled via
      // smart pointer mechanism (i.e., std::shared_ptr)
    }
  }

  for (auto &group : grouped_domain_objects_)
  {
    group.sequenced_dmn_instances_.clear();
  }
  domain_objects_.clear();

  // Unload all plguins
  if (plugin_mgr_ != nullptr)
  {
    plugin_mgr_->UnloadAll();
  }
}

```

[source](https://gitlab.us.lmco.com/hub/incubator/mra/modules/arise-msw-wosa/-/blob/develop/src/wosa-domain-manager/src/domain_manager.cpp?ref_type=heads#L36)