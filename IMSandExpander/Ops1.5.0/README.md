# IMS 2.0.2 for Open PHACTS 1.5.0


New IMS WAR file here:

https://github.com/openphacts/queryExpander/releases/tag/2.0.2

Rename to `QueryExpander.war` before deploying


Also eventually deployed here (mysql loading is taking quite a while on that machine for some reason):

http://openphacts.cs.man.ac.uk:9095/QueryExpander/


mySQL dump (as mentioned earlier)

http://data.openphacts.org/dev/ims/


which was generated from
http://openphacts.cs.man.ac.uk/ims/dev/version1.5.0-SNAPSHOT/
(copied to:
http://openphacts.cs.man.ac.uk/ims/linkset/version1.5.0/)

I'll put on data.openphacts.org as well if folks confirm we are happy with this.


Configure in `conf/BridgeDb/local.properties` the usual stuff to refer to the new database.



If loaded correctly, http://localhost:8080/QueryExpander/ (or equivalent) should say::


> **Statisitics**
> 31,382,458 Mappings
> 186 Mapping Sets
> 50 Source Data Sources
> 4 Predicates
> 50 Target Data Sources
> 33 Lenses

