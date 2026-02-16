## From Yoda to Storm

### Data access password
In order to get the data from yoda into storm, you need to create a data access password. 
Basically, you go to the yoda webpage (on your local computer) and find “Create Data Access Password” 
in the dropdown menu. Make sure to write the password down as it cannot be retrieved anymore.

For more information on the data access password, visit this [link](https://servicedesk.surf.nl/wiki/spaces/WIKI/pages/56295623/How+to+get+a+Data+Access+Password)

### Storm
Once you've created a data access password, you open storm. Do this using the x2go app,
since you need the interface to operate ibridges.

**Step 1**: Within storm, open the terminal and create a yoda_ibridges directory in a
directory of your choice:
```
mkdir yoda_ibridges
cd yoda_ibridges/
```

**Step 2**: Create a python vertual environment (venv), activate it and install ibridges.
- In order for the venv to work later on, you need to have qt. You can install this via
```
brew install qt #only first time
```
If this gives you an error, you probably need to install brew as well before you can do this.

- The code to create the virtual environment:
```
python -m venv venv_yoda #only first time
```

- The code to activate it **(do this each time you want to get access to ibridges)**:
```
source venv_yoda/bin/activate
```
You should now see the activated venv before the prompt, like 
#(venv_yoda) ~ /Current/working/dir

- Finally, you can install the ibridges gui:
```
pip install ibridgesgui #only first time
```

**Step 3**: Open the ibridges gui
```
ibridges gui
```

### The iBridges gui
In the ibridges interface, go to `Configure` in the top menu, and add a configuration (the first
time you use it). You will need to specify some of the parameters:
```
- irods_host = aumc-yoda.irods.surfsara.nl
- irods_home = /yoda/home
- irods_user_name = YOUR_YODA_USERNAME. In my case this is my amc email adress.
- irods_zone_name = yoda
- Irods_default_resource = irodsResc
- Irods_identification_scheme = pam_password
```
Note that these are the login entries for WP3. For WP1 & 2, these can be found on the [yoda
documentation site](https://servicedesk.surf.nl/wiki/spaces/WIKI/pages/19824798/Data+handling+in+Yoda+iRODS+using+iCommands/)
If the configuration worked, you should see a `irods_environment.json` and `ibridges_config.json` file
in your home directory. 

After you have created the configuration files you can connect to irods by clicking 
`Connect -> Connect to IRODS`. Here, you enter the password you created at the beginning
of this tutorial. Then, a data transfer interface will pop up. Go to the `Synchronize Data`
tab to select the folders you want to synchronize.

### Leonardo's github:
https://github.com/leonardocerliani/GUTS_fmri_preproc/tree/main/TUT/07_Yoda_iBridges_data_transfer


