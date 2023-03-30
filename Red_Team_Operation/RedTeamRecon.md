# Red Team Recon

## [Recon-ng](https://github.com/lanmaster53/recon-ng)

### Creating a Workspace
`workspaces create WORKSPACE_NAME`  
`recon-ng -w WORKSPACE_NAME`: starts recon-ng with the specific workspace  

### Seeding the Database
`db insert domains`  

### Recon-ng Marketplace
- `marketplace search KEYWORD` to search for available modules with keyword.
- `marketplace info MODULE` to provide information about the module in question.
- `marketplace install MODULE` to install the specified module into Recon-ng.
- `marketplace remove MODULE` to uninstall the specified module.  

### Working with Installed Modules

- `modules search` to get a list of all the installed modules
- `modules load MODULE` to load a specific module to memory  
- `options list` to list the options that we can set for the loaded module.
- `options set <option> <value>` to set the value of the option.

### Keys
- `keys list` lists the keys
- `keys add KEY_NAME KEY_VALUE` adds a key
- `keys remove KEY_NAME` removes a key

### Runs
- `modules load MODULE` loads an installed module
- `CTRL + C` unloads the module.
- `info` to review the loaded module’s info.
- `options list` lists available options for the chosen module.
- `options set NAME VALUE`
- `run` to execute the loaded module.


