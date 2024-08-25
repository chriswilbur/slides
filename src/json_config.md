JSON config file

```json [: |2|4-7|9-11|13-19|40|41-50|45,55 ]
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
        "factory_registration_key": "Sample Static Domain"
      },
      {
        "domain_name": "Sample Domain #2", "domain_id": "0xC4", "instance_id": 2, "element_id": 1,
        "tdp_filename": "../INPUT_DATA/tdp_file_sample_static_domain.txt",
        "connecting_mdb_instance_id": 1, "connecting_mdb_element_id": 1,
        "factory_registration_key": "Sample Static Domain"
      },
      {
        "domain_name": "Sample Domain #3", "domain_id": "0xC4", "instance_id": 3, "element_id": 1, 
        "tdp_filename": "../INPUT_DATA/tdp_file_sample_domain.txt",
        "connecting_mdb_instance_id": 1, "connecting_mdb_element_id": 1,
        "factory_registration_key": "Sample Static Domain"
      },
      {
        "domain_name": "Sample Domain #4", "domain_id": "0xC4", "instance_id": 4, "element_id": 1,
        "tdp_filename": "../INPUT_DATA/tdp_file_sample_static_domain.txt",
        "connecting_mdb_instance_id": 1, "connecting_mdb_element_id": 1,
        "factory_registration_key": "Sample Static Domain"
      }
    ],
  
    "execution_groups": [
      {
        "group_id": 1,
        "group_rate": 100,
        "sequence": [
          {"domain_id": 196, "instance_id": 1, "element_id": 1,  "task": 1},
          {"domain_id": 196, "instance_id": 2, "element_id": 1,  "task": 1},
          {"domain_id": 196, "instance_id": 3, "element_id": 1,  "task": 1},
          {"domain_id": 196, "instance_id": 4, "element_id": 1,  "task": 1}
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
      }
    ]
  }
```

Notes:
Slide 6.3
- Looking at our JSON file, we have 5 Properties,    
    - file_version : obvious   
    - mdb_configuration_filenames : not used here but might want to look at ad_ifs in the future   
    - domain_plugins : same as above..    
    - domain_instances : defines our domains  
    - execution_groups : groups domains w/ group #, rate, and ordered list of domains to run sequentially  
      - die = domain
      - can also specifiy domain's task 