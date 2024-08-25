domain_manager.cpp

```cpp [303: ]
// -------------------------------------------------------------------------- //
// Function:    Execute
// Description: Can be used to sequence through a specified execution group
//              of domains. This is not thread safe.
// -------------------------------------------------------------------------- //
void DomainManager::Execute(uint32_t group)
{
  if ((group != 0) && (group <= grouped_domain_objects_.size()))
  {
    for (auto const &d : grouped_domain_objects_[group - 1].sequenced_dmn_instances_)
    {
      d.instance_->Execute(d.task_);
    }
  }
}

// -------------------------------------------------------------------------- //
// Function:    GetExecutionGroup
// Description: Retrieves a specified execution group of domains
// -------------------------------------------------------------------------- //
bool DomainManager::GetExecutionGroup(uint32_t group, GroupConfigurationInfo &group_config) const
{
  bool retrieved = false;
  if ((group != 0) && (group <= grouped_domain_objects_.size()))
  {
    group_config = grouped_domain_objects_[group - 1];
    retrieved = true;
  }
  return retrieved;
}

```

Note:  
- DomainManager execute functions:
- give us greater control over how we choose to execute our domain sequencing