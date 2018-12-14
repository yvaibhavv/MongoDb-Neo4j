MONGODB README:

PREPREOCESSING STEPS:

1) Remove  excessive quotes from the Posts.tsv file by using notepad++.

2) The datasets are present in the mongodb data folder.

3) Using terminal import the datasets using the following commands: 

mongoimport --db assignment --collection posts --type tsv --headerline  --file path to(Posts.tsv)
mongoimport --db assignment --collection votes --type tsv --headerline  --file path to(Votes.tsv)
mongoimport --db assignment --collection tags --type tsv --headerline  --file path to(Tags.tsv)
mongoimport --db assignment --collection user --type tsv --headerline  -—file path to(User.tsv)

4)changing date format to iso in files using function:

POSTS :

a)db.posts.find().forEach(function(dat){
  dat.CreationDate = new Date(dat.CreationDate);
  db.posts.save(dat);
})

b)db.posts.find().forEach(function(dat){
  dat.ClosedDate = new Date(dat.ClosedDate);
  db.posts.save(dat);
})

USERS:
﻿a)﻿db.users.find().forEach(function(dat){
    dat.CreationDate = new Date(dat.CreationDate);
    db.users.save(dat)
})

b)db.users.find().forEach(function(dat){
    dat.LastAccessDate = new Date(dat.LastAccessDate);
    db.users.save(dat)
})

VOTES:

a)db.votes.find().forEach(function(dat){
    dat.CreationDate = new Date(dat.CreationDate);
    db.votes.save(dat)
})

5) Creating indexes for the combined info collection

db.combinedinfo.createIndex({OwnerUserId:1})
db.combinedinfo.createIndex({Id:1})
db.combinedinfo.createIndex({AcceptedAnswerId:1})
db.combinedinfo.createIndex({ParentId:1})
db.combinedinfo.createIndex({tags:1})
﻿db.combinedinfo.createIndex({ViewCount:1})
﻿db.combinedinfo.createIndex({PostTypeId:1})
db.votes.createIndex({PostId:1})
db.votes.createIndex({UserId:1})

6)We also changed the shell timeout to 100 seconds in mongodb to avoid timeout at a later stage.

7) Query to embedd users, votes and tags to combined info collection:

Embedding votes :

db.post.aggregate(
[{
   $lookup:
   {from:"votes",
       localField: "Id",
       foreignField: "PostId",
       as:"votes "   }},{ $out:"post_vote"}])

Embedding users :
db.post_vote.aggregate(
[{$lookup: {from: "users",localField: "OwnerUserId" ,foreignField: "Id",as: "user_info"}},{"$out":"collected"}])

Splitting tags:

db.collected.aggregate([{"$addFields": { "tags":{ "$split":[ "$Tags",","] } } },{"$out":"combinedinfo" }])

7) Run queries from the mongoldb_queries file in mongodb folder from the zip files.






