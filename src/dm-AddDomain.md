#### this is my header

```cpp [262: ]
// -------------------------------------------------------------------------- //
// Function:    AddDomain
// Description: Attempts to add the passed in Domain object to its container
// -------------------------------------------------------------------------- //
bool DomainManager::AddDomain(std::shared_ptr< wosa_api::Domain_API > dmn)
{
  bool added = false;

  if (dmn != nullptr)
  {
    const wosa_api::DomainIdType &dmn_id = dmn->GetDomainInfo();

    WOSACommonTypes::DIEUnion unique_id;
    unique_id.id_.domain_id_ = dmn_id.die_id_.domain_id_;
    unique_id.id_.instance_id_ = dmn_id.die_id_.instance_id_;
    unique_id.id_.element_id_ = dmn_id.die_id_.element_id_;

    // std::map emplace function will not overwrite an existing element. Its
    // returned type of pair<iterator,bool> is used to determine if the
    // insertion was successful or not.
    added = domain_objects_.emplace(unique_id.combined_id_, std::move(dmn)).second;
    if (!added)
    {
      SimpleString< 64 > out;
      SPrintf(out, "Failed to store %s") << dmn_id.domain_name_.c_str();
      ReportErrorCode("DomainManager::AddDomain", out.CStr(), unique_id.combined_id_);
    }
  }
  return added;
}
```
