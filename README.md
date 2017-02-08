### A little on MongoDB and how a Java application and MongoDB can be put together ###

MongoDB is a NoSQL "document orineted" "schema less" database that is aimed to achieve hign performance in large distribted dataset and easy to scale. This works based on concept of "collection" and "document". If NoSQL database is compared with traditional RDBMS then "collection" in NoSQL coresponsds to "table" in RDBMS and "document" in NoSQL corresponds to "row/tupple" in RDBMS.

###Collection###
It is a set of documents and do not enforce schema. Documents in collection can have different fields unlike RDBMS table which enforces a schema.

###Document###
It is a set of key value pairs where in the data is kept in json format.

###How to install###
    Download binary from https://www.mongodb.com/download-center
    Install it
    As a result of install, In windows 64- you will see Mongo home directory C:\Program Files\MongoDB\Server\3.4
    Let's call Mongo_Home for easy recall.

###How to start###
    Navigate to Mongo_Home/bin directory
    Run mongod.exe without argument. This method expects the directory "C:\data\db" to be created else the MongoDB server will not start. The default port of MongoDB is 27017.
    Or
    Run mongod.exe --dbpath <file system path> --port <port number> for example mongod.exe --dbpath C:\data\mongo\master --port 27017
    
###How to connect to MongoDB###
MongoDB binary also contains a command line tool - that is a client which can be used to connect to MongoDB server.
    
    Navigate to Mongo_Home/bin directory
    Run mongo.exe

###Frequent MongoDB commands###
Run MongoDB client (shell)
    
    db.help()   to see list of commands
    db.stats()  MongoDB server stats
    use <db_name>   To create or switch database
    db  to check current database
    show dbs    list databases
    db.dropDatabase()   To drop database
    db.createCollection(name, options)  to create collection
    db.<collection_name>.insert(json)   also creates a collection and put document in it
    show collections    shows collections present
    db.<collection_name>.drop()    drop collection
    db.COLLECTION_NAME.insert(document) To insert a document
    db.COLLECTION_NAME.find()   To query data from MongoDB collection
    db.COLLECTION_NAME.find().pretty()  results in a formatted way
    db.COLLECTION_NAME.findOne()    returns only one document
    db.COLLECTION_NAME.update(SELECTION_CRITERIA, UPDATED_DATA)     To update
    db.COLLECTION_NAME.save(document)   To save
    db.COLLECTION_NAME.remove(DELLETION_CRITTERIA)  To remove
    db.COLLECTION_NAME.ensureIndex({KEY:1})     To create an index
    db.COLLECTION_NAME.aggregate(AGGREGATE_OPERATION)   
    
###MongoDB Replication###
Replication is the process of synchronizing data across multiple servers. This helps achieve high availability and recover data from single point of failure.

MongoDB uses replica set to achieve replication across multiple servers. A replica set is a group of mongodb instances forming cluster of nodes. one of the nodes in replica set beocmes primary where all write operation goes to where as the remaining nodes become secondry and handle read operations.

How to setup replica set
Multiple mongodb instances can be run on the same host but on different port and data directory.

        Navigate to MongoDB_Home
        mongod.exe --dbpath C:\data\mongo\replicaset\m0 --port 54321 --replSet rs0
        mongod.exe --dbpath C:\data\mongo\replicaset\s1 --port 54322 --replSet rs0
        mongod.exe --dbpath C:\data\mongo\replicaset\s2 --port 54323 --replSet rs0
        Connect to master node from mongo client mongo --port 54321
        run it from master node rs.initiate()
        Add nodes in replica set
        rs.add("hostname:54322")
        rs.add("hostname:54323")
        rs.conf() and rs.status() for metadata
        
        
###MongoDB Sharding###

###MongoDB Security###
    
### MongoDB and Java###

###MongoDB Document Java Pojo Mapping ODM###

###Use case###



