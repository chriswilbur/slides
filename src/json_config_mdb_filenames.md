JSON config file

```json
{
    "file_version" : "1.0.0",
  
    "mdb_configuration_filenames" : [
      "not_used_in_this_example_1.json",
      "not_used_in_this_example_2.json"
    ],
    
    "domain_plugins": [
      {}
    ],
  
    "domain_instances": [
      {
        "domain_name": "Sample Domain #1", "domain_id": "0xC4", "instance_id": 1, "element_id": 1, 
        "tdp_filename": "../INPUT_DATA/tdp_file_sample_domain.txt",
        "connecting_mdb_instance_id": 1, "connecting_mdb_element_id": 1,
        "factory_registration_key": "My Sample Static Domain"
      },
      {
        "domain_name": "Sample Domain #2", "domain_id": "0xC4", "instance_id": 2, "element_id": 1,
        "tdp_filename": "../INPUT_DATA/tdp_file_sample_static_domain.txt",
        "connecting_mdb_instance_id": 1, "connecting_mdb_element_id": 1,
        "factory_registration_key": "My Sample Static Domain"
      },
      {
        "domain_name": "Sample Domain #3", "domain_id": "0xC4", "instance_id": 3, "element_id": 1, 
        "tdp_filename": "../INPUT_DATA/tdp_file_sample_domain.txt",
        "connecting_mdb_instance_id": 1, "connecting_mdb_element_id": 1,
        "factory_registration_key": "My Sample Static Domain"
      },
      {
        "domain_name": "Sample Domain #4", "domain_id": "0xC4", "instance_id": 4, "element_id": 1,
        "tdp_filename": "../INPUT_DATA/tdp_file_sample_static_domain.txt",
        "connecting_mdb_instance_id": 1, "connecting_mdb_element_id": 1,
        "factory_registration_key": "My Sample Static Domain"
      },
      {
        "domain_name": "My Test Domain #5", "domain_id": "0xC4", "instance_id": 5, "element_id": 1,
        "tdp_filename": "../INPUT_DATA/tdp_file_sample_domain.txt",
        "connecting_mdb_instance_id": 1,"connecting_mdb_element_id": 1,
        "factory_registration_key": "My Test Domain"
      },
      {
        "domain_name": "My Test Domain #6", "domain_id": "0xC4", "instance_id": 6, "element_id": 1,
        "tdp_filename": "../INPUT_DATA/tdp_file_sample_static_domain.txt",
        "connecting_mdb_instance_id": 1, "connecting_mdb_element_id": 1,
        "factory_registration_key": "My Test Domain"
      }
    ],
  
    "execution_groups": [
      {
        "group_id": 1,
        "group_rate": 100,
        "sequence": [
          {"domain_id": 196, "instance_id": 1, "element_id": 1},
          {"domain_id": 196, "instance_id": 2, "element_id": 1},
          {"domain_id": 196, "instance_id": 3, "element_id": 1},
          {"domain_id": 196, "instance_id": 4, "element_id": 1}
        ]
      },
      {
        "group_id": 2,
        "group_rate": 50,
        "sequence": [
          {"domain_id": 196, "instance_id": 1, "element_id": 1,  "task": 2},
          {"domain_id": 196, "instance_id": 2, "element_id": 1,  "task": 2},
          {"domain_id": 196, "instance_id": 3, "element_id": 1,  "task": 2},
          {"domain_id": 196, "instance_id": 4, "element_id": 1,  "task": 2}
        ]
      },
      {
        "group_id": 3,
        "group_rate": 25,
        "sequence": [
          {"domain_id": 196, "instance_id": 5, "element_id": 1,  "task": 1},
          {"domain_id": 196, "instance_id": 6, "element_id": 1,  "task": 1}
        ]
      }
    ]
  }
```
