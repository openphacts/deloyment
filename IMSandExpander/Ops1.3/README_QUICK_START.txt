version 1.3 Install and run instructions.

Full Readme files are included and recommeneded reading.
Property files mentioned there and included in jar and war have been copied to properties folder.

===============
Config Critical
--------------
Change the *** set me *** values in local properties. (found in the example folder)
Save as explained in BridgeDB_README.md

1. SQL user name and password
1a. Create th database as per mysqlConfig.sql

2. Create RDF and Directories with read write permissions!

3. All the *** optional set me *** should not be needed but set and uncomment if desired,

====
Load 
----

Optional:
Copy files from
http://openphacts.cs.man.ac.uk/ims/linkset/version1.3
And place in a local folder
Then set the #PathToFile properties an explained in IMS_README.md
You will still need access to http://openphacts.cs.man.ac.uk/ims/linkset/version1.3 to read directory information.

Run:
java -jar loader.jar 

====
HACK
----
Run sql commands found in EndofLoadHack.txt

====
Run
---
Deploy QueryExpander.war