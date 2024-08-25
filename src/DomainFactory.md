#### DomainFactory()

```cpp [12: 2-9 | 15]

using DomainFactoryType =
  ClassFactory< wosa_api::Domain_API,
                // The following are the constructor parameters of the base Domain_API class:
                wosa_api::DomainIdType,
                std::shared_ptr< wosa_api::MDB_API >,
                const wosa_api::DomainConfigurationType &,
                std::shared_ptr< wosa_api::Config_API >,
                std::shared_ptr< wosa_api::HW_API > >;

/**
 * @brief DomainFactory is a class to be used and shared by all processes (plugin libraries
 * and the main executable)
 */
class CORE_API DomainFactory : public Singleton< DomainFactoryType >
{
public:
  /**
   * @brief DomainFactory defaultconstructor
   */
  DomainFactory() = default;

  /**
   * @brief DomainFactory default destructor
   */
  ~DomainFactory() = default;
};


```

[source](https://gitlab.us.lmco.com/hub/incubator/mra/modules/arise-msw-wosa/-/blob/develop/src/wosa-domain-manager/examples/01-domain-manager/main.cpp?ref_type=heads#L162)
