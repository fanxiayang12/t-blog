```js
// mongo localhost:27017/local -quiet mongo-shell.js

db = connect("localhost:27017/local");
// db.auth();

var cursor = db.getCollection('hospital_clear_log_159').find({}).sort({'_id':1}).limit(3);
// cursor.forEach(printjson)
// 1 find
var datas = [];
cursor.forEach(function(data){
    datas.push(data);
});
// 2 printjson
printjson(datas);
// 3 insert to another db
datas.forEach(function(data){
    db.getCollection('hospital_clear_log_159').insert(data);
});

// check insert/delete/update result
if (db.getLastError() == null) {
	print('update done!!!');
} else {
	print(db.getLastError());
}
```