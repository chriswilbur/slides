#### int main()

```cpp [162: 2|28-31|33-34|36-43]

int main(int argc, const char *argv[])
{
  // uncomment for debugging
  std::array< char, 256 > tmp;
  if (getcwd(tmp.data(), 256) != nullptr)
  {
    std::cout << "Current working directory: " << tmp.data() << std::endl;
  }

  ELogListener elog_listener(arise::msw::OFPUtilsFactory::Get()->sys_error_log_.GetPointer());
  std::cout << "Number of arguments: " << argc << std::endl;
  for (int i = 0; i < argc; i++)
  {
    std::cout << "Argument " << i + 1 << ": " << argv[i] << std::endl;
  }

  std::string input_folder = ".";
  if (argc > 1)
  {
    input_folder = argv[1];
    std::cout << "Starting with config file at " << input_folder << std::endl;
  }

  std::string input_filename = "domain_mgmt_config_example_1.json";
  std::cout << "Full Path of Input File: " << input_folder + "/" + input_filename << std::endl;

  // The following is to demonstrate that domains can be statically linked
  // and don't have to be compiled as separate loadable library plugins
  arise::msw::DomainFactory::Instance().RegisterType< IntegrationDomainApp >(
    "Sample Static Domain");

  std::shared_ptr< arise::msw::Port > domain_config_file_port =
    std::make_shared< arise::msw::MultiFileDriver >("Domain config port");

  // Parse Domain Management input file
  arise::msw::DomainConfigInitJSON config_init(
    domain_config_file_port.get(), input_folder, input_filename);
  if (!config_init.IsDomainMgmtConfigValid())
  {
    std::cout << "Unable to retrieve Domain Mgmt Config File info" << std::endl;
    return -1;
  }

  // Create the concrete MDB Manager
  std::shared_ptr< arise::msw::MDBManager > mdb_manager = std::make_shared< NullMDBManager >();

  // Retrieve the MDB configuration filename(s) and use the MDB Manager to parse it
  std::vector< std::string > const &mdb_config_filenames = config_init.GetMdbConfigFileNames();
  if (!mdb_config_filenames.empty())
  {
    std::shared_ptr< arise::msw::Port > mdb_config_file_port =
      std::make_shared< arise::msw::MultiFileDriver >("MDB config port");
    arise::msw::MDBManager::FilePortsList file_ports_list;
    std::transform(mdb_config_filenames.begin(),
                   mdb_config_filenames.end(),
                   std::back_inserter(file_ports_list),
                   [&](const std::string &file)
                   { return std::make_pair(mdb_config_file_port, file); });
    if (!mdb_manager->Configure(file_ports_list))
    {
      std::cout << "MDB Manager Failed to parse MDB File(s)" << std::endl;
    }
  }

  // Create the Domain Manager
  arise::msw::DomainManager domain_manager(mdb_manager);

  // Initialize the Domain Manager
  domain_manager.Initialize(config_init);

  // Execute our domains using the helper function provided by the Domain Manager
  // Note that the first group is 1 not 0
  for (uint32_t i = 1; i <= domain_manager.GetNumConfiguredGroups(); i++)
  {
    std::cout << "\nExecute instantiated domains for Group " << i << std::endl;
    domain_manager.Execute(i);
  }

  // Can also retrieve the execution groups and sequence them how you see fit.
  arise::msw::DomainManager::GroupConfigurationInfo exec_group_1;
  arise::msw::DomainManager::GroupConfigurationInfo exec_group_2;
  if (domain_manager.GetNumConfiguredGroups() == 2)
  {
    domain_manager.GetExecutionGroup(1, exec_group_1);
    domain_manager.GetExecutionGroup(2, exec_group_2);
  }

  // Run Domains configured for execution group 1
  std::cout << "\nExecute instantiated domains for Group 1:" << std::endl;
  if (!exec_group_1.sequenced_dmn_instances_.empty())
  {
    for (auto const &domain : exec_group_1.sequenced_dmn_instances_)
    {
      domain.instance_->Execute(domain.task_);
    }
  }
  // Run Domains configured for execution group 2
  std::cout << "\nExecute instantiated domains for Group 2:" << std::endl;
  if (!exec_group_2.sequenced_dmn_instances_.empty())
  {
    for (auto const &domain : exec_group_2.sequenced_dmn_instances_)
    {
      domain.instance_->Execute(domain.task_);
    }
  }
  return 0;
}
```

Notes:
