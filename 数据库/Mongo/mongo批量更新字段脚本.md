```js
// 调试开关
var debug = true;
// 操作集合
var collection = 'pr_organize_info';
db.getCollection(collection).find().forEach(function(doc){
    var set = {};
    // todo、 convert fields
    
    var criteria = {_id:doc._id};
    var update = { $set : set };
    if (debug) {
        print(set);
    } else {
        db.getCollection(collection).update( criteria, update, false, false);
    }
})
```

```js
// 调试开关
var debug = true;
var ids = [// "a921f19b-d3c3-451c-b995-96a1b9341b0f",
"bbb8af7e-b164-4bac-8e2f-a65cba5c5a21",
"cc7c7a9a-d148-426b-a179-719087c15836",
"b0277629-acfb-4b58-9248-05f42b673710",
"45a82824-94d5-4a1f-b960-3bdf7b7fcab7",
"9dfa8967-c8cf-4145-907b-9b370867a125",
"51bfb3c9-a819-4210-9133-47bec405300d",
"1a806d67-111c-4025-aea4-654afcb35e33",
"ad8c39fb-afd0-4f93-89e3-c13afaa18b27",
"68d010bf-576f-4457-87b1-364fa792420e",
"efc593df-f28b-4b5c-8c5e-85b7073509f0",
"5aafe3f6-d928-4b10-8cfd-9889fefd0935",
"cd75b040-76c0-472b-b048-502ea40cf116",
"d5b8eaf2-9907-4185-b45f-5952726ef445",
"986c1681-7066-40b2-a50d-40e8f151eed1",
"f00d20ce-bc19-4ff3-a912-6e231460760a"];
// 操作集合
var collection = 'followuprecords';
db.getCollection(collection).find({"historyId" : {$in: ids}}).forEach(function(doc){
    var set = {};
    // todo、 convert fields
    if (doc.telephonefollowup) {
        set.telephonefollowup = doc.telephonefollowup.replace(/(AI助手:)/g, '\n$1').replace(/(患者:)/g, '\n$1').replace('\n', '')
    }
    
    var criteria = {_id:doc._id};
    var update = { $set : set };
    if (debug) {
        print(doc);
    } else {
        db.getCollection(collection).update( criteria, update, false, false);
    }
})
```