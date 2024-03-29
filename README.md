# omam-troubleshooting
## Summary
Skeleton helper project to assist in troublehsooting issues with ansible playbooks.

## Project Structure 
```
│   readme.md
│
└───omam-troubleshooting
    │   inventory.ini                       # Inventory File :: add endpoints under group
    │
    ├───group_vars
    │       all.yml                         # Global Group Vars :: change ansible_python_interpreter to virtual environment 
    │       okc.yml                         # Group Specific Vars :: add specific group vars 
    │       rr.yml                          # Group Specific Vars :: add specific group vars 
    |
    |───example_args.json                   # Used to exercise module code locally :: edit module specific 
    |
    |───venv                                # python virtual environment :: virtualenv venv
    |   ├── bin
    |       ├── activate
    |       ├── python -> /usr/bin/python3  # Path to virtual env python 
    |
    |──────lib
            └── python3.8
                └── site-packages
                    ├── ansible_collections
                        └── dellemc
                            └── openmanage 
                                └── plugins
                                    ├── modules         # code  
                                    ├── module_utils    # code
```

## Environment Setup 
Clone this repository

```git clone https://github.com/00-eight/omam-troubleshooting.git```

Connect IDE to the remote system. 
+ Install https://code.visualstudio.com/download/
+ Add extention  *Remote - SSH*
+ Update selected .shh/config file to contain proper user name 
```
Host 192.168.0.120
  HostName 192.168.0.120
  User root
```
+ Open the project folder */path/to/thisrepository*
+ launch terminal and cd into this repository 

Create the virtual environment 

```python3 -m virtualenv venv```

Activate the virtual environment 

```source venv/bin/activate```

Install ansible into the virtual environment 

```pip install ansible or pip install ansible --proxy=http://proxy:port```

Install OMAM modules and any other python packages omsdk that are needed. Reference the official docs https://github.com/dell/dellemc-openmanage-ansible-modules#installation

```ansible-galaxy collection install dellemc-openmanage-x.tar.gz -p /<path_to>/venv/lib/<python_ver>/site-packages/ansible_collections```

Modify the Inventory and Group Vars
+ *group_vars/all.yml* update with path to venv python
+ *group_vars/name.yml* update with group/module specific vars
+ *inventory.ini* update with target systems

## Techniques 
### Executing Module Code Locally
Allow use of normal python debugger - PDB to break during execution. 
+ Add breakpoints into the module pdb.set_trace() 
+ Create an args.json file with the specific module arguments 
+ Execute the module by calling it

    ```python -m ansible_collections.dellemc.openmanage.plugins.modules.ome_smart_fabric args.json```

### Add logging 
Add standard python logging to ansible module to log messages to console and file.  
+ Add logging to the module using normal python logging module 
```
import logging
logging.basicConfig(level=logging.DEBUG,
  format="%(asctime)s %(levelname)s %(thread)d %(funcName)s %(message)s",
  handlers=[
    logging.StreamHandler(),
    logging.FileHandler(filename='/tmp/ansible_debug.log')
  ])
```
+ Add logging messages where relavant 

    ```logging.debug("something: %r" % variable)```

+ Execute the module by calling it via ansible-playbook

    ```ansible-playbook -i inventory.ini play.yml```