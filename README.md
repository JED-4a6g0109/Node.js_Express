# Node.js_Express



## 哈囉世界

```javascript
// 載入 HTTP 模組
var http = require("http");

// 創建 HTTP 伺服器並監聽8000 port
http.createServer(function(request, response) {

   // Set the response HTTP header with HTTP status and Content type
   response.writeHead(200, {'Content-Type': 'text/plain'});

   // Send the response body "Hello World"
   response.end('Hello World\n');
}).listen(8000);

// Print URL for accessing server
console.log('Server running at http://127.0.0.1:8000/');
```



## router



routerAPI.js
設置路由


```javascript
var express = require('express')
var router = express.Router()

// middleware that is specific to this router
router.use(function timeLog (req, res, next) {
  console.log('Time: ', Date.now())
  next()
})
// define the home page route
router.get('/', function (req, res) {
  res.send('Birds home page')
})
// define the about route
router.get('/about', function (req, res) {
  res.send('About birds')
})

module.exports = router
```

app.js
加入路由,透過app.use()


```javascript
var express = require('express');
const port = 3333
const API = require('./routerAPI')
const app = express();
app.use('/routerAPI', API)

app.get('/', function(req, res) {
  res.send('Hello World!');
});

app.listen(port, function() {
  console.log('Example app listening on port 3000!');
});
```



## Logs

大多數應用程序將使用*第三方*中間件來簡化常見的 Web 開發任務

使用 cookie、會話、用戶身份驗證、訪問請求`POST`和 JSON 數據、日誌記錄等

npm

```
npm install morgan
```

app.js

```javascript
var express = require('express');
const logger = require('morgan');
const API = require('./routerAPI')
const port = 3333
const app = express();
app.use(logger('dev'));
app.use('/routerAPI', API)

app.get('/', function(req, res) {
  res.send('Hello World!');
});

app.listen(port, function() {
  console.log('Example app listening on port 3333!');
});

```



## 中間安插function

可以在Http Verb function中在調用function來處理自定義事件

```javascript
var express = require('express');
var app = express();

// An example middleware function
var a_middleware_function = function(req, res, next) {
  // ... perform some operations
  next(); // Call next() so Express will call the next middleware function in the chain.
}

// Function added with use() for all routes and verbs
app.use(a_middleware_function);

// Function added with use() for a specific route
app.use('/someroute', a_middleware_function);

// A middleware function added for a specific HTTP verb and route
app.get('/', a_middleware_function);

app.listen(3000);
```



## Serving static files

node.js提供了靜態檔案服務,講白話就是可以把HTML、CSS、Javascript的檔案,透過靜態檔案服務來使用,好處是可以把檔案分區整理需要調用哪個html或是js



在app.js同層資料夾新增public資料夾,在pubilc資料夾裡再新增html、css、js資料夾,新增完後在html資料夾裡新增index.html檔案

```bash
#macOS需要brew install tree才能使用tree來看file tree
>>  tree /f public
資料夾結構如下：
app.js
public
├── css
├── html
│  └── index.html
└── js
```



Index.html

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset='utf-8'>
    <meta http-equiv='X-UA-Compatible' content='IE=edge'>
    <title>Page Title</title>
    <meta name='viewport' content='width=device-width, initial-scale=1'>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">

</head>
<body>
    <table class="table table-dark">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">First</th>
            <th scope="col">Last</th>
            <th scope="col">Handle</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <th scope="row">1</th>
            <td>Mark</td>
            <td>Otto</td>
            <td>@mdo</td>
          </tr>
          <tr>
            <th scope="row">2</th>
            <td>Jacob</td>
            <td>Thornton</td>
            <td>@fat</td>
          </tr>
          <tr>
            <th scope="row">3</th>
            <td>Larry</td>
            <td>the Bird</td>
            <td>@twitter</td>
          </tr>
        </tbody>
      </table>
    
</body>
</html>
```



app.js

```javascript
var express = require('express');
const logger = require('morgan');
const API = require('./routerAPI')
const port = 3333
const app = express();
app.use(logger('dev'));
app.use('/routerAPI', API)

app.use(express.static('public'));

app.get('/', function(req, res) {
  res.send('Hello World!');
});

app.listen(port, function() {
  console.log('Example app listening on port 3333!');
});
```



Start Server

`node app.js`

`http://localhost:3333/html/index.html`

可直接把public file tree類似路由的概念進行訪問

## Using databases

Express本身沒有預設的databse,如果是django有預設sqlite,但不用當心Express很強大支援PostgreSQL, MySQL, Redis, SQLite, MongoDB, etc.等等,需要用npm先安裝,而我使用mongodb強大的資料庫

詳細各種DB安裝請參考

https://expressjs.com/en/guide/database-integration.html



Install

```
npm install mongodb
npm install mongoose

```



Docker install mongoldb

```bash
docker pull mongo
```



docker-compose.yml

```yacas
version: "3.7"
services:
  mongodb:
    container_name: mongo-dev
    image: mongo
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_DATABASE=auth
      - MONGO_INITDB_ROOT_PASSWORD=pass
    ports:
      - '27017:27017'
    volumes: 
        - ./MongoDB/mongodb:/data/db
  mongo-express:
    container_name: mongo-express
    image: mongo-express
    depends_on:
      - mongodb
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
      - ME_CONFIG_MONGODB_ADMINPASSWORD=pass
      - ME_CONFIG_MONGODB_SERVER=mongo-dev
      - ME_CONFIG_BASICAUTH_USERNAME=admin
      - ME_CONFIG_BASICAUTH_PASSWORD=ihavealongpassword
    ports:
      - '8081:8081'

```





mongodbConnect.js

```javascript
var MongoClient = require('mongodb').MongoClient;
MongoClient.connect('mongodb://localhost:27017/', function(err, client) {
  if(err) throw err;
  console.log("Connected to MongoDB server");
  let db = client.db('JS');
});
```

node mongodbConnect.js







```bash
docker pull mongo
```



```bash
docker pull mongo
```



```bash
docker pull mongo
```



```bash
docker pull mongo
```



```bash
docker pull mongo
```

