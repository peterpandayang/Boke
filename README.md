# Boke

To run this app, just copy this folder to your local machine and use : $ npm install to install all the modules.


Step-by-step set up process:

Step 1: Setup the environment

Install express generator:
$ sudo npm install express-generator -g

Create an express project:
$ express -e app

Install modules:
$ cd app
$ npm install

Find PID:
$ lsof -i tcp:3000 

Kill PID:
$ kill -9 6106

start the server:
$ npm start

This project take advantage of the ejs model engine
app.js: the entry ducument
package.json: dependencies of the modules
node_modules: store the installed modules
public: store image, css, js files
routes: store route files
views: store view documents or model documents
bin: store runnable documents

Instructions:
(1) var app = express()：produce an express app。
(2)app.set('views', path.join(__dirname, 'views’))：set views folder as the documents to store the view files, _dirname is the global variable and store the catalog of the running script
(3)app.set('view engine', 'ejs’)：set the view engine as ejs
(4)app.use(favicon(__dirname + '/public/favicon.ico’))set /public/favicon.ico as favicon icon
(5)app.use(logger('dev’))：add logger middleware
(6)app.use(bodyParser.json())： add middleware to parse json
(7)app.use(bodyParser.urlencoded({ extended: false }))： add middleware to parse urlencoded request body
(8)app.use(cookieParser())：add middleware to parse cookie
(9)app.use(express.static(path.join(__dirname, 'public')))：set public folder to store static files
(10)app.use('/', routes); and app.use('/users', users)：route controller
(11)
app.use(function(req, res, next) {
    var err = new Error('Not Found');
    err.status = 404;
    next(err);
});
capture 404 error and resend to error handler
(12)
if (app.get('env') === 'development') {
    app.use(function(err, req, res, next) {
        res.status(err.status || 500);
        res.render('error', {
            message: err.message,
            error: err
        });
    });
}
error handler in the development environment and display the error message in the browser
(13)
app.use(function(err, req, res, next) {
    res.status(err.status || 500);
    res.render('error', {
        message: err.message,
        error: {}
    });
});
error handler in the production environment and will not leak the error message to the user
(14)module.exports = app ：export app for other modules to use

take a look at bin/www：

#!/usr/bin/env node
var debug = require('debug')('blog');
var app = require('../app');
app.set('port', process.env.PORT || 3000);
var server = app.listen(app.get('port'), function() {
  debug('Express server listening on port ' + server.address().port);
});
(1)#!/usr/bin/env node：demonstrate the this is a node runnable file
(2)var debug = require('debug')('blog’)：import debug module and print debug logger
(3)var app = require('../app’)：import the exported app
(4)app.set('port', process.env.PORT || 3000)：set port 
(5)
var server = app.listen(app.get('port'), function() {
  debug('Express server listening on port ' + server.address().port);
});
start and listen port 3000, if successful, print Express server listening on port 3000

for routes/index.js file：
var express = require('express');
var router = express.Router();
/* GET home page. */
router.get('/', function(req, res) {
  res.render('index', { title: 'Express' });
});
module.exports = router;
create a router to capture the GET request when visiting the home page, export this route and load by 
app.use('/', routes); in app.js
Everytime the home page is visited, res.render('index', { title: 'Express' }); will be called
Apply views/index.ejs model and display in the browser

For views/index.ejs :
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1><%= title %></h1>
    <p>Welcome to <%= title %></p>
  </body>
</html>
When creating model, we pass a variable named title with value of "express" string, the model engine will replace all the <%= title %> as express and display the created html in the browser


Route Control:
We can put all the route controller and function to achieve route in the index.js and there is only one general route interface in the app.js
So we add the following in the app.js:
routes(app);

The index.js will be the following:
module.exports = function(app) {
  app.get('/', function (req, res) {
    res.render('index', { title: 'Express' });
  });
};

Route Rules:
app.get() and app.post(): The first param is the request path, the second param is the callback function, there are two params in the callback function-- req and res, representing the request info and response info

req.query： handle get request and acquire request param
req.params： handle /:xxx form get or post request and acquire request params
req.body： handle post request and acquire post body
req.param()： handle get and post request, the priority is: req.params→req.body→req.query
Examples:
req.query
// GET /search?q=tobi+ferret  
req.query.q  
// => "tobi ferret"  
// GET /shoes?order=desc&shoe[color]=blue&shoe[type]=converse  
req.query.order  
// => "desc"  
req.query.shoe.color  
// => "blue"  
req.query.shoe.type  
// => "converse"

req.body
// POST user[name]=tobi&user[email]=tobi@learnboost.com  
req.body.user.name  
// => "tobi"  
req.body.user.email  
// => "tobi@learnboost.com"  
// POST { "name": "tobi" }  
req.body.name  
// => "tobi"  

req.params
// GET /user/tj  
req.params.name  
// => "tj"  
// GET /file/javascripts/jquery.js  
req.params[0]  
// => "javascripts/jquery.js" 

req.param(name)
// ?name=tobi  
req.param('name')  
// => "tobi"  
// POST name=tobi  
req.param('name')  
// => "tobi"  
// /user/tobi for /user/:name   
req.param('name')  
// => "tobi"  

We can try to add a demo page for localhost:3000/newpage:
We can add a /newpage route rule in the index.js with:
app.get('/newpage', function (req, res) {
  res.send('hello,world!');
});

How to make the server to automatic apply to the changes:
1. Change node -> nodemon in the package.json;
2. $ sudo npm install nodemon -g 


Models Engine
It can combine page model and data to be displayed to be a HTML page.
In the MVC architecture, the model engine is included in the server side
ejs is a kind of model engine and is friendly with express

There are only three tags in ejs:
<% code %>：JavaScript code
<%= code %>：display special character content with replacement of HTML
<%- code %>：display the original HTML

We can use javascript inside <% %>
The Data
supplies: ['mop', 'broom', 'duster']
The Template
<ul>
<% for(var i=0; i<supplies.length; i++) {%>
   <li><%= supplies[i] %></li>
<% } %>
</ul>
The Result
<ul>
  <li>mop</li>
  <li>broom</li>
  <li>duster</li>
</ul>

Use include to do page layout
index.ejs
<%- include a %>
hello,world!
<%- include b %>

a.ejs
this is a.ejs

b.ejs
this is b.ejs

index.ejs will display
this is a.ejs
hello,world!
this is b.ejs

We have the following routing plan
/ ：Home page
/login ：User Login
/reg ：User Register
/post ：Post
/logout Logout

Based on the plan, we have the following for index.js
module.exports = function(app) {
  app.get('/', function (req, res) {
    res.render('index', { title: '主页' });
  });
  app.get('/reg', function (req, res) {
    res.render('reg', { title: '注册' });
  });
  app.post('/reg', function (req, res) {
  });
  app.get('/login', function (req, res) {
    res.render('login', { title: '登录' });
  });
  app.post('/login', function (req, res) {
  });
  app.get('/post', function (req, res) {
    res.render('post', { title: '发表' });
  });
  app.post('/post', function (req, res) {
  });
  app.get('/logout', function (req, res) {
  });
};

Database(MongoDB):
Locally start the database:
Download the MongoDB and rename it to mongodb. Make a directory named boke and enter bin folder to run:
$ ./mongod --dbpath ../boke/
this means that settting boke folder as saving catalog and start the database

Connect to MongoDB:
Add the MongoDB module
$ npm install mongodb -- save

Make a setting.js file in the root 
module.exports = { 
  cookieSecret: 'myblog', 
  db: 'boke', 
  host: 'localhost',
  port: 27017
}; 
db is the name of the database, host is the address of the database, port is the port i for the database and cookieSecret is used for crypting Cookie 

Then we make models folder in the root and make db.js with the following code
var settings = require('../settings'),
Db = require('mongodb').Db,
Connection = require('mongodb').Connection,
Server = require('mongodb').Server;
module.exports = new Db(settings.db, new Server(settings.host, settings.port), {safe: true});

The last line: new Db(settings.db, new Server(settings.host, settings.port), {safe: true}); set the name for the database, the host and the port as an incidence and exports the incidence. So we can require this to read and write to the databse

Then open app.js，add the following code under var routes = require('./routes/index'); 
var settings = require('./settings');

We can use session middleware to store the user info in the memory and databse using express-session and connect-mongo:
$ npm install express-session --save
$ npm install connect-mongo --save

Add the following to the app.js:
var session = require('express-session');
var MongoStore = require('connect-mongo')(session);
app.use(session({
  secret: settings.cookieSecret,
  key: settings.db,//cookie name
  cookie: {maxAge: 1000 * 60 * 60 * 24 * 30},//30 days
  store: new MongoStore({
    url: 'mongodb://localhost/boke'
  })
}));

Using the express-session and connect-mongo module will store the info in the mongodb. secret is used for changing the cookie, value of key is the name of the cookie, setting cookie's maxAge to set the living length of the cookie to be 30 days. Set its param as incidence of mnogodb and pass the session to the databse in case of loss 


Register and login page

change views/index.ejs as:
<%- include header %>
This is home pase
<%- include footer %>

Create header.ejs file under views folder and add the following code：
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Blog</title>
		<link rel="stylesheet" href="/stylesheets/style.css">
	</head>
	<body>
<header>
<h1><%= title %></h1>
</header>
<nav>
	<span><a title="Home Page" href="/">home</a></span>
	<span><a title="Login" href="/login">login</a></span>
	<span><a title="Regiser" href="/reg">register</a></span>
</nav>
<article>

Create footer.js under the same directory
</article>
</body>
</html>

Change css as the following:
/* inspired by http://yihui.name/cn/ */
*{padding:0;margin:0;}
body{width:600px;margin:2em auto;padding:0 2em;font-size:14px;font-family:"Microsoft YaHei";}
p{line-height:24px;margin:1em 0;}
header{padding:.5em 0;border-bottom:1px solid #cccccc;}
nav{float:left;font-family:"Microsoft YaHei";font-size:1.1em;text-transform:uppercase;margin-left:-12em;width:9em;text-align:right;}
nav a{display:block;text-decoration:none;padding:.7em 1em;color:#000000;}
nav a:hover{background-color:#ff0000;color:#f9f9f9;-webkit-transition:color .2s linear;}
article{font-size:16px;padding-top:.5em;}
article a{color:#dd0000;text-decoration:none;}
article a:hover{color:#333333;text-decoration:underline;}
.info{font-size:14px;}

Then we add login.ejs under views:
<%- include header %>
<form method="post">
  UserName: <input type="text" name="name"/><br />
  PassWord:  <input type="password" name="password"/><br />
         <input type="submit" value="login"/>
</form>
<%- include footer %>

Then we create reg.ejs under views folder:
<%- include header %>
<form method="post">
  UserName:  <input type="text" name="name"/><br />
  PassWord：    <input type="password" name="password"/><br />
  Confirm PassWord：<input type="password" name="password-repeat"/><br />
  Email：    <input type="email" name="email"/><br />
           <input type="submit" value="register"/>
</form>
<%- include footer %>

Flash
flash, also known as connect-flash, is a specific zone for store info. When the info is written in the flash, it will disapear after shown in the next page. One application is the redirect, making sure that the next page is the one to show:
$ npm install connect-flash --save

Require it in the app.js and use flash module:
var flash = require('connect-flash'); // after var settings = require('./settings');
app.use(flash());		// after app.set('view engine', 'ejs');
Be suer to put app.use(session({...})) before app.use(flash())! Put both before routes(app);


Register response
Add post to the register page
Add user.js under models folder:
var mongodb = require('./db');

function User(user) {
  this.name = user.name;
  this.password = user.password;
  this.email = user.email;
};
module.exports = User;
//存储用户信息
User.prototype.save = function(callback) {
  //要存入数据库的用户文档
  var user = {
      name: this.name,
      password: this.password,
      email: this.email
  };
  //打开数据库
  mongodb.open(function (err, db) {
    if (err) {
      return callback(err);//错误，返回 err 信息
    }
    //读取 users 集合
    db.collection('users', function (err, collection) {
      if (err) {
        mongodb.close();
        return callback(err);//错误，返回 err 信息
      }
      //将用户数据插入 users 集合
      collection.insert(user, {
        safe: true
      }, function (err, user) {
        mongodb.close();
        if (err) {
          return callback(err);//错误，返回 err 信息
        }
        callback(null, user[0]);//成功！err 为 null，并返回存储后的用户文档
      });
    });
  });
};

//读取用户信息
User.get = function(name, callback) {
  //打开数据库
  mongodb.open(function (err, db) {
    if (err) {
      return callback(err);//错误，返回 err 信息
    }
    //读取 users 集合
    db.collection('users', function (err, collection) {
      if (err) {
        mongodb.close();
        return callback(err);//错误，返回 err 信息
      }
      //查找用户名（name键）值为 name 一个文档
      collection.findOne({
        name: name
      }, function (err, user) {
        mongodb.close();
        if (err) {
          return callback(err);//失败！返回 err 信息
        }
        callback(null, user);//成功！返回查询的用户信息
      });
    });
  });
};
User.prototype.save can store the user's info and User.get can get the user's info

Then we add code to index.js:
var crypto = require('crypto'),
	User = require('../models/user.js');
crypto is a core module in Node.js and we use it to encode the password

Add the crypto module:
$ npm install crypto --save

Then we modify app.post('/reg') in the index.js:
app.post('/reg', function (req, res) {
  var name = req.body.name,
      password = req.body.password,
      password_re = req.body['password-repeat'];
  //检验用户两次输入的密码是否一致
  if (password_re != password) {
    req.flash('error', 'Password should be the same!'); 
    return res.redirect('/reg');//返回注册页
  }
  //生成密码的 md5 值
  var md5 = crypto.createHash('md5'),
      password = md5.update(req.body.password).digest('hex');
  var newUser = new User({
      name: name,
      password: password,
      email: req.body.email
  });
  //检查用户名是否已经存在 
  User.get(newUser.name, function (err, user) {
    if (err) {
      req.flash('error', err);
      return res.redirect('/');
    }
    if (user) {
      req.flash('error', 'User already existed!');
      return res.redirect('/reg');//返回注册页
    }
    //如果不存在则新增用户
    newUser.save(function (err, user) {
      if (err) {
        req.flash('error', err);
        return res.redirect('/reg');//注册失败返回主册页
      }
      req.session.user = newUser;//用户信息存入 session
      req.flash('success', 'Register successful!');
      res.redirect('/');//注册成功后返回主页
    });
  });
});

We store the user info in session and we can use req.session.user to read the user's info
req.body： this is the objest after the parse of POST request. For example, we can access 
req.body['password'] or req.body.password to get value in name="password";
res.redirect： it achieves page redirect function;
User：User is a discriptive object--model in the MVC. It is different from views or controllers, it deals with real data and it is the root part of the website.

Check if the user is in the database or not
go to database bin folder
$ mongo
$ use boke
$ db.users.find()

Then we add the after-register page
Modify header.ejs and change <nav></nav> to :
<nav>
<span><a title="Home Page" href="/">Home</a></span>
<% if (user) { %>
  <span><a title="发表" href="/post">post</a></span>
  <span><a title="登出" href="/logout">logout</a></span>
<% } else { %>
  <span><a title="登录" href="/login">login</a></span>
  <span><a title="注册" href="/reg">register</a></span>
<% } %>
</nav>
And add the following page after<article>:
<% if (success) { %>
  <div><%= success %></div>
<% } %>
<% if (error) { %>
  <div><%= error %> </div>
<% } %>

Change app.get('/') in the index.js to:
app.get('/', function (req, res) {
  res.render('index', {
    title: 'Home Page',
    user: req.session.user,
    success: req.flash('success').toString(),
    error: req.flash('error').toString()
  });
});

Change app.get('reg') to:
app.get('/reg', function (req, res) {
  res.render('reg', {
    title: '注册',
    user: req.session.user,
    success: req.flash('success').toString(),
    error: req.flash('error').toString()
  });
});


We use session to detect the status of the user and navigate to the different info.
After successfully registered, the user's info is stored in session, and jumped to Home Page. User info in the session is assigned to variable user. When loading index.ejs, it will detect if the user is online

success: req.flash('success').toString() means assigne the success info to viriable success; error: req.flash('error').toString() means assign error message to variable error and we pass these two variables when loanding ejs file


Login in and Logout
Change app.post('/login') in index.js as follows:
app.post('/login', function (req, res) {
  //生成密码的 md5 值
    var md5 = crypto.createHash('md5'),
        password = md5.update(req.body.password).digest('hex');
    //检查用户是否存在
    User.get(req.body.name, function (err, user) {
      if (!user) {
        req.flash('error', 'User Not Existed!'); 
        return res.redirect('/login');//用户不存在则跳转到登录页
      }
      //检查密码是否一致
      if (user.password != password) {
        req.flash('error', 'Wrong Password!'); 
        return res.redirect('/login');//密码错误则跳转到登录页
      }
      //用户名密码都匹配后，将用户信息存入 session
      req.session.user = user;
      req.flash('success', 'Login Successful!');
      res.redirect('/');//登陆成功后跳转到主页
    });
  });

  Change app.get('/login') as follows:
  app.get('/login', function (req, res) {
    res.render('login', {
        title: 'Login',
        user: req.session.user,
        success: req.flash('success').toString(),
        error: req.flash('error').toString()});
  });

  Change app.get('/logout') as follows:
  app.get('/logout', function (req, res) {
    req.session.user = null;
    req.flash('success', 'Logout Successful!');
    res.redirect('/');//登出成功后跳转到主页
  });
  We assign null to req.session.user to discard user's info in session to achieve logout



Authentification Management
To stop the user access localhost:3000/reg when he has login, we need to do the following:
Add the two function in the index.js:
function checkLogin(req, res, next) {
        if (!req.session.user) {
            req.flash('error', 'Not Logged in!');
            res.redirect('/login');
        }
        next();
    }

    function checkNotLogin(req, res, next) {
        if (req.session.user) {
            req.flash('error', 'Logged in!');
            res.redirect('back');
        }
        next();
    }

So index.js should be looking like this:
var crypto = require('crypto'),
    User = require('../models/user.js');

module.exports = function(app) {
  app.get('/', function (req, res) {
    res.render('index', {
      title: 'Home Page',
      user: req.session.user,
      success: req.flash('success').toString(),
      error: req.flash('error').toString()
    });
  });

  app.get('/reg', checkNotLogin);
  app.get('/reg', function (req, res) {
    res.render('reg', {
      title: 'Register',
      user: req.session.user,
      success: req.flash('success').toString(),
      error: req.flash('error').toString()
    });
  });

  app.post('/reg', checkNotLogin);
  app.post('/reg', function (req, res) {
    var name = req.body.name,
        password = req.body.password,
        password_re = req.body['password-repeat'];
    if (password_re != password) {
      req.flash('error', '两次输入的密码不一致!'); 
      return res.redirect('/reg');
    }
    var md5 = crypto.createHash('md5'),
        password = md5.update(req.body.password).digest('hex');
    var newUser = new User({
        name: name,
        password: password,
        email: req.body.email
    });
    User.get(newUser.name, function (err, user) {
      if (err) {
        req.flash('error', err);
        return res.redirect('/');
      }
      if (user) {
        req.flash('error', '用户已存在!');
        return res.redirect('/reg');
      }
      newUser.save(function (err, user) {
        if (err) {
          req.flash('error', err);
          return res.redirect('/reg');
        }
        req.session.user = user;
        req.flash('success', '注册成功!');
        res.redirect('/');
      });
    });
  });

  app.get('/login', checkNotLogin);
  app.get('/login', function (req, res) {
    res.render('login', {
      title: 'Login',
      user: req.session.user,
      success: req.flash('success').toString(),
      error: req.flash('error').toString()
    }); 
  });

  app.post('/login', checkNotLogin);
  app.post('/login', function (req, res) {
    var md5 = crypto.createHash('md5'),
        password = md5.update(req.body.password).digest('hex');
    User.get(req.body.name, function (err, user) {
      if (!user) {
        req.flash('error', '用户不存在!'); 
        return res.redirect('/login');
      }
      if (user.password != password) {
        req.flash('error', '密码错误!'); 
        return res.redirect('/login');
      }
      req.session.user = user;
      req.flash('success', '登陆成功!');
      res.redirect('/');
    });
  });

  app.get('/post', checkLogin);
  app.get('/post', function (req, res) {
    res.render('post', {
      title: 'Post',
      user: req.session.user,
      success: req.flash('success').toString(),
      error: req.flash('error').toString()
    });
  });

  app.post('/post', checkLogin);
  app.post('/post', function (req, res) {
  });

  app.get('/logout', checkLogin);
  app.get('/logout', function (req, res) {
    req.session.user = null;
    req.flash('success', '登出成功!');
    res.redirect('/');
  });

  function checkLogin(req, res, next) {
    if (!req.session.user) {
      req.flash('error', 'Not Logged in!'); 
      res.redirect('/login');
    }
    next();
  }

  function checkNotLogin(req, res, next) {
    if (req.session.user) {
      req.flash('error', 'Logged in!'); 
      res.redirect('back');
    }
    next();
  }
};

Notice: To maintain the user's status and flash notification function, we pass three value to every ejs file:
user: req.session.user,
success: req.flash('success').toString(),
error: req.flash('error').toString()


Post article:
Page Design
Make post.ejs under views folder:
<%- include header %>
<form method="post">
  标题：<br />
  <input type="text" name="title" /><br />
  正文：<br />
  <textarea name="post" rows="20" cols="100"></textarea><br />
  <input type="submit" value="发表" />
</form>
<%- include footer %>


Article Model
We name article model as Post object as with User. We have interface like Post.get and Post.prototype.save. Post.get can get article from database based on the user's request. Post.prototype.save can save article to the database
We can make post.js under models folder:
var mongodb = require('./db');

function Post(name, title, post) {
  this.name = name;
  this.title = title;
  this.post = post;
}

module.exports = Post;

//存储一篇文章及其相关信息
Post.prototype.save = function(callback) {
  var date = new Date();
  //存储各种时间格式，方便以后扩展
  var time = {
      date: date,
      year : date.getFullYear(),
      month : date.getFullYear() + "-" + (date.getMonth() + 1),
      day : date.getFullYear() + "-" + (date.getMonth() + 1) + "-" + date.getDate(),
      minute : date.getFullYear() + "-" + (date.getMonth() + 1) + "-" + date.getDate() + " " + 
      date.getHours() + ":" + (date.getMinutes() < 10 ? '0' + date.getMinutes() : date.getMinutes()) 
  }
  //要存入数据库的文档
  var post = {
      name: this.name,
      time: time,
      title: this.title,
      post: this.post
  };
  //打开数据库
  mongodb.open(function (err, db) {
    if (err) {
      return callback(err);
    }
    //读取 posts 集合
    db.collection('posts', function (err, collection) {
      if (err) {
        mongodb.close();
        return callback(err);
      }
      //将文档插入 posts 集合
      collection.insert(post, {
        safe: true
      }, function (err) {
        mongodb.close();
        if (err) {
          return callback(err);//失败！返回 err
        }
        callback(null);//返回 err 为 null
      });
    });
  });
};

//读取文章及其相关信息
Post.get = function(name, callback) {
  //打开数据库
  mongodb.open(function (err, db) {
    if (err) {
      return callback(err);
    }
    //读取 posts 集合
    db.collection('posts', function(err, collection) {
      if (err) {
        mongodb.close();
        return callback(err);
      }
      var query = {};
      if (name) {
        query.name = name;
      }
      //根据 query 对象查询文章
      collection.find(query).sort({
        time: -1
      }).toArray(function (err, docs) {
        mongodb.close();
        if (err) {
          return callback(err);//失败！返回 err
        }
        callback(null, docs);//成功！以数组形式返回查询的结果
      });
    });
  });
};


Post Response:
We add post response to the aiticle and add the following code in index.js:
,Post = require('../models/post.js');

Modify app.post('/post') as follows:
app.post('/post', checkLogin);
app.post('/post', function (req, res) {
  var currentUser = req.session.user,
      post = new Post(currentUser.name, req.body.title, req.body.post);
  post.save(function (err) {
    if (err) {
      req.flash('error', err); 
      return res.redirect('/');
    }
    req.flash('success', '发布成功!');
    res.redirect('/');//发表成功跳转到主页
  });
});

Then we modify index.ejs as follows:
<%- include header %>
<% posts.forEach(function (post, index) { %>
  <p><h2><a href="#"><%= post.title %></a></h2></p>
  <p class="info">
    作者：<a href="#"><%= post.name %></a> | 
    日期：<%= post.time.minute %>
  </p>
  <p><%- post.post %></p>
<% }) %>
<%- include footer %>

Change app.get('/') in the index.js:
app.get('/', function (req, res) {
  Post.get(null, function (err, posts) {
    if (err) {
      posts = [];
    } 
    res.render('index', {
      title: '主页',
      user: req.session.user,
      posts: posts,
      success: req.flash('success').toString(),
      error: req.flash('error').toString()
    });
  });
});

You can use Robomongo to view your database



Step 2: Use markdown
Install Markdown:
$ npm install markdown --save

Add a line after mongodb = require('./db') in post.js:
markdown = require('markdown').markdown;

Add code before callback(null, docs); in Post.get function:
//解析 markdown 为 html
docs.forEach(function (doc) {
	if(doc.post){  				// 加个判断即可. 报错是提示doc.post为null导致后续出错
    	doc.post = markdown.toHTML(doc.post);
    }
});


Step 2-extra: Deploy database to MongoLab
Configurite your MongoDB in MongoLab (website: https://mlab.com/home)

Change settings.js to:
module.exports = { 
  cookieSecret: 'myblog', 
  url: 'mongodb://root:123456@ds023560.mlab.com:23560/boke'
};

Change app.use(session(...)); in app.js to:
app.use(session({
  secret: settings.cookieSecret,
  cookie: {maxAge: 1000 * 60 * 60 * 24 * 30},//30 days
  url: settings.url
}));


Delete(Comment) db.js and make changes to post.js, user.js and comment.js as follows:
<!-- Change mongodb = require('./db') into mongodb = require('mongodb').Db 
Add var settings = require('../settings');
Change all mongodb.open(function (err, db) { into mongodb.connect(settings.url, function (err, db) {
Change all mongodb.close(); into db.close(); -->
Replace var mongodb = require('./db'); with var MongoClient = require('mongodb').MongoClient;
Add var settings = require('../settings');
Change all mongodb.open(function (err, db) { into MongoClient.connect(settings.url, function (err, db) {
Change all mongodb.close(); into db.close();





















