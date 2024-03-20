#### MongoDB Tut:

##### 1. BSON - Binary representation of JSON. (convert json data to binary during run time to execute faster.)

![Mysql vs Mongodb](https://ik.imagekit.io/ojhpetjpw/mongodb/1.png?updatedAt=1698133295064)

Note:

- **100 level** of nesting is possible in mongodb
- maximum size of any document to be inserted is **16 mb**

#### CRUD Operation in MongoDB:

##### Create:

- insertOne(data, options)
- insertMany(data, options)

##### Read:

- find(filter, options)
- findOne(filter, options) {returns first element which is match and inserted first in db}

##### Update:

- updateOne(filter,data, options)
- updateMany(filter,data, options)
- relace(filter,data, options)

##### Delete:

- deleteOne(data, options)
- deleteMany(data, options)

#### Data types in mongodb:

1. Text
2. Boolean
3. Number (Integer, NumberLong, NumberDecimal)
4. ObjectId
5. IsoDate
6. TimeStamp
7. Array
8. Emb document

#### How to delete Db and collection:

- db.drop() - this will delete entire database
- db.collection_Name.drop() this will delete all the collection but not the db itself (to use this first you need to be inside in db. )

**Note**:

- Ordered option in insertmany - insert all correct doc even if an error occur at particular position.

###### To create only collection:

- db.createCollection("collection_name")

**Notes**:

- see journoul google (during power cut data save inside journel not in db to know where it is left with)

- if you don't want aknowledge then you can set it false.

- just do google **options in insertMany()**.

#### Atomicity in mongoDB:

- Atomicity is achieved in mongodb at document level meaning that if we are inserting 5 document and during 3 the power get off. then if 3 is inserted completely then upto 3 will be stored in db but 4 and 5 will not.
- But here not happens like inserting half of doc and rest not

###### - How to import json in mongodb:

### Comparison Operator in mongodb:

- **$e**
- **$ne**
- **$lt**
- **$gt**
- **$lte**
- **$gte**
- **$in** - this takes an **Array**
- **$nin** - this takes an **Array**

#### Logical Operator in mongodb:

- **$and** - this takes an **Array**
- **$or** - this takes an **Array**
- **$not**
- **$nor**

#### Evaluation Operation in mongodb:

- **$expr**
- **$jsonSchema**
- **$mod**
- **$regex**
- **$text** -- we use it to create index
- **$where**

--> Just do google Evaluation operator in mongodb

#### Some others:

- **exists**
- **type** -- some predefined type have ex- boolean -8, string 2 , etc;
- **elemMatch** - used to find single elem inside array.

```mongodb
db.collection.find({hasMac: {$exists: true, $type: 8}})
```

**Notes**:

- now suppose we have to query something insede an array.

```mongodb
{
    name: "sanjeev",
    likes: ["cricket", "football", "volleyball"],
    experience: [
        {
            company: "amazon",
            duration: 2.5
        }
    ]
}

<!-- Now query to find its company name. -->
db.collection.find({"experience.company": "amazon"})
<!-- Note here we need to pass in "" -->
```

- we can apply more than one field to sort a doc
  for ex-

```mongodb
db.collection.find({}).sort({name: 1, age: 1})
```

#### Update cmd:

- $set
- $inc:
- $max: - it will set to max value passed
- $min: - it will set to min value passed
- $mull:
- $rename:
- $upsert: - if match then nothing else insert it into doc

'
Q. find all the candidate who has experience lte 1 and add a field inside it as neglect true.

```mongodb
db.students.updateMany({experience: {$elemMatch: {duration: {$lte: 1}}}, {$set: {"experience.$.neglect": true}}})
```

- here $ is used to select first field (we have option to select first, all match , or all available field.)

- for first match

```mongodb
db.students.updateMany({experience: {$elemMatch: {duration: {$lte: 1}}}, {$set: {"experience.$.neglect": true}}})
```

- for all

```mongodb
db.students.updateMany({experience: {$elemMatch: {duration: {$lte: 1}}}, {$set: {"experience.$[].neglect": true}}})
```

- for all match

```mongodb
db.students.updateMany({experience: {$elemMatch: {duration: {$lte: 1}}}, {$set: {"experience.$.neglect[e]": true}}, {arrayFilters:[{"e.duration": {$lte: 1}}]}})
```

- $pull
- $push

#### Indexing in mongodb:

- indexes are stored in b-tree data structure
- it stores

  1. index key
  2. pointer to document inside the collection

- when query is executed mongodb use index to quickly locate the doc using b-trees

**TRADE-OFF**

1. **storage space** ( as it stores all indexes and pointer separeately)
2. **write performance** (need to write indexes and and pointer in the memory which need some times) i.e. why we should use index efficiently.

##### TYPES OF INDEXES

1. **single field indexes**
2. **Compound field indexes**
3. **Text indexes**

##### How to create and get indexes:

- db.collection.createIndex({})
- db.collection.getIndex()

#### When to not use indexes:

1. when collection is small.
2. when collection is freequently updated
3. when query is complex (i.e. multiple index)

#### When to use:

- when collection is large and make less indexes.

1. **single field indexes**
   db.collecttion.createIndex({age: 1}) // this will sort the collectoin by increasing order or age.

2. **Compound field indexes** (order matters)
   db.collecttion.createIndex({age: 1}, {gender: 1}) // this will sort the collectoin by increasing order or age first then sort them using their gender.

- for ex- if 30 year old age are two people then first female come then male.
  **Notes**:
- here we sort it using age
- also using age and gender
- but not using gender and age
- if you search using gender then age or only gender then it will scan using collection but not indexscan.

3. when query is complex (i.e. multiple index)

4. Covered Query:

5. text index -

- single text index per collection
- tokenization and stemming (i.e. it remove helping verb and adjective from the sentence.)
- relevence score checkup -- checks how much matches occur based on that it rank the score.

#### Aggreagate pipeline in mongodb:

- it groups the multiple documents into single doc based on specified expression.
- here output of one pipeline is the input for the next pipeline
  ![mongodb pipeline](https://media.geeksforgeeks.org/wp-content/uploads/20210209233143/Aggregate-660x477.png)

```mongodb
db.collection.aggregate(pipeline, options)
```

pipeline - here is array like this

```mongodb
db.collection.aggregate([{}, {}, {}, ...], options)

```

##### group:

```mongodb
$group: {_id: expression, field1: expr1, field2: expr2, ...}
db.newTestAgg.aggregate([{$group: {_id: "$age" }}]) // this will group all the people by age i.e. 5 people of 18 year group.

db.newTestAgg.aggregate([{$group: {_id: "$age", names: {$push: "$name"}}}]) // this will group all the people by age i.e. 5 people of 18 year group.


```

```mongodb

db.newTestAgg.aggregate([
  {$match: {gender: "female"}},  // filter based on gender
  {$group: {_id: "$age", newCollection: {$push: {name: "$name" age: "$age"}}}}, // creates group based on age (i.e. if 25 age people two present then it create a single group of 25)
  {$unwind: "$newCollection"}, // destructure the array newCollection
  {$sort: {"newCollection.age": 1}},
  {$skip: 3},
  {$limit: 2},
  {$project: {"newCollection.name":1, "newCollection.age":1, _id: 0}} // project only name and age
])
```

**Note**:

- if you group by \_id: NULL then all the data will be group into single array.

##### $lookup (join in mongodb):

**$unwind** in mongodb:

```mongodb
db.users.aggregate([
  {
    $lookup: {
      from: "posts",
      localField: "_id",
      foreignField: "author",
      as: "userPost"
    }
  },
  {
    $unwind: "$userPost"
  },
  {
    $project: {
      _id: 0,
      username: 1,
      title: "$userPost.title",
      desc: "$userPost.desc"
    }
  }
]);

```

- **from** - which collection you want to make join
- **localField** - field(of users) through which you want to make join
- **foreignField** - field (of other i.e. here posts) upon which join perform (remember both field value must be same to come in output.)
- **as** - array of modified field (or collected field)

###### $unwind:

- here we destructure userPost into separate filed from array.

##### $project:

- is used to take projection

##### $filter:

##### $bucket:

```mongodb
db.newTestAgg.aggregate([
  {
$match: {gender: "male"}},
  {
$bucket: {
groupBy: "$age",
boundaries: [0, 20, 30],
  default: "Gretor than 40: ",
output: {
count: {$sum: 1},
names: {$push: "$name"}
    }
  }
}
])

```

$match: filter by gender
$bucket - to make bucket of groups

1. groupBy - to make group by age field
2. boundaries - to make boundaries here two :

   - one is from 0-20
   - second is from 20-30

default: means if something greator than 30 then come in this group
output: is for outputing

1. count : means counting each bucket with
2. names : print all the name of that age group bucket.

#### capped in mongodb:

```mongodb
db.createCollection("order_log", {capped: true, max: 4, size: 1000000})

```

- capped true
  1. max: 4 - means maxm 4 doc can be stored here. or max size of doc could be of 1000000 bytes.
- if more than that going to store then first inserted doc automatically get deleted and then new doc get inserted

#### Authentication and Authorization in mongodb:
