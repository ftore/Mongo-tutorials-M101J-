```javascript
# Get documents, where writters are Joel Coen and Ethan Coen.
db.movieDetails.find({ "writters" : ["Joel Coen", "Ethan Coen"] }).count()
```

```javascript
# Get documents, where Jeff Bridges is playing leading role.
> db.movieDetails.find({ "actors.0": "Jeff Bridges" }).pretty()
```

```javascript
# Working with mongodb cursors
> var c = db.movieDetails.find();
> var doc = function() { return c.hasNext() ? c.next() : null; }
> c.objsLeftInBatch();
# Example output: 104
> doc() # Iterate the function
> doc() # Iterate the function
> doc() # Iterate the function
> c.objsLeftInBatch();
# Example output: 101
```

```javascript
# Include or exclude fields in return statement.
> db.movieDetails.find({ rated: "PG"}, { title: 1, _id: 0}).pretty()
```

```javascript
# Greater than operator
> db.movieDetails.find({ runtime: { $gt: 90 }).pretty()

# Less than operator
> db.movieDetails.find({ runtime: { $gt: 90, $lt: 120 }).pretty()

# Embedded document and equal to operator
> db.movieDetails.find({ "tomato.meter": { $gte: 95}, runtime: { $gt: 180 } }, { title: 1, runtime: 1, _id: 0 }).pretty()

# Not equal operator
> db.movieDetails.find({ rated: { $ne: "UNRATED" } }).count()

# IN operator
> db.movieDetails.find({ rated: { $in: ["G", "PG"] } }).pretty()
```

```javascript
# Exists operator
> db.movieDetails.find({ "tomato.meter": { $exists: true } }).pretty()
> db.movieDetails.find({ "tomato.meter": { $exists: false } }).pretty()

# Type Operator
> db.moviesScratch.find({ "_id": { $type: "string" } }).count()
```

```javascript
# OR operator
> db.movieDetails.find({ $or: [ { "tomato.meter": { $gt: 95 } }, { "metacritic": { $gt: 88 } } ] }).pretty()

# And operator
> db.movieDetails.find({ $and: [ { "tomato.meter": { $gt: 95 } }, { "metacritic": { $gt: 88 } } ] }).pretty()
# Equalent query
> db.movieDetails.find({ { "tomato.meter": { $gt: 95 } }, { "metacritic": { $gt: 88 } }).pretty()

# Example for using of $and operator
> db.movieDetails.find({ $and: [ { "metacritic": { $ne: null } }, { "metacritic": { $exists: true } } ] }).count()
```

```javascript
# Reg Exp

> db.movieDetails.find({ "awards.text": { $regex: /^Won\s.*/ } } )
```

```javascript
# Array operators

> db.movieDetails.find( { genres: { $all: ["Comedy", "Crime", "Drama" ] } } ).pretty()

> db.movieDetails.find({ countries: { $size: 1 } }).pretty()

> db.movieDetails.find({ boxOffice: { $elemMatch: { country: "UK", revenie: { $gt: 15 } } } }).pretty()
```

```javascript
# Create a asc index
> db.students.createIndex({ student_id: 1});
# Explain database configurations
> db.students.explain().find({ student: 5 });

# Create a complex index. student_id will be asc, class_id will be desc.
> db.students.createIndex({ student_id: 1, class_id: -1 });

# Get collection indexes
> db.students.getIndexes();

# Delete index
> db.students.dropIndex({ student_id: 1});

# Create .not notation index
db.students.createIndex({ 'scores.score': 1 });

# Create unique index
> db.stuff.createIndex({ thing: 1 }, { unique: true });

# Geospatial Indexes
> db.collection.ensureIndex({ location: '2d' })
# Suppose you have a 2D geospatial index defined on the key location in the collection places. Write a query that will find the closest three places (the closest three documents) to the location 74, 140.
> db.places.find( { location : { $near : [74,140] } }).limit(3)

# Geospatial Spherical
> db.places.ensureIndex({ location: '2dg' })
# What is the query that will query a collection named "stores" to return the stores that are within 1,000,000 meters of the location latitude=39, longitude=-130? Type the query in the box below. Assume the stores collection has a 2dsphere index on "loc" and please use the "$near" operator
> db.stores.find({ loc:{ $near:	{ $geometry: { type: "Point", coordinates: [-130, 39]}, $maxDistance:1000000 } } })

# Text index
> db.sentences.ensureIndex({ 'words': 'text' })
> db.sentences.find({ 'text': { $search: 'dog' } } )
```

```javascript
# Profiling 
> db.system.profile.find({ ns: /school.students/}).sort({ts: 1}).pretty()
> db.system.profile.find({millis: { $gt: 1 }}).sort({ts: 1}).pretty()
> db.getProfilingLevel()
> db.setProfilingLevel(1, 4)
> db.getProfilingStatus()
```

```javascript
# Aggregation framework

# Aggregate operator
> db.companies.aggregate([ { $match: { founded_year: 2004 } }, ])

# Projection
> db.companies.aggregate([ { $match: { founded_year: 2004 } }, { $project: { _id: 0, name:1, founded_year: 1 } } ])

# Limit 
> db.companies.aggregate([ { $match: { founded_year: 2004 } }, { $limit: 5 }, { $project: { _id: 0, name:1, founded_year: 1 } } ])

# Sort
> db.companies.aggregate([ { $match: { founded_year: 2004 } }, { $sort: { name: 1 } }, { $limit: 5 }, { $project: { _id: 0, name:1, founded_year: 1 } } ])

# Skip
> db.companies.aggregate([ { $match: { founded_year: 2004 } }, { $sort: { name: 1 } }, { $skip: 10 }, { $limit: 5 }, { $project: { _id: 0, name:1, founded_year: 1 } } ])

# Unwind
> db.companies.aggregate([ { $unwind: "$funding_rounds" }, { $unwind: "$funding_rounds.investments" }, { $match: { "funding_rounds.investments.financial_org.permalink": "greylock" } }, { $project : { _id: 0, name: 1, fundingOrganization: "$funding_rounds.investments.financial_org.permalink" } } , ])
```
