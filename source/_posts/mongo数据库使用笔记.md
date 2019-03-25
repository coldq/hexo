---
title: Windows下启动mongo
date: 2017-05-06 18:10:47
tags: 
  - mongo
categories: mongo
---

```shell
C:\Program Files\MongoDB\Server\3.4\bin> .\mongod --dbpath D:\MGDB 
//dbpath 指定数据存放地址
```

### mongoose简单使用

1. 连接

```js
var connection = mongoose.createConnection('mongodb://localhost:27017/test');
var db = connection.model('Tank', yourSchema);
```

2. 保存

```js
var Tank = db.model('Tank', yourSchema);

var small = new Tank({ size: 'small' });
small.save(function (err) {
  if (err) return handleError(err);
  // saved!
})

// or

Tank.create({ size: 'small' }, function (err, small) {
  if (err) return handleError(err);
  // saved!
})

```

3. 删除

```js
Tank.remove({ size: 'large' }, function (err) {
  if (err) return handleError(err);
  // removed!
});
```
