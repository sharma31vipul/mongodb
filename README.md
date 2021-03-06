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
Sharding is the process of distributing data across multiple servers.

Why Sharding
Vertical scaling requires increase in the capacity of the server in terms of CPU RAM etc which is quite expensive where as Horizontal Scaling involves dividing the dataset and load over multiple servers, adding additional servers to increase capacity as required.

MongoDB supports horizontal scaling through sharding.
A MongoDB sharded cluster consists of the following components:

        shard: Each shard contains a subset of the sharded data. Each shard can be deployed as a replica set.
        mongos: The mongos acts as a query router, providing an interface between client applications and the sharded cluster.
        config servers: Config servers store metadata and configuration settings for the cluster. As of MongoDB 3.4, config servers must be deployed as a replica set (CSRS).

MongoDB shards data at the collection level, distributing the collection data across the shards in the cluster. 

Shard Key
To distribute the documents in a collection, MongoDB partitions the collection using the shard key. The shard key consists of an immutable field or fields that exist in every document in the target collection. A sharded collection can have only one shard key.

Chunks
MongoDB partitions sharded data into chunks. Each chunk has an inclusive lower and exclusive upper range based on the shard key.

###MongoDB Security###

Enabling Authentication
With access control enabled, ensure you have a user with userAdmin or userAdminAnyDatabase role in the admin database. This user can administrate user and roles such as: create users, grant or revoke roles from users, and create or modify customs roles.

        Run mongoDB instance without access control
        Connect to DB (mongo shell)
        Create User
      
        use admin
        db.createUser(
            {
                user: "dbadmin",
                pwd: "admin123",
                roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
            }
        )
       
        Restart mongod with access control
        mongod --auth --port 27017 --dbpath /data/db1
        Connect and authenticate as the user administrator from mongo shell
        mongo --port 27017 -u "dbadmin" -p "admin123" --authenticationDatabase "admin"
        or
        use admin
        db.auth("dbadmin", "admin123" )
        
        For built-in roles https://docs.mongodb.com/manual/core/security-built-in-roles/
        For user defined roles https://docs.mongodb.com/manual/core/security-user-defined-roles/
        
        
    
### MongoDB and Java###
MongoDB JDBC driver is needed for any java application to connect to MongoDB.
Maven based projects need to include the below dependency in pom.xml
```xml
<dependency>
			<groupId>org.mongodb</groupId>
			<artifactId>mongo-java-driver</artifactId>
			<version>3.4.2</version>
		</dependency>
```

A few APIs
To connect to MongoDB
```java
public void connect() {
 String mongoHost = "mongodb://localhost:27017";
 MongoClient mclient = new MongoClient(mongoHost);
}
```
To connect database
```java
public void connectDB(String dbName) {
 DB database = mclient.getDB(dbName);
}
```
To Authenticate
```java
public void authenticate(DB database) {
 database.authenticate("userName", "password");
}
```
A few operations

	DBCollection collection = db.getCollection("collection_name")	// collection instance
	DBObject document = new BasicDBObject(); 	// basic document
	document.put("key1", "value1");		// record in document
	document.put("key2", "value2");		// record in document
	collection.insert(document);		// To insert document into collection
	
	DBObject doc = collection.findOne();	// To read
	DBCursor cursor = collection.find();
	for(DBObject doc: cursor) { }
	DBObject criteria = new BasicDBObject("age", age);
	// matching operator {"age": age}
	DBCursor cursor = collection.find(criteria);
	
	//Read and Filter
	DBObject criteria = new BasicDBObject("name", name);
	DBObject fields = new DBObject("address", 1);		// 1 for ON include
	fields.put("_id", 0);		// exclude _id	0 for OFF
	DBObject document = collection.findOne(criteria, fields);
	
Refer MongoDB Java doc for all APIs
http://api.mongodb.com/java/current/


###MongoDB Document Java Pojo Mapping ODM Object Document Mapper###
Just like Hibernate (ORM) that does object to relational mapping, In mongoDB there are various tools that provide 'ODM' power and 'Morphia' is one of the tools.

For using Morphia, the class which has to be persisted to the MongoDB has to be annotated with @Entity and if you have an “id” attribute in the class, then it has to be annotated by @Id.

Add morphia dependency into the application.
```java
import java.util.Date;
import org.bson.types.ObjectId;
import com.google.code.morphia.annotations.Entity;
import com.google.code.morphia.annotations.Id;

@Entity
public class Sample {

  private String task;
 
  @Id private ObjectId id;

  public Sample(String task,
              ObjectId id){
    this.task = task;
    this.setId(id);
  }

  public Todo(String task){
    this.task = task;
  }

  public String getTask() {
    return task;
  }
  public void setTask(String task) {
    this.task = task;
  }

  public ObjectId getId() {
    return id;
  }

  public void setId(ObjectId id) {
    this.id = id;
  }
}

//Using Morphia
Mongo mongo = new Mongo();
Morphia morphia = new Morphia();
morphia.map(Sample.class);
Datastore ds = morphia.createDatastore(mongo, DBNAME);
ds.save(sampleObject);
```

###Use case###
Since MongoDB schemas are flexible that do not need to have the same set of fields or structure. Common fields in a collection’s documents may hold different types of data.
Designing schema in MongoDB very much depends on 
	
	requirements, 
	what all objects with their respective roles, 
	their connectivity with each other and 
	how objects will collaborate with each other.

Suppose a client needs a database design for his blog/website and see the differences between RDBMS and MongoDB schema design. Website has the following requirements.

	Every post has the unique title, description and url.
	Every post can have one or more tags.
	Every post has the name of its publisher and total number of likes.
	Every post has comments given by users along with their name, message, data-time and likes.
	On each post, there can be zero or more comments.

RDBMS Schema
Classes and their relation 
	
	Post [id, title, desc, url, likes, post_by]
	Comment [comment_id, post_id, by_user, message, data_time, likes]
	TagList [id, post_id, tag]


MongoDB Schema

```json
{
   _id: POST_ID
   title: TITLE_OF_POST, 
   description: POST_DESCRIPTION,
   by: POST_BY,
   url: URL_OF_POST,
   tags: [TAG1, TAG2, TAG3],
   likes: TOTAL_LIKES, 
   comments: [	
      {
         user:'COMMENT_BY',
         message: TEXT,
         dateCreated: DATE_TIME,
         like: LIKES 
      },
      {
         user:'COMMENT_BY',
         message: TEXT,
         dateCreated: DATE_TIME,
         like: LIKES
      }
   ]
}

```
So while showing the data, in RDBMS you need to join three tables and in MongoDB, data will be shown from one collection only.


