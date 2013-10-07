Background
==========
BridgeDb is an id mapping framework which lets you:

* translate identifiers from one system to another
* search references by id or symbol
* link out to online information for an identifier 

BridgeDb is not tied to a specific source of mapping information. Instead it provides an abstraction layer
so you can switch easily between flat files, relational databases and several different web services. 

The server has been tested on Centos 5 with mysql 5.0 & apache tomcat 6 & 7.

Architecture
------------
Apache CXF is used to create the endpoints for the web service calls and for the urls behind the web application front end.
The web services are defined in `org.bridgedb.ws.server` and the web app in `org.bridgedb.uri.ws.server`.


Configuration
=============

Where are configuration files loaded from?
------------------------------------------
BridgeDB looks for the configuration files from the following locations with priority given to those at the top of the list (ie location 1 is a
higher priority than 2 etc). Once it finds a configuration file the other locations are ignored.  

1. Directly in the run directory  (Mainly for java *.jar runs)  
2. Environment Variable `OPS_IMS_CONFIG` : can be used to point to any location  
3. Tomcat configuration folder : `$CATALINA_HOME/conf/OPS-IMS`  
4. ../conf/OPS-IMS : Allows tomcat to pick up `$CATALINA_HOME/conf/OPS-IMS` even if it can not get `$CATALINA_HOME`  
5. Using classLoader getResource : This will pick up the files included in Jars and Wars.  


Configuration files
-------------------
local.properties
BridgeDB.properties  
log4j.properties  
DataSource.ttl  
lens.properties  
graph.properties

### local.properties
(There is no local properties files included)

This is the recommended place to overwrite individual property values of any other *.properties file.

local.properties will overwrite values with the same key in any other properties file.
Properties not overwritten in local will keep their original values.

To install local properties you need to.

1. Create a local.properties file  
2. Store it in a location as described above  
3. Copy the keys from the original file  

### BridgeDB.properties
(Default file is included in build and can be found in org.bridgedb.utils/resources)

This file contains the local setup information which **MUST** be configured correctly for the service to run. It is essential that the
database user, password and database name are correct.
		
You **MUST** either supply local values matching your local setup or setup your data stores to use the defaults. 
The recommened way to overwrite properties is to add a property with the exact (case sensitive) key to local.properties

Database Dependency
-------------------
MySQL version 5 or above **MUST** be installed and running  
MySQL databases and users **MUST** be created with CREATE, DROP, INDEX, INSERT, ALTER,
UPDATE, DELETE, and SELECT permissions.

Consult the BridgeDB.properties file for the defaults, or copy and amend the configuration file
to reflect your own setup.

If you are using the default mysql accounts and databases then execute the file 
mysqlConfig.sql from the BridgeDB root directory which will configure your local mysql with the BridgeDB defaults

	mysql -u root -p < mysqlConfig.sql

Note that the sql script will fail, without reverting changes made up to the 
point of failure, if any of the user accounts or databases already exist.

RDF Repository and Transitive Directory Dependency
-------------------------
BridgeDB uses OpenRDF Sesame RDF engine and this is included automatically via maven.  
**WARNING**: All directories **MUST** exists and the (linux) user running tomcat **MUST** have READ/WRITE permission set!
Some of the OpenRDF error message are unclear if this is not the case.

See BridgeDB.properties and change the appropriate property to point to the correct directory.
A Sesame SailNativeStore(s) will be created automatically as long as the loader can create/find the directory,

We recommend changing the relative directories to absolute directories.
Please ensure the parent directories exist and have the correct permissions. 

The settings for testing (and therefore compilation) can be left as is as long as the testing user would have permission to create and delete files there.

The BaseURI variable is no longer used but may be in the future so is worth setting correctly.

Other Configuration files
-------------------------
### log4j.properties
(Default file is included in build and can be found in org.bridgedb.utils/resources)

Edit this to change the logger setup.
The default can be found in the Utils Resource directory
Please refer to the log4j documentation for more information.

### DataSource.ttl 
(Included in the build and found at org.bridgedb.rdf/resources)

RDF format of all the BridgeDB DataSource(s) and Registered UriPatterns,
Found in $BRIDGEDB_HOME/org.bridgedb.rdf/resources

This file defines all the URI patterns that will match every BridgeDB DataSource.
Warning: As additional UriPatterns are constantly being found and created this file is subject to continuous updates. 
Having a local DataSource.ttl is therefore highly discouraged as it will block future updates being discovered.
Instead please push any changes into the version inside the source code. 
This file is **NOT** effected by local.properties and 
you cannot change existing or add additional datasource URI patterns through local.properties. 
If you require local additions that should not become general usage (such as commercial uriPatterns)
then the suggested approach is for you to change the code to use multiple dataSource files.

### DataSource.owl
(Found at org.bridgedb.rdf/resources)

Ontology of the datasource file. 
Included for reference only and may or may not be updated to reflect current state.

### lens.properties
(Included in the build and found at org.bridgedb.uri.sql\resources)

This file defines the lenses to be used in the system.
See [Scientific Lenses over Linked Data](http://ceur-ws.org/Vol-951/paper5.pdf)
for more information on what lenses are.

Can and should be added to using local.properties

**WARNING**: As the Lens work is still evolving it is subject to alterations and the format of this file could be changed at any time.
Having a local lens.properties is highly discouraged as it will block future updates being discovered.
Instead please push any changes into the version inside the source code.

Local additions that should not become general usage (such as commercial lens) can be added to the local.properties file.

Note: the fourth part of the key  
`lens.lenkey.justification.***`  
only serves to keep the keys unique and can have any value.
If extending a key we suggest using `local**` as the fourth part of the justification key to ensure not overwriting general additions.

### graph.properties
(Included in the build and found at org.bridgedb.uri.sql\resources)

This file maps RDF Graphs/Context with the UriPatterns found in that graph.
This allows Map functions to supply a graph name rather than a list of targetUriPatterns

Data in the included file is OpenPHACTS specific.

Data Loading
============
All tests should load their required data at the start of the tests.
To load the test data into the live sql use the method SetupWithTestData in the URI loader package.
The [IMS project](https://github.com/openphacts/IdentityMappingService) also has a data loader which should be used
if the IMS is the deployed project.

Compilation
===========

If you've obtained the source code of BridgeDb, you should be
able to compile with a simple: 

	mvn clean install
	
Note that for the maven build to run all tests: 
1. The MySQL database **MUST** be running and configured as above.
2. (Optional) http://localhost:8080/OPS-IMS to be running the war created by the URI webserver Server module,
   with test data which can be loaded using the class SetupWithTestData in the URI Loader module.
   Maven will skip the client tests if the localhost server is not found.
	
OPS Webservice Setup.
--------------------

Make sure your local.properties file matches:
* The SQL databases included user names and password
* The rdf parent directories are setup (and accessible) as above.

or you have set up the default databases etc from BridgeDB.properties

Deploy $BridgeDb/org.bridgedb.uri.ws.service/target/org.bridgedb.uri.ws.server-*.war to something like your local
tomcata webapps directory
To setup databases and add test data run org.bridgedb.uri.loader.SetupLoaderWithTestData. The easiest way is within eclipse since you
can set the OPS_IMS_CONFIG environment variable within the run configuration, Netbeans unfortunately does not allow environment variables to be set
within the IDE.
(Optional) Deploy $BridgeDb/org.bridgedb.ws.service/target/BridgeDb.war
   Both wars share the same SQL data.


Note: If Installing the OpenPhacts IMS and or the OpenPhacts QueryExpander the org.bridgedb.uri.ws.server-*.war should not be deployed but
instead the war appropriate to the other project should be deployed. See the readme within the
other projects for more details.

Contact
=======

For OpenPhacts specific please contact Christian and use the OPS Jira.

For geneneral BridgeDB:
Website, wiki and bug tracker: http://www.bridgedb.org
Mailing list: http://groups.google.com/group/bridgedb-discuss/
Source code can be obtained from http://svn.bigcat.unimaas.nl/bridgedb

Authors
=======

BridgeDb and related tools were originally developed by:

Jianjiong Gao
Isaac Ho
Martijn van Iersel
Alex Pico

And extended by the OpenPHACTS BridgeDB Team:
Christian Brenninkmeijer
Alasdair Gray
Egon Willighagen
Ian Dunlop

License
-------

BridgeDb is free and open source. It is available under
the conditions of the Apache 2.0 License. 
See License-2.0.txt for details.
