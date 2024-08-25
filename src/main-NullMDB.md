#### main.cpp::NullMDB..

```cpp [85: ]

class NullMDB_API : public wosa_api::MDB_API // NOLINT(readability-identifier-naming)
{
public:
  using MDB_API::MDB_API;

  bool Read(wosa_api::WosaMessage & /*message*/) override { return false; }

  bool Write(const wosa_api::WosaMessage & /*message*/) override { return true; }
};

class NullMDBManager : public arise::msw::MDBManager
{
public:
  NullMDBManager() = default;
  ~NullMDBManager() override = default;

  bool Configure(const FilePortsList & /*list*/) override { return true; }

  void GetMDBDomains(MDBDomainsList & /*list*/) const override {}

  std::shared_ptr< wosa_api::MDB_API >
  GetMDBInterface(uint8_t /*mdb_instance_id*/,
                  uint8_t /*mdb_element_id*/,
                  const wosa_api::DieIdType & /*connecting_dmn_die*/) const override
  {
    return std::make_shared< NullMDB_API >();
  }
};

```

[source](https://gitlab.us.lmco.com/hub/incubator/mra/modules/arise-msw-wosa/-/blob/develop/src/wosa-domain-manager/examples/01-domain-manager/main.cpp?ref_type=heads#L85)
