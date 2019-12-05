Neo4J
=============

Installation
-----------------

There is desktop version(but very big) and also tar version.

Now we installed a tar version in centOS http://backup.saraqian.cn:7474/

- Path: /usr/share/neo*
- Setup: /etc/init.d/neo4j for service and neo4j_ctl for auto restart during server reboot



Cypher command
------------------

Basic Command
``````````````````
- Insert: CREATE (ee:Person { name: "Emil", from: "Sweden", klout: 99 })
- Search: MATCH (ee:Person) WHERE ee.name = "Emil" RETURN ee;
- MATCH (ee:Person)-[:KNOWS]-(friends) WHERE ee.name = "Emil" RETURN ee, friends
- MATCH (js:Person)-[:KNOWS]-()-[:KNOWS]-(surfer) WHERE js.name = "Johan" AND surfer.hobby = "surfing" RETURN DISTINCT surfer

**Batch operation:**

.. code-block::

  MATCH (ee:Person) WHERE ee.name = "Emil"
  CREATE (js:Person { name: "Johan", from: "Sweden", learn: "surfing" }),
  (ir:Person { name: "Ian", from: "England", title: "author" }),
  (rvb:Person { name: "Rik", from: "Belgium", pet: "Orval" }),
  (ally:Person { name: "Allison", from: "California", hobby: "surfing" }),
  
  (ee)-[:KNOWS {since: 2001}]->(js),(ee)-[:KNOWS {rating: 5}]->(ir),
  (js)-[:KNOWS]->(ir),(js)-[:KNOWS]->(rvb),
  (ir)-[:KNOWS]->(js),(ir)-[:KNOWS]->(ally),
  (rvb)-[:KNOWS]->(ally)


**Using profile/explain to see how the Cypher works: **
- PROFILE MATCH (js:Person)-[:KNOWS]-()-[:KNOWS]-(surfer) WHERE js.name = "Johan" AND surfer.hobby = "surfing" RETURN DISTINCT surfer



