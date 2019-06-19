# learning_mongo
Exercise files for the Learning Mongo course on Lynda

## Mongo

create image:

    docker run -d -p 27017-27019:27017-27019 --name mongodb mongo

create image with external data storing `/data/databases/mongo/db` and with access to different folred `/home/sergii/Development/projects/research/mongo/learning_mongo` mapped on `/home/learning_mongo` docker inside:

    docker run -d -p 27017-27019:27017-27019 -v /data/databases/mongo/db:/data/db -v /home/sergii/Development/projects/research/mongo/learning_mongo/:/home/learning_mongo --name mongo-db mongo

    docker ps | grep mongo
    
    docker exec -it <mongoId> bash

    :/#mongo
```
MongoDB shell version v4.0.10
connecting to: mongodb://127.0.0.1:27017/?gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("5aa30ef3-646d-409d-b1f1-ae7cdec210e3") }
MongoDB server version: 4.0.10
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
	http://docs.mongodb.org/
Questions? Try the support group
	http://groups.google.com/group/mongodb-user
Server has startup warnings: 
2019-06-17T11:56:27.034+0000 I STORAGE  [initandlisten] 
2019-06-17T11:56:27.034+0000 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
2019-06-17T11:56:27.034+0000 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
2019-06-17T11:56:27.642+0000 I CONTROL  [initandlisten] 
2019-06-17T11:56:27.642+0000 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2019-06-17T11:56:27.642+0000 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2019-06-17T11:56:27.642+0000 I CONTROL  [initandlisten] 
---
Enable MongoDB's free cloud-based monitoring service, which will then receive and display
metrics about your deployment (disk utilization, CPU, operation statistics, etc).

The monitoring data will be available on a MongoDB website with a unique URL accessible to you
and anyone you share the URL with. MongoDB may use this information to make product
improvements and to suggest MongoDB products and deployment options to you.

To enable free monitoring, run the following command: db.enableFreeMonitoring()
To permanently disable this reminder, run the following command: db.disableFreeMonitoring()
---
```
    > show dbs
    > db.people.save({ firstname: "Nic", lastname: "Raboy" })
    WriteResult({ "nInserted" : 1 })
    > db.people.save({ firstname: "Maria", lastname: "Raboy" })
    WriteResult({ "nInserted" : 1 })
    > db.people.find({ firstname: "Nic" })
    { "_id" : ObjectId("5d078059eac917e283931d8e"), "firstname" : "Nic", "lastname" : "Raboy" }
    
    > db.people.find()
    { "_id" : ObjectId("5d078059eac917e283931d8e"), "firstname" : "Nic", "lastname" : "Raboy" }
    { "_id" : ObjectId("5d078066eac917e283931d8f"), "firstname" : "Maria", "lastname" : "Raboy" }
    > db.getCollection("people").find()
    { "_id" : ObjectId("5d078059eac917e283931d8e"), "firstname" : "Nic", "lastname" : "Raboy" }
    { "_id" : ObjectId("5d078066eac917e283931d8f"), "firstname" : "Maria", "lastname" : "Raboy" }

import some collections:
```
root@0b0f810a5174:/# mongoimport --db learning_mongo --collection tours --jsonArray /home/learning_mongo/Chapter2/02_04/Finished/tours.json 
2019-06-19T16:23:26.877+0000	connected to: localhost
2019-06-19T16:23:26.924+0000	imported 29 documents
```

```
# mongo
> db.tours.find({"tourPackage":"Taste of California"})
> db.tours.find({"tourPackage":"Taste of California"}).explain("executionStats")
> db.tours.createIndex({tourPackage:1})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
> db.tours.find({"tourPackage":"Taste of California"}).explain("executionStats")

	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 4,
		"executionTimeMillis" : 1,
		"totalKeysExamined" : 4,
		"totalDocsExamined" : 4,
```
The value of `totalDocsExamined` has been updated from 29 to 4.

```
> db.tours.find( {"tourPrice":{$lte:500}, "tourLength":{$lte:3}})
> db.tours.find( {"tourPrice":{$lte:500}, "tourLength":{$lte:3}}).explain("executionStats")
> db.tours.createIndex({tourPrice:1,tourLength:1})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 2,
	"numIndexesAfter" : 3,
	"ok" : 1
}
> db.tours.find( {"tourPrice":{$lte:500}, "tourLength":{$lte:3}}).explain("executionStats")
```
`totalDocsExamined` in this improvement now is 10.

Connect to DB and search something:

    /learning_mongo/Chapter3/03_01/Finished$ node index.js

---

    learning_mongo/Chapter3/03_02/Finished$ node index.js

    http://localhost:8080/api/tours?tourPackage=Backpack%20Cal
    http://localhost:8080/api/tours/Big%20Sur%20Retreat

    $ curl -X POST -H "Content-Type:application/json" http://localhost:8080/api/tours -d "{\"tourName\":\"serhii fabulous tour3\",\"tourPackage\":\"fun in the 3sun\",\"tourPrice\":10000,\"tourLength\":5}"
    http://localhost:8080/api/tours/serhii&#101;s%20fabulous%20tour3
    http://localhost:8080/api/tours/serhii's%20fabulous%20tour


    curl -X POST -H "Content-Type:application/json" http://localhost:8080/api/tours -d "{\"tourName\":\"serhii's fabulous tour\",\"tourPackage\":\"fun in the sun\",\"tourPrice\":10000,\"tourLength\":5}"
    {"tourName":"serhii's fabulous tour","tourPackage":"fun in the sun","tourPrice":10000,"tourLength":5,"_id":"5d0a99c94530153e17c1a2e1"}
    curl -X PUT -H "Content-Type:application/json" -d "{\"tourName\":\"serhii's fabulous tour\",\"tourPackage\":\"fun in the sun\",\"tourPrice\":10000,\"tourLength\":3}" "http://localhost:8080/api/tours/serhii's%20fabulous%20tour"
    {"_id":"5d0a99c94530153e17c1a2e1","tourName":"serhii's fabulous tour","tourPackage":"fun in the sun","tourPrice":10000,"tourLength":3}

    > db.tours.createIndex({"tourName":1},{unique:true})
    {
	"createdCollectionAutomatically" : true,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
    }

```
root@0b0f810a5174:/# mongoimport --db learning_mongo --collection tours --jsonArray /home/learning_mongo/Chapter4/04_01/Start/tours_id.json 
2019-06-19T20:52:28.903+0000	connected to: localhost
2019-06-19T20:52:28.917+0000	num failures: 28
2019-06-19T20:52:28.917+0000	error inserting documents: multiple errors in bulk operation:
  - E11000 duplicate key error collection: learning_mongo.tours index: _id_ dup key: { : "Big Sur Retreat" }
  - E11000 duplicate key error collection: learning_mongo.tours index: tourName_1 dup key: { : null }

2019-06-19T20:52:28.917+0000	imported 1 document
```

    > db.tours.find({},{tourName:1})
    > db.tours.find({},{tourName:1,_id:0})
    > db.tours.find({},{tourName:1,tourPrice:1,tourLength:1,_id:0})
    > db.tours.find({},{tourName:1,tourPrice:1,tourLength:1,_id:0}).pretty()
    > db.tours.find({},{tourName:1,tourPrice:1,tourLength:1,_id:0}).pretty().sort({"tourPrice":-1})
    > db.tours.find({},{tourName:1,tourPrice:1,tourLength:1,_id:0}).pretty().sort({"tourPrice":-1}).limit(1)
    > db.tours.find({},{tourName:1,tourPrice:1,tourLength:1,_id:0}).pretty().sort({"tourPrice":-1}).limit(1).skip(20)
    > db.tours.find({tourPrice:{$lte:1000,$gte:800}})
    > db.tours.find({tourPrice:{$lte:1000,$gte:800}}).pretty()

```
> db.tours.createIndex({tourDesctiption:"text",tourBlurb:"text"})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
> db.tours.find({$text:{$search:"wine"}}).pretty()
> db.tours.find({$text:{$search:"wine"}}).pretty().sort({score:{$meta:"textScore"}})
> db.tours.find({$text:{$search:"wine"}},{score:{$meta:"textScore"}}).pretty().sort({score:{$meta:"textScore"}})
> db.tours.find({$text:{$search:"wine"}},{score:{$meta:"textScore"}}).pretty().sort({score:{$meta:"textScore"}})
> db.tours.find({$text:{$search:"wine"}},{score:{$meta:"textScore"},tourName:1,_id:0}).pretty().sort({score:{$meta:"textScore"}})
> db.tours.find({tourDescription:{$regex:/backpack/}})
> db.tours.find({tourDescription:{$regex:/backpack/}},{tourName:1})
> db.tours.find({tourDescription:{$regex:/backpack/i}},{tourName:1,_id:0})
> db.tours.find({tourDescription:/backpack/i},{tourName:1,_id:0})
```

```
root@0b0f810a5174:/# mongoimport --db learning_mongo --collection people --jsonArray /home/learning_mongo/Chapter4/04_06/Start/people.json 
2019-06-19T21:33:06.915+0000	connected to: localhost
2019-06-19T21:33:06.959+0000	imported 20 documents
root@0b0f810a5174:/# mongoimport --db learning_mongo --collection movies --jsonArray /home/learning_mongo/Chapter4/04_06/Start/movies.json 
2019-06-19T21:33:22.642+0000	connected to: localhost
2019-06-19T21:33:22.687+0000	imported 55 documents

> db.people.findOne({_id:"Brad Pitt"})
{
	"_id" : "Brad Pitt",
	"movies" : [
		"Fight Club",
		"Inglourious Basterds",
		"World War Z"
	],
	"img" : "/kc3M04QQAuZ9woUvH3Ju5T7ZqG5.jpg",
	"popularity" : 16.080298
}
> db.movies.findOne({_id:"Fight Club"})
{
	"_id" : "Fight Club",
	"overview" : "A ticking-time-bomb insomniac and a slippery soap salesman channel primal male aggression into a shocking new form of therapy. Their concept catches on, with underground \"fight clubs\" forming in every town, until an eccentric gets in the way and ignites an out-of-control spiral toward oblivion.",
	"genres" : [
		"Drama"
	],
	"img" : "/adw6Lq9FiC9zjYEpOqfq03ituwp.jpg",
	"popularity" : 6.492569
}
> db.people.find({movies:"The Avengers"})
{ "_id" : "Jeremy Renner", "movies" : [ "The Avengers", "Thor", "Avengers: Age of Ultron" ], "img" : "/g8gheNEdPSXWH5SnjfjTYWj5ziU.jpg", "popularity" : 14.416074 }
{ "_id" : "Chris Evans", "movies" : [ "The Avengers", "Captain America: The First Avenger", "Avengers: Age of Ultron" ], "img" : "/ueIzur9vURNLoqZCdfWltnpuZTq.jpg", "popularity" : 14.415341 }

> var movieName="The Avengers"
> var movieObj=db.movies.findOne({_id:movieName})
> movieObj.cast=[]
[ ]
> var personArray = db.people.find({movies:movieName})
> personArray.forEach(function(person){
... movieObj.cast.push(person)
... })
> movieObj
```
### aggregate
```
> db.tours.count({tourPackage:"Cycle California"})
3
> db.tours.aggregate([{$group:{_id:'$tourPackage',count:{$sum:1}}}])
{ "_id" : "Taste of California", "count" : 4 }
{ "_id" : "Nature Watch", "count" : 3 }
{ "_id" : "Snowboard Cali", "count" : 3 }
{ "_id" : "Kids California", "count" : 3 }
{ "_id" : "From Desert to Sea", "count" : 2 }
{ "_id" : "Backpack Cal", "count" : 5 }
{ "_id" : "Cycle California", "count" : 3 }
{ "_id" : "California Hot springs", "count" : 3 }
{ "_id" : "California Calm", "count" : 3 }
> db.tours.aggregate([{$group:{_id:'$tourOrganizer.organizerName',count:{$sum:1}}}])
{ "_id" : "Alison Birch", "count" : 4 }
{ "_id" : "Smokey Bear", "count" : 2 }
{ "_id" : "Mickey Mouse", "count" : 1 }
{ "_id" : "Tony Hawkins", "count" : 3 }
{ "_id" : "Ishmael Fisher", "count" : 4 }
{ "_id" : "John Smith", "count" : 5 }
{ "_id" : "Jack Kerouak", "count" : 1 }
{ "_id" : "Martin Jones", "count" : 4 }
{ "_id" : "Lance Armstrong", "count" : 2 }
{ "_id" : "Amber Fineway", "count" : 3 }
> db.tours.aggregate([{$group:{_id:'$tourPackage',average:{$avg:'$tourPrice'},count:{$sum:1}}}])
{ "_id" : "Taste of California", "average" : 418.75, "count" : 4 }
{ "_id" : "Nature Watch", "average" : 866.6666666666666, "count" : 3 }
{ "_id" : "Snowboard Cali", "average" : 900, "count" : 3 }
{ "_id" : "Kids California", "average" : 716.6666666666666, "count" : 3 }
{ "_id" : "From Desert to Sea", "average" : 275, "count" : 2 }
{ "_id" : "Backpack Cal", "average" : 480, "count" : 5 }
{ "_id" : "Cycle California", "average" : 3233.3333333333335, "count" : 3 }
{ "_id" : "California Hot springs", "average" : 916.6666666666666, "count" : 3 }
{ "_id" : "California Calm", "average" : 558.3333333333334, "count" : 3 }
> db.tours.aggregate([{$group:{_id:'$tourPackage',average:{$avg:'$tourPrice'},count:{$sum:1}}},{$out:'prices'}])
> db.prices.find()
{ "_id" : "Taste of California", "average" : 418.75, "count" : 4 }
{ "_id" : "Nature Watch", "average" : 866.6666666666666, "count" : 3 }
{ "_id" : "Snowboard Cali", "average" : 900, "count" : 3 }
{ "_id" : "Kids California", "average" : 716.6666666666666, "count" : 3 }
{ "_id" : "From Desert to Sea", "average" : 275, "count" : 2 }
{ "_id" : "Backpack Cal", "average" : 480, "count" : 5 }
{ "_id" : "Cycle California", "average" : 3233.3333333333335, "count" : 3 }
{ "_id" : "California Hot springs", "average" : 916.6666666666666, "count" : 3 }
{ "_id" : "California Calm", "average" : 558.3333333333334, "count" : 3 }
```
