====================================
Initial DNS Seedlist Discovery tests
====================================

This directory contains platform-independent tests that drivers can use
to prove their conformance to the Initial DNS Seedlist Discovery spec.

Test Setup
----------

Start a three-node replica set on localhost, on ports 27017, 27018, and 27019,
with replica set name "repl0".

To run the tests that accompany this spec, you need to configure the SRV and
TXT records with a real name server. The following records are required for
these tests::

  Record                                    TTL    Class   Port   Target
  _mongodb._tcp.test1.test.build.10gen.cc.  86400  IN SRV  27017  localhost
  _mongodb._tcp.test1.test.build.10gen.cc.  86400  IN SRV  27018  localhost
  _mongodb._tcp.test2.test.build.10gen.cc.  86400  IN SRV  27018  localhost
  _mongodb._tcp.test2.test.build.10gen.cc.  86400  IN SRV  27019  localhost
  _mongodb._tcp.test3.test.build.10gen.cc.  86400  IN SRV  27017  localhost
  _mongodb._tcp.test5.test.build.10gen.cc.  86400  IN SRV  27017  localhost
  _mongodb._tcp.test6.test.build.10gen.cc.  86400  IN SRV  27017  localhost

  Record                                    TTL    Class   Text
  test5.test.build.10gen.cc.                86400  IN TXT  "connectTimeoutMS=300000&socketTimeoutMS=300000"
  test6.test.build.10gen.cc.                86400  IN TXT  "connectTimeoutMS=200000"
  test6.test.build.10gen.cc.                86400  IN TXT  "socketTimeoutMS=200000"

You need to adapt the records shown above to replace ``build.10gen.cc`` with
your own domain name, and update the "uri" field in the YAML or JSON files in
this directory with the actual domain.

Test Format and Use
-------------------

These YAML and JSON files contain the following fields:

- ``uri``: a mongodb+srv connection string
- ``seeds``: the expected set of initial seeds discovered from the SRV record
- ``hosts``: the discovered topology's list of hosts once SDAM completes a scan
- ``options``: the parsed connection string options as discovered from URI and
  TXT records

For each file, create MongoClient initialized with the mongodb+srv connection
string. You SHOULD verify that the client's initial seed list matches the list of
seeds. You MUST verify that the set of ServerDescriptions in the client's
TopologyDescription eventually matches the list of hosts. You MUST verify that
the set of Connection String Options matches the client's parsed set.
