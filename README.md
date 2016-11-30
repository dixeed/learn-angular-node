# Learn Angular/Node
Project used to teach basic concepts of using angular and node for a REST API development. It is mainly used for our new comers that are new to JavaScript full-stack.

This project is split into 2 parts:
* The server (backend)
* The client (frontend)


## Goal
We are going to build a TODO application. For simplicity sake, we are going to have only one entity which is __Task__. This entity will have 2 fields: title and content.

At the end of the project we will have a simple application to handle TODO tasks that contacts a REST API on the server.

## Instructions
Before we dive into the project, you need to __fork__ this repository into your own account. You will work in this fork and not the original repository. If you wonder how to fork, Github already made a good [tutorial](https://help.github.com/articles/fork-a-repo/) about it. Once the fork is created you need to clone it on your local machine to get started.

Once you complete a section, you need to tag that version of your code so we can come back to it later. Before tagging, your code should be committed. You will create an annotated tag: `git tag -a myTagName -m "myTagMessage"`. Noticed that we did not give any commit hash to the tag function so it will take the last commit as the tag reference. For this project, use the name of the section you just completed as your tag name (i.e section1, section2 etc.). When you submit your code to your remote repository, remember to add `--tags` to your `git push` because it does not push the tags to the remote by default.


## Project structure
The project is split in 2 folders: `client/` and `server/`. All angular related codes will be put into the first, and all NodeJS code will be in the second.


## The server
NodeJS is a JavaScript runtime to be used on the server. You can do as much as you would do with Python or another scripting language. Node is used in conjunction with [NPM](https://www.npmjs.com/), his package manager.

In this project we are using Node for web development. In that context, we are going to use a web framework: [Hapi](http://hapijs.com/). There are other alternatives such as [Express](http://expressjs.com/) but that's not what we are going to learn.

### Node basic knowledge
NodeJS is based around the __module__ concept. I recommend you to read [that part](https://nodejs.org/docs/latest/api/modules.html#modules_modules) of NodeJS official documentation. In order to use these modules in our code, we need a __module loader__. Do not worry I am not going to ask you to write one, someone already did it: [RequireJS](http://requirejs.org/). It is already shipped within NodeJS so no need to worry about it.

#### Modules public API
Every JS file you create in NodeJS are wrapped in a module. If you want your module to be used somewhere else you need to define its **public API**. In other words, you will need to define the **functions/properties/objects** which people will have access to when requiring your module. To declare the public interface of your module you have to use the `module.exports` object available in all NodeJS modules. You can either affect something to `module.exports` or add properties to `exports` both are made available to your module by NodeJS. Here are some examples:

```javascript
// myApp/modules/answer.js

// you can affect a function to module.exports but you can affect pretty much anything
module.exports = function getUniversalAnswer() {
  return 42;
};

// if you require your Answer module in another file, you would get a function from your require call
// notice that I don't need to write the js extension when requiring the module
var answerModule = require('./answer');

console.log(typeof answerModule); // prints 'Function'
answerModule(); // returns 42

```

```javascript
// myApp/modules/otherModule.js

exports.getMyName = function() {
  return 'toto';
};


exports.getWeekDay = function() {
  return 'Monday';
};

// this time if you require this module in another file, you would get an object with 2 methods: getMyName() and getWeekDay().
var otherModule = require('./otherModule');

console.log(typeof otherModule); // prints 'Object'
otherModule.getMyName(); // returns 'toto'
otherModule.getWeekDay(); // returns 'Monday'

```

### Folder structure
Every NodeJS server has a main file from which the server is started. In this file you will find the server initialization and configuration. It is also the file which bootstraps your application - put all the pieces of your server together -. By convention it is called `index.js` but that's not a must-do. In our case it is called `server.js` and you can find it at the root of `server/`.

Now have a look at `server.js`. Use the previous section and [Hapi's official API](http://hapijs.com/api) to understand what it does.

### Basic commands
This section suppose you already have __Node__ and __NPM__ installed and configured on your local machine. If it is not the case, please refer to Dixeed's internal documentation.

NPM uses a file named `package.json` to store information about your project.

The commands below are to use in your own terminal and the current directory has to be the one where your `package.json` is.

1. `node index.js` to start your server. Another way of running your server is using NPM's script: `npm start` which is just an alias for the previous command. Prefer using the last as it is a way to standardize your server starting command.

2. `node` to access a Node CLI where you can execute JavaScript code. It is useful to make quick tests on something without writing a proper file for it.

3. `npm install [packageName]` to install a package in your project. It will be downloaded into the `node_modules` folder at your project root.

4. `npm install [packageName] --save` to install the package and mark it as a dependency of your project and respectively `--save-dev` for a development dependency. When using the above flags, the dependency will be written in your __package.json__ along with their [semver](https://github.com/npm/node-semver) version notation.

5. `npm install` to install all the dependencies written in your `package.json`. They will be downloaded into the `node_modules` folder at your project root.

#### Notes

NPM's commands are registered in the package.json scripts section of your project. Some keywords are registered for npm such as `start`, `test`, amongst others. If you want to use a script you defined that is not using NPM's keywords, you need to run: `npm run yourScriptName`.

### Section 1: Build your API endpoints
Simply put, a REST API is a collection of [Web Services](https://en.wikipedia.org/wiki/Web_service) accessible through the HTTP(s) protocol.

Let's now dive into our server requirements ... For the client to interact with our server, it has to call the Web Services methods to communicate with it. This methods are called __endpoints__. Here is the list of the endpoints we want to use:

* `/tasks` [GET] : returns a list of the tasks
* `/tasks/{id}` [GET] : returns the complete information of a task referenced by its id.
* `/tasks/create` [POST] : receives the data of the task to be created.
* `/tasks/delete/{id}` [DELETE] : delete the task referenced by the id.
* `/tasks/update/{id}` [PUT] : updates the information of the task referenced by the id.

Now that's your job to implement these routes! For the moment we are not using any database, we will dive into that later. If you need to manage data inside your routes as creating a new Task from the information you received or returning a Task according to its id, just use JS objects with dummy information. For example:
````javascript

var taskDb = {
  data: {
    1: {
      id: 1,
      title: 'My little task',
      content: 'I must wash my clothes before going to bed!',
      tags: ['before', 'bed', 'wash']
    },
    10: {
      id: 10,
      title: 'TODO',
      content: 'Clean the house, buy pizza, buy beer',
      tags: ['evening', 'todo', 'YAY']
    }
  },
  getById: function(id) {
    return this.data[id];
  },
  getAll: function() {
    var ret = [];
    for (var id in this.data) {
      ret.push(this.data[id]);
    }

    return ret;
  }
};

````

You could now use the object `taskDb`, as if it was a database, in your routes' handlers in order to retrieve, update, delete tasks from it. It is quite simple and does not manage all the things a real database engine does but you get the picture. In order to build all the endpoints mentioned above you have to create new methods as `getById(id)` and `getAll()` but for the missing behavior: update, delete and add.

Do not forget to handle the edge cases as well, what will you do if the id you received does not match any task inside your database?

In order to handle error cases or special cases, you can use [Boom](https://github.com/hapijs/boom). It is the HTTP error handling library developed by the Hapi team.

If you want to debug your API you can use [Postman](https://www.getpostman.com/) which is available as a Chrome app or a Mac App. If you prefer to test in your terminal, check out [curl](https://curl.haxx.se/).

You can find some help in [Hapi's routing tutorial](http://hapijs.com/tutorials/routing) and in [Hapi's API](http://hapijs.com/api).

Have you already finished section 1? If you do, remember to tag it before you go on to the next section. Please, refer to the [instructions](#instructions) if you forgot.

### Section 2 : As Mom said, "DO YOUR TESTS!"
Now that we have a server which accepts HTTP requests and responds accordingly, we need to write __tests__! I hear you out already: "Buuuttt we already tested our server using Postman as you suggested in the last section!!!". I know, I know, that's what I said. But! Are you going to run these tests by yourself every time you want to make sure it is still working as intended? Let me ask again, __ARE YOU SURE__ you want to run these tests __every time__ you add a feature to your server to check that it didn't break something?

![How-about-no bear](http://i1.kym-cdn.com/photos/images/original/000/129/577/1a4.jpg)

As your application grows in size, so does your test suites! Redoing every test by yourself is just a waste of time therefore we are going to write __unit tests__.

First thing first, we could just write some JavaScript files that require our `server.js` to test it however we need the tests to quit when the code does not match our requirements. For that matter we use an __assertion library__ that provides functions we can use that throws errors when things are not happening as we want them to. The library we use is [assert](https://nodejs.org/api/assert.html). It is included by default in NodeJS.

Another thing we do not want is: running ourselves every test one by one. We need a __test runner__. We are lucky! A lot has already been done on the matter and great tools already exists. We are going to use [Mocha](https://mochajs.org/). You need to add it to your server as a dev dependency (Psss, look [here](#basic-commands)).

We do not put our tests in the same folder as the code they are testing. You need to create a new folder called `test/` in `server/`. Inside this folder, just create an `index.js` file whose content is given below:

````javascript

'use strict';

var assert = require('assert');
var server = {};

describe('Task [endpoint]:', function() {

  // we clear the variable to make sure every test is independent from the others
  beforeEach('Set up the server', function() {
    server = require('../server');
  });

  afterEach('Close the server', function(done) {
    server.stop(function(err) {
      if (err) {
        return done(err);
      }

      done();
    });
    // could be simplified to server.stop(done);
  });

  it('should return tasks list when contacted on /tasks', function(done) {
    var options = {
      method: 'GET',
      url: '/tasks'
    };

    server.inject(options, function(response) {
      assert.equal(response.statusCode, 200);
      assert.equal(response.result.constructor, Array);
      done();
    });
  });
});

````

For this test to work you have to define your server instance as the public interface of your `index.js` module. So when you require `server.js` you get your server instance. Refer to [this](#modules-public-api) section for more information.

I just gave you the first test for your server. It tests your /tasks [GET] route. In order to fully understand the code, even though some parts are self-explanatory, you need some more information:

* http://hapijs.com/api#serverinjectoptions-callback
* https://mochajs.org/#synchronous-code. You will find what you seek from this point in their documentation.

You can already run this test with `npm test` as you may have only installed Mocha locally for your project. Otherwise you could use `mocha test` only if you installed it globally (`npm install -g mocha`) on your local machine. Mocha's output should print you all the tests in your `test/` folder whether they pass or not. All green? Yes, then you are good to go otherwise you need to correct your code.

Now that's your turn to write your own tests for the rest of your endpoints. Do not forget to test your edge case or error cases.

Don't forget to [tag](#instructions) your code once this section is completed.

### Section 3: Would you like some validation with it?
Validation is an important part of any development. Here we need to validate the data passed to your endpoints. Fortunately Hapi provides a validation library: [Joi](https://github.com/hapijs/joi). Routes configuration directly integrates Joi schema definition to apply on either path parameter, query parameter or payload. This time I am not going to give you the link of what I am referring to. You need to find it yourself on Hapi's website. Once you know what you need to do, just add validations to your endpoints.

Do not forget to update your tests accordingly. Just add 1 or 2 tests for each endpoints so you get the idea but we do not need you to test every edge case for this training.

[Please, tag it!](#instructions)

### Section 4: Go get me some real data!
So far we have been using dummy data in our server but that would be nice if we could actually save the tasks we receive in order to send them back when asked for it.

We want to use an ORM so we can have __DAO__ (Data Access Objects) and ease the interaction with the database. We will be using [Sequelize](http://docs.sequelizejs.com/en/latest/docs/getting-started/). You need to install the npm package and its dependencies based on the database you want to use. In this project we are going to use PostgreSQL, refer to the link above for the installation steps.

Now, we need to make the link between our Hapi server and Sequelize. Hapi provides a built-in plugin system to allow better code reuse and code separation. It offers a lot of different [plugins](http://hapijs.com/plugins) made by the community or the Hapi team. Here is a [plugin](https://github.com/danecando/hapi-sequelize) to make the link between the 2. You need to install it and mark it as a project dependency. Once installed, you need to explicitly tell your server that it needs to load that particular plugin. It is done through the `server.register()` [function](http://hapijs.com/api#serverregisterplugins-options-callback). Now, call this method in your `server.js` and pass the [needed options](https://github.com/danecando/hapi-sequelize#configuration) to the plugin in order to access your database.

So far we have a working connection to our database but nothing in it. We need to define a model that maps our __Task__ entity. There is an [example](https://github.com/danecando/hapi-sequelize/blob/master/test/models/blog/blog.js) in the hapi-sequelize plugin. If you need more information you can find it [in Sequelize documentation](http://docs.sequelizejs.com/en/latest/docs/models-definition/). It would be better to put the model definition in its own file (so you could tell to the plugin where to find it through the `models` options). Create a `model.js` for that matter. The database `sync` Sequelize call should now create a table Task in your database.

As we have created our model, the endpoints should use it instead of dummy data. You can access your model in your request handlers through hapi-sequelize [API](https://github.com/danecando/hapi-sequelize#api).

Before you dive into models operation, I suggest that you read the next section.

#### I promise it will not hurt!
The next step is to update your handlers for them to use the model in order to create/update/delete the proper Task data. When dealing with Sequelize you have to use __Promises__ to control asynchronous control-flow. Simply put, a promise is like an engagement for a value. A promise always returns a value (object, primitive, array ...). It can be either pending, resolved or rejected, it can be only in one of these state. A promise can be chained with some functions, I will show you the 2 most used: `then()` and `catch()`. The former is called when the promise it is chained to is resolved and the latter is called when the promise is rejected. You can chain `then()` and `catch()` call because both of this function return a promise. Here is an example:

````javascript

functionThatReturnsAPromise().then(function(value) {
                                console.log('YAY!');
                            })
                            .catch(function(err) {
                                console.log('Doh');
                            });
````

`YAY!` will be printed only if the promise returned by `functionThatReturnsAPromise()` is resolved. If it is not resolved but rejected instead, `Doh` will be printed.

Let's take a look at a more complex example:

````javascript

functionThatReturnsAPromise().then(function(value) {
                                return anotherFunctionThatReturnsAPromise();
                            })
                            .then(function(secondValue) {
                              return 4;
                            })
                            .then(function(integer) {
                              console.log(integer);
                            })
                            .catch(function(err) {
                                console.log('Doh');
                            });
````

This time if the first promise returned by `functionThatReturnsAPromise()` is resolved we go into the first `then()` call which performs another asynchronous operation. That operation returns a promise as well so we can return the result of that function. As the first `then()` call returns a promise, it can be chained with another `then()`. In the second call we just return a primitive. _"How could we chain another `then()` in that case?"_. Well, `then()` and `catch()` always return a promise even though you did not return one explicitly. Internally it will wrap your returned value into a Promise. In that case, the third `then()` call will print `4`.

Let's say the call to `anotherFunctionThatReturnsAPromise()` returns a rejected promise then the next 2 `then()` calls will be skipped and `catch()` will be called. It would be the same if a JavaScript error is thrown in a `then()` handler. One thing to remember is that any promise chain __should always be terminated by a `catch()` call__ and when I say always I mean __ALWAYS__! If you do not put one then your error will go silent and you will never know that there was even one to begin with!
![Good luck meme](https://blog.homeceuconnection.com/wp-content/uploads/2014/11/goodluck.jpg)

Bear in mind that this little introduction on Promises was only meant for you to know how to use them. We did not see how to create a Promise ourselves nor other operations you can perform on them. If you want to learn more, [here](https://github.com/wbinnssmith/awesome-promises) is a list of resources you can read.

Now that you start to grasp how Promises can be used, you need to implement the different models operation into your request handlers. Refer to [this](http://docs.sequelizejs.com/en/latest/docs/models-usage/) and [this](http://docs.sequelizejs.com/en/latest/docs/instances/). Don't forget to update your unit tests and to tag your code once it is completed.

After that you will have a working basic REST API server!

![Congrats meme](http://cdn.meme.am/instances/400x/55597118.jpg)

### Section 5: Make it beautiful, make it a plugin!
Did you think it was over? Well, __NO__!

So far we have put all our code into our `server.js` which is fine for a short training project as this one but we want you to be ready for the _real world_! Imagine if you have 50 endpoints on your server, would you put all of them in your `server.js`? Of course not! Here we are going to take advantage of Hapi's great plugin system.

First, you need to create a folder `server/lib` which contains all our plugins. In this folder, you will create another folder `task`. Create an `index.js` file in it. Get inspired from [Hapi's plugin tutorial](http://hapijs.com/tutorials/plugins) to create your own plugin. You need to put all the code relative to the Task entity in this plugin. Something you need to ask yourself when making a plugin is: "Can I use it in another project only by taking this plugin code?". The answer should be yes. It should be standalone. It should not require anything external to work (except external configuration values of course). You can even split your routes definition code by taking out the handlers in another NodeJS module, let's say `handlers.js`. You would then require that file into your `index.js` to use it. Splitting your project into plugins should make it easier to write tests because there are no tight dependencies. Each plugin can be tested on its own. You could test the route definition on their own and also test the handlers on their own. As I think you have guessed it, you need to update your unit tests. Your `test/` folder should replicate your `lib/` folder structure so if you create a `task/` folder in `lib/` do it as well in the test folder and put all the test related code of Task in it.

The end goal of splitting your codebase to plugins is to reuse them in other projects. You could upload these plugins on a private NPM server and install them as you do for any other NPM libraries. In order to do that, each plugin would require to have its own `package.json` to specify all the dependencies. It is not the purpose of this training but that's something worth keeping in mind.

Once you have created your plugin remember that you need to load it in the main `index.js` and remember to tag the code once done.

### Section 6: Externalize your configuration
So far we have multiple configuration values throughout our project. If we need to reuse some data we would need to duplicate them which is far from ideal. Fortunately Hapi provides a configuration library: [Confidence](https://github.com/hapijs/confidence). We will use this lib to regroup all our configuration data such as the database connection information. In the future when we need to make a change, there is only one place to look at. In addition there are some data that should be hidden from your VCS (Version Control Software), in our case Git. An example of confidential data would be cryptographic information (authentication key, etc.), or your database credentials.

For us to achieve that we will need a JSON file where the confidential data will be stored and we will add this file to our `.gitignore` at our project root. Our configuration file will use this _ignored_ file to get the information needed but the exact value will be hidden to Git. The rule of thumb here is that you never know how your project will evolved. Perhaps at the beginning of your project your repository is private and only trusted person have access to it. In that case we do not care that sensible data are versioned however your repository might not stay private forever. At some point in your application lifetime, you might want to open-source it but we do not want people to access our sensitive data from our Git repository history.

Do not forget that Confidence is a package that needs to be installed through NPM and marked as a dependency of your project. Regarding Confidence-related code, we will put it inside a `config/` folder at the project root. Create an `index.js` file inside and put the content shown below:

````javascript

'use strict';

var Confidence = require('confidence');
var dbCredentials = require('./database/credentials');
var store;
var criteria;

exports.get = function(key) {
  return store.get(key, criteria);
};

store = new Confidence.Store({
  database: {
    credentials: {
      $filter: 'env',
      dev: {
        dbName: dbCredentials.dev.database,
        user: dbCredentials.dev.user,
        pass: dbCredentials.dev.pass,
        dialect: dbCredentials.dev.dialect,
        host: dbCredentials.dev.host,
        port: dbCredentials.dev.port
      },
      test: {
        dbName: dbCredentials.test.database,
        user: dbCredentials.test.user,
        pass: dbCredentials.test.pass,
        dialect: dbCredentials.test.dialect,
        host: dbCredentials.test.host,
        port: dbCredentials.test.port,
      }
    }
  },

  server: {
    host: process.env.COMPARE_FOOD_HOST || 'localhost',
    port: process.env.COMPARE_FOOD_PORT || 8890
  }
});

criteria = {
  env: process.env.NODE_ENV || 'prod'
};

````

In the `config/` folder, we have a sub-folder `database/` used to store database related information. It contains a `credentials.json` file which is ignored by Git and that stores the sensitive data as explained earlier. The structure of this file is as follow:

````json

{
  "dev": {
    "database": "dev",
    "user": "userDev",
    "pass": "myBigPassword",
    "dialect": "postgres",
    "host": "localhost",
    "port": 1010
  },
  "test": {
    "database": "test",
    "user": "userTest",
    "pass": "myBigPassword",
    "dialect": "postgres",
    "host": "localhost",
    "port": 1010
  }
}
````

The JSON object is composed of 2 properties: `dev` and `test`. This structure allows us to use Confidence `$filter` feature. This feature allows to have conditional values depending on a parameter. You have an exemple in the JavaScript code above.

Now remove the database configuration information from your `server/` code and use Confidence instead to access the information. Look through your code if there are other values that you could export into Confidence as well and do it if so.

Your server is now ready to be used by a client. We will use create a client in the next part of this project.

## The client (under construction)
![under construction](http://dixeed.com/wp-content/uploads/2016/01/Under_Construction-300x300.png)
