IntegrationDomainApp class

```cpp [115: 3-16]
// This example demonstrates the basic setup of a Domain App
// IntegrationDomainApp is a child of DomainBase which is a child of Domain_API.
class IntegrationDomainApp : public arise::msw::DomainBase
{
public:
  IntegrationDomainApp(
    wosa_api::DomainIdType domain_id,
    std::shared_ptr< ::wosa_api::MDB_API > mdb_if,
    const wosa_api::DomainConfigurationType &config_data = ::wosa_api::DomainConfigurationType(),
    std::shared_ptr< wosa_api::Config_API > config_if = nullptr,
    std::shared_ptr< wosa_api::HW_API > hw_if = nullptr)
    : arise::msw::DomainBase(std::move(domain_id),
                            std::move(mdb_if),
                            config_data,
                            std::move(config_if),
                            std::move(hw_if))
  {
    std::cout << "Created IntegrationDomainApp with name of \"" << domain_id_.domain_name_ << "\""
              << " and DIE ID <" << static_cast< unsigned int >(domain_id_.die_id_.domain_id_)
              << ", " << static_cast< unsigned int >(domain_id_.die_id_.instance_id_) << ", "
              << static_cast< unsigned int >(domain_id_.die_id_.element_id_) << ">" << std::endl;
  }

  ~IntegrationDomainApp() override = default;

  // The following functions override the pure virtual functions of
  // project ::wosa_api::Domain_API class Domain_API.
  void Initialize() override
  {
    std::cout << "Initializing Integration Domain \"" << domain_id_.domain_name_ << "\""
              << std::endl;
  }
  void Execute(unsigned int task = 0) override
  {
    std::cout << "Executing Integration Domain \"" << domain_id_.domain_name_ << "\" Task " << task
              << std::endl;
  }
  void Finalize() override
  {
    std::cout << "Finalizing Integration Domain \"" << domain_id_.domain_name_ << "\"" << std::endl;
  }
  void ProcessExecutiveMsg(const ::wosa_api::WosaMessage &message) override
  {
    stored_exec_cmd_ = message;
  }
  wosa_api::WosaMessage stored_exec_cmd_;
};
```

Note:  
- ClassFactory calls this constructor when creating our domain