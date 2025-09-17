# Mongodb aggregation cheat sheet

### Match Documents:
This command filters the documents in the collection based on a given condition or set of conditions.
```
db.collection.aggregate([
   { $match: { field: value } }
])
```
### Group by Field:
This command groups the documents in the collection based on a specified field and returns the count or sum of documents in each group. Similar to map object in js.
```
db.collection.aggregate([
   { $group: { _id: "$field", total: { $sum: 1 } } }
])
```
### Project Fields:
This command selects or excludes specific fields from the documents in the collection.
[Array.map()]
```
db.collection.aggregate([
   { $project: { field1: 1, field2: 1 } }
])
```
### Sort Documents:
This command sorts the documents in the collection based on a specified field and in ascending or descending order.
$sort taks 1/-1 for the order
```
db.collection.aggregate([
   { $sort: { field: 1 } }
])
```
### Skip Documents:
This command skips a specified number of documents from the beginning of the result set.
with $limit Used for pagination.
```
db.collection.aggregate([
   { $skip: 10 }
])

```
### pagination  
```
  const result = await collection.aggregate([
        { $skip: (pageNumber - 1) * pageSize },
      { $limit: pageSize }
    ]).toArray();
```    
### The $in operator   
is used to match documents where the value of a specified field matches any value in a provided array. 
[Array.includes()]
```
db.users.aggregate([
  { $match: { status: { $in: ["active", "pending"] } } }
])
```
### $regex
the $regex operator takes a regular expression pattern as its value, which can be specified using the JavaScript RegExp object or using a string literal.
```
db.users.aggregate([
  {$match: {field: { $regex: "str", $options: "i" }}}
])
```

### Lookup Documents from another Collection:
This command performs a left outer join between two collections and returns all matching documents from the joined collection.  
**from**: Specifies the name of the collection to join with.

**localField**: Specifies the name of the field from the input documents. This field is used for the matching operation with the foreignField.

**foreignField**: Specifies the name of the field from the joining documents. This field is used for the matching operation with the localField.

**as**: Specifies the name of the new array field to be added to the input documents. The joined documents are added to this array as sub-documents.

**pipline**: Use this to loop over items in the lookup. This will replace $unwind and $group 
```
db.orders.aggregate([
   {
      $lookup:
         {
           from: "products",
           localField: "product_id",
           foreignField: "_id",
           as: "product"
           pipeline: [
            { $project: { 'password': 0, 'score': 0 } 
           ]                
         }
   }
])

```
[$lookup in 10 min youTube](https://youtu.be/cuLYt1ODSk4/)

## Insert and extract from Array
This will be the same as using Array.pop() method
```
{
  "_id": mongoStrId,
  "fruits": [
    { "id": 1, "name": "apple" },
    { "id": 3, "name": "orange" },
    { "id": 4, "name": "apple" }
  ]
}
db.collection.updateOne(
  { _id: mongoStrId },
  { $pull: { fruits: { $elemMatch: { id: 2 } } } }
);

```
This will be the same as using Array.push()
```
db.collection.updateOne(
  { _id: 1 },
  { $push: { fruits: "orange" } }
);

```

## Get updated document
```
db.collection.findOneAndUpdate(
  { _id: ObjectId("your-document-id") }, 
  { $set: { fieldName: "newValue" } },   
  { returnDocument: "after" }            
)
```
## Change field name
```
 {$set: {"byUser.userId": "$byUser._id" }}
```

## Remove fields
```
 {$unset: ["byUser._id"]}
```
## Documentation

[Mongodb docs](https://www.mongodb.com/docs/v5.0/meta/aggregation-quick-reference/)

