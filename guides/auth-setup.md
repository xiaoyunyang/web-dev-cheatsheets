# Authentication and Database Setup

## Goal
We want to build a `react` and `node` app with user authentication via `passport`, `mongo` for database, and `mongoose` to connect the app to the database. 

## Middleware

**Authentication**

* [`passport`](http://www.passportjs.org/), which help us authenticating with different methods includes these add-ons to help us authenticate with different methods:
	* [`passport-local`]()
	* [`passport-facebook`]() 
	* [`passport-google-oauth`]() 
	* [`passport-github2`](https://www.npmjs.com/package/passport-github2)
* [`passport-local-mongoose`](https://github.com/saintedlama/passport-local-mongoose)
* [`dotenv`](https://github.com/motdotla/dotenv)
* [`nodemailer`](https://github.com/nodemailer/nodemailer)
* [`body-parser`](https://github.com/expressjs/body-parser) -  parsing `POST` request bodies to get information from html forms. For example, when a user submits a form.

**Session**

* [`express-session`](https://github.com/expressjs/session)
* [`cookie-parser`]() - read cookies from users (needed for auth). This needs to be paired with another Express-supported middleware like `express-session`. Once you've done this, you can keep track of users, providing them with user accounts and other features.
* [`cookie-session`](https://github.com/expressjs/cookie-session) - stores the session data on the client within a cookie, while a module like `express-session` stores only a session identifier on the client within a cookie and stores the session data on the server, typically in a database.

**Database**

* [`mongoose`](http://mongoosejs.com/docs/connections.html)
* [`mongo`](https://www.tutorialspoint.com/mongodb/mongodb_data_modeling.htm)
* [`knex`](http://knexjs.org/) - SQL query builder for Postgres. Our app uses `knex` to interact with the database.

**Security**

* [`bcrypt-nodejs`](https://www.npmjs.com/package/bcrypt-nodejs) - used to hash strings and compare the hash with the hash stored in the database
* [`crypto`](https://nodejs.org/api/crypto.html) - nodes's built in module for hashing. Deprecated.
* [`helmet`](https://github.com/helmetjs/helmet) - Helps to secure your applications. It doesn’t magically make you more secure, but a small amount of work can protect you from a lot of hacks.


**Utilities**
	
* [`connect-flash`]() - allows for passing session flashdata messages.
* [`morgan`](https://www.npmjs.com/package/morgan-2) - log every request to the console
* [`dotenv`](https://github.com/motdotla/dotenv)
* [`compression`](https://github.com/expressjs/compression) - Compression for compressing responses to save on bytes
* [`connect-assets`](https://github.com/adunkman/connect-assets) —Compiles and minifies your CSS and JavaScript assets. It will also work with CSS preprocessors like SASS, SCSS, LESS, and Stylus, should you choose to use them.

## Set up the `User` model
You want to sign up users and log users in to use your website. We need to create a `User` object. From your project directory:

```
$ mkdir models
$ touch models/User.js
```
The `User` model will include logic for authentication, including:

* Hooking up to a `users` table in the postgresql database
* When a new user signs up, create a new `User` with the information provided by the user and saving all that information into the `users` table in the database. We don't want to save the raw password into the database because if your database gets hacked, the hacker will have the login information of all your users. A mitigation for that is to hash the password with secure hash algorithm (SHA) and store the hash into the database.
* Next time returning user tries to log in with the password, you hash the password and compare the hash of the password with the stored hash in the database. If they match, the user is authenticated and gets logged in. If the hashes don't match, then the user can click on a "forgot my password" link to reset the password. Note, we can't tell the user what the password is because SHA is a one way function, meaning it is not possible to guess what the original password is using just the hash.
* Dependencies to achieve these goals:
	* [`knex`](http://knexjs.org/) - SQL query builder for Postgres. We use `knex.js` to interact with the database.

		```
		$ mkdir models/dbconfig
		$ touch models/dbconfig/bookshelf.js
		$ touch models/dbconfig/knexfile.js
		$ touch models/dbconfig/.env
		```
**What is salt and what it's good for?**

> [Salt](https://www.wikiwand.com/en/Salt_(cryptography)) is random data that is used as an additional input to a one-way function that "hashes" data, a password or passphrase. Salts are closely related to the concept of nonce. The primary function of salts is to defend against dictionary attacks or against its hashed equivalent, a pre-computed rainbow table attack

> [Rainbow tables](https://www.wikiwand.com/en/Rainbow_table) are one tool that has been developed to derive a password by looking only at a hashed value.
> 
> It is a practical example of a space–time tradeoff, using less computer processing time and more storage than a brute-force attack which calculates a hash on every attempt, but more processing time and less storage than a simple lookup table with one entry per hash. Use of a key derivation function that employs a salt makes this attack infeasible.

**More on cryptographic hash functions**

* You can think about a hash digest as a fingerprint for the data. 
* A hash digest is the output of a hash function. A hash function takes an input and produces a unique output. A cryptographic hash function such as SHA-256 is designed to be a one way function and has the property that any small change to the input will produce a large and unpredictable change to the output. 
* It is not computationally feasible to deduce the original data from the hash digest generated by the secure hash algorithm. The only way to get the original data is by brute forcing or make a space time tradeoff and mount a rainbow table attack. 
* SHA-256 always outputs 256 bits for any size input. This translates to 64 hexadecimal characters.
* Note:  There are things you can do such as add a salt to your hash calculation to mitigate a rainbow table attack. But we are going off topic. 
* This [stackoverflow response](https://stackoverflow.com/questions/28942244/when-to-use-salt-with-message-digest) explains the salt very well:
> You can use “[salt](https://en.wikipedia.org/wiki/Salt_%28cryptography%29)”, which refers to a small, random input to the hash function that is used to alter the state of the function prior to adding additional input that may be predictable in some way. This is a security mechanism that was developed for protecting passwords when using a message digest as a password verifier function. If a salt is not used, then any users having the same password will have the same password hash stored in the user database. Enormous efficient hash reversal tables ("[rainbow tables](https://www.wikiwand.com/en/Rainbow_table)") exist for the most common message digest functions used in this manner (MD5, NTLM password hash, etc.), and an attacker who obtains the database has only to perform a table lookup to obtain the plaintext password of every user. Using a salt prevents the generation of these tables, since each byte of salt results in a 256x size increase of the lookup table.
> A message digest (also known as a "hash") is the output of a digest or [cryptographic hash function](https://www.wikiwand.com/en/Cryptographic_hash_function), which is a one-way fixed-size-output compression function having the property that small changes to the input (message) result in large, unpredictable changes in the output digest. How to use such a function depends greatly on what you are trying to do with it

## Set up the Authentication Process

**What is authentication?**

It's a way to verify the person visiting your webpage has the right credentials to access the webpage. Credentials are extracted from cookies, sessions, and user login.


**What is passport?**

`passport` is an authentication middleware for node. Passport implements the boilerplate code for authenticating the user so you don't have to write the boilerplate code yourself. 

**Passport Setup**

1. Create a config file for passport
	```
	$ touch config/passport.js
	```
2. Follow the scotch.io tutorials to populate the `config/passport.js` file to use different strategies to log in/sign up
	* [Set up local](https://scotch.io/tutorials/easy-node-authentication-setup-and-local)
	* [Set up oAuth](https://scotch.io/tutorials/easy-node-authentication-facebook)

	The `config/passport` file will end up looking like this after you set passport up to use both local and facebook strategies:
	
	```javascript
	// config/passport.js
	
	const LocalStrategy = require('passport-local').Strategy;
	const FacebookStrategy = require('passport-facebook').Strategy;
	const User = require('../models/user');
	
	// You are exporting a function that takes passport as 
	// an argument and returning
	module.export = (passport) => {
	
	  /** passport session setup **/
	  // required for persistent login session
	  passport.serializeUser((user, done) => { /* TODO */ }
	  passport.deserializeUser((id, done) => { /* TODO */ }
	  
	  /** Local Signup **/
	  passport.use('local-signup', new LocalStrategy( /* TODO */ ))
	  
	  /** Facebook Signup **/
	  passport.use('facebook-signup', new FacebookStrategy( /* TODO */ ))	 
	}
	```
3. We pass this `config/passport.js` file into `server.js` to configure `passport`. `server.js` contains the following code:
	
	```javascript
	// server.js
	
	const passport = require('passport')
	require('./config/passport')(passport);
	
	```

**Set up passport with auth0**

[https://github.com/auth0/passport-auth0](https://github.com/auth0/passport-auth0)

## Set up Open Authentication With Passport

To use any of the included OAuth providers (e.g. Facebook, Twitter, Google), you will need to **obtain API keys**. I have included "throw-away" API keys for all OAuth providers to get you up and running quickly, but be sure to update them with your own keys.

### Facebook
[Read the official doc](https://developers.facebook.com/docs/facebook-login/web) and Check out the [Facebook Developer help community](https://www.facebook.com/help/community/question/?id=237899156642623)

1. Go to [Facebook Developers](https://developers.facebook.com/). Click on **My Apps** dropdown, then select **Add a New App**.
2. Select **Website** platform, then click on **Skip and Create App ID** button. 
	- Enter a **name** and choose a **category** for your app. 
	- Click on **Create App ID** button. 
3. Under Products > Facebook Login > QuickStart, click on **+ Add Platform** button. Facebook will ask you "Tell us what the URL of your site is". Make sure Site URL is `http://localhost:3000/auth/facebook/callback`.
4. Under Products > Facebook Login > Settings, 
	- ensure Valid OAuth redirects URIs is `http://localhost:3000/auth/facebook/callback`, which should match Site URL from the previous step.
	- Make sure you turn on "Client OAuth Login" and "Web OAuth Login"
4. Copy and paste **App ID** and **App Secret** keys into `.env` file: 
	 - `FACEBOOK_ID='YOUR_APP_ID'` 
	 - `FACEBOOK_SECRET='YOUR_APP_SECRET'`

### Google
1. Go to [Google Cloud Console](https://cloud.google.com/console/project), then select **Create project**. 
	- Enter a **Project name**, then click on **Create** button.
	- Click on **Use Google APIs** (Enable and manage APIs) panel. 
2. Click on **Credentials** tab in the sidebar. Click on **Create credentials** dropdown, then select **OAuth client ID**. Select or enter the following: 
	- **Application type**: `Web application` 
	- **Authorized JavaScript origins**: `http://localhost:3001` 
	- **Authorized redirect URIs**: `http://localhost:3001/auth/google/callback` 
	- Click on **Create** button. 
3. Copy and paste **client ID** and **client secret** keys into `.env` file: 
	- `GOOGLE_ID='YOUR_CLIENT_ID'` 
	- `GOOGLE_SECRET='YOUR_CLIENT_SECRET'`

### Twitter
1. Go to [Twitter Application Management](https://apps.twitter.com/). 
2. Click on **Create New App** button. Fill out required fields. 	- **Callback URL**: `http://127.0.0.1:3001/auth/twitter/callback` 
3. Go to **Settings** tab. 
	 - Click on **Allow this application to be used to Sign in with Twitter** checkbox. 
	 - Click on **Update Settings** button. 
4. Go to **Keys and Access Tokens** tab. Copy and paste **Consumer Key** and **Consumer Secret** keys into `.env` file: 
	 - `TWITTER_ID='YOUR_CONSUMER_KEY'` 
	 - `TWITTER_SECRET='YOUR_CONSUMER_SECRET'`

### Github
1. Go to [Github Developer Applications Settings](https://github.com/settings/developers) 
2. Click on **Register a new application** button. Fill out required fields. 
	- **Application Name** 
	- **Homepage URL** 
	- **Callback URL**: `http://127.0.0.1:3001/auth/github/callback` 
	- Click on **Register application** 
3. Copy and paste **client ID** and **client secret** keys into `.env` file: 
	- `GITHUB_ID='YOUR_CLIENT_ID'` 
	- `GITHUB_SECRET='YOUR_CLIENT_SECRET'`

Note: If you are using React or AngularJS, copy and paste client secret into .env file and client ID into app/actions/oauth.js (React) and app/app.js (AngularJS).

## Integration With React Router

Check out [this tutorial](https://tylermcginnis.com/react-router-protected-routes-authentication/)

[This Stackoverflow post](https://stackoverflow.com/questions/43164554/how-to-implement-authenticated-routes-in-react-router-4) suggests creating a `PrivateRoutes` component:


Same Tutorial is found here: [React Training](https://reacttraining.com/react-router/web/example/auth-workflow) 

[`react-router-config`](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-config)

* According to the [documentation](https://reacttraining.com/react-router/web/example/route-config), you can do something like this:

	```javascript
	const routes = [
	  { path: '/sandwiches',
	    component: Sandwiches
	  },
	  { path: '/tacos',
	    component: Tacos,
	    routes: [
	      { path: '/tacos/bus',
	        component: Bus
	      },
	      { path: '/tacos/cart',
	        component: Cart
	      }
	    ]
	  }
	]
	
	// wrap <Route> and use this everywhere instead, then when
	// sub routes are added to any route it'll work 
	const RouteWithSubRoutes = (route) => (
	  	<Route path={route.path} render={props => (
	  		// pass the sub-routes down to keep nesting
	    <route.component {...props} routes={route.routes}/>
	  	)}/>
	)
	
	```
`react-router-config` issues:
* [Issue 5138](https://github.com/ReactTraining/react-router/issues/5138)
* [Issue 4962](https://github.com/ReactTraining/react-router/issues/4962)

[Tutorial](https://www.mokuji.me/article/react-auth0) for implementing react-node app with authentication using auth0.


## Set up your Database

### MongoDB
* Install MongoDB:  

	```
	$ brew install mongodb
	```
* Create the data directory: 

	```
	$ sudo mkdir -p /data/db
	```
* Set permissions for the data directory: 
	
	```
	$ sudo chown -R `whoami` /data/db	
	```
* Run MongoDB server in one terminal and then MongoDB shell in another terminal: 
	
	```
	$ mongod
	```

	```
	$ mongo
	>
	```
	
* Install [Mongo.app](http://mongoapp.com/), which lets you launch an application that runs in the menu bar at the top right of your screen. You can tell when it’s running and when it’s not, easily start up a console, and shut it down effortlessly.

* Things you can do in the mongoDB shell ([Quick Reference](https://docs.mongodb.com/manual/reference/mongo-shell/))

	```
	> show dbs  $ see all available databases
	> use test $ switch to db test
	> db $ see which data you are using currently
	> show collections $ list all collections inside current db
	users
	> db.users.find() $ in the users collection, return all documents
	> db.users.remove( { } ) $ remove all documents in the users collection
	> db.users.remove( { index } )
	> db.users.dropIndexes()

	```

**Resources**
* [Azat Marda's Cheatsheet](https://github.com/azat-co/cheatsheets/tree/master/mongodb-mongoose)
* [Little Mongo Handbook](http://openmymind.net/mongodb.pdf)
* MongoDB's [official documentation](https://docs.mongodb.com/manual/mongo/)


### PostgreSQL

##### Install Postgres
Using Homebrew:  `$ brew install postgres`
##### Start Postgres
* Automatic:
	* `pg_ctl -D /usr/local/var/postgres start && brew services start postgresql` - This makes Postgres start every time your computer starts up. Execute the following command
	* `brew services start postgresql` - To have launchd start postgresql now and restart at login
* Manual
	* Start Postgres: `$ pg_ctl -D /usr/local/var/postgres start`
	* Stop Postgres: `$ pg_ctl -D /usr/local/var/postgres stop`

##### Database Management
* `> createdb looseleaf` - creates a database called looseleaf
* `> dropdb looseleaf` - delete the database called looseleaf
* `> psql looseleaf` - run the database called looseleaf. After you type this, you'll see this: `looseleaf=#`, which is the header for the postgres database interface. Type the command after the `#`. For example:
	* `looseleaf=# \du` - see what users are installed
	* `looseleaf=# \h` - help
	* `looseleaf=# \q` - quit
If you want to use a PostgresQL GUI, install and launch [Postico](https://eggerapps.at/postico/). Look up the User name using `looseleaf=# \du`.

Integrate Postgres with your Node app:

*  Link database to your app: In the `.env` file in the root directory of the app, edit the DB_NAME line to say `DB_NAME='looseleaf'` and change `DB_USER` to the name as appeared when you run the `looseleaf=# \du` command above.
* If you don't start the database before you run `npm start`, then you will get a "Knex: Error Pool 2 - Error: connect ECONNREFUSED" error.

## Connect App To Database
we can use [`mongoose`](http://mongoosejs.com/) to provide database access for our application.

* [Mozilla Tutorial for Express-Nodejs-mongoose setup](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/mongoose)
* [mongoose getting started](http://mongoosejs.com/docs/index.html)
* [Connect mongoose to mongodb](http://mongoosejs.com/docs/connections.html)
 
[mongoose Schemas](http://mongoosejs.com/docs/guide.html) 
 > Do not declare methods using ES6 arrow functions (=>). Arrow functions [explicitly prevent binding](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_binding_of_this) this, so your method will not have access to the document and the above examples will not work.
 

## Debugging
* [`node-inspector`](https://github.com/node-inspector/node-inspector)
	* node-inspector is great, but due to a [current bug](https://github.com/node-inspector/node-inspector/issues/905), it won’t work on any version of Node higher than 6.3.1.
	* [This article](https://team.goodeggs.com/visual-debugging-with-es6-and-node-js-44631b3b040f) discusses options for debugging in Node, in particular `node-inspector` and JetBrains Webstorm.
* [`node-devtools`](https://www.npmjs.com/package/node-devtools) - not working
* [Official doc for create-react-app](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#debugging-in-the-editor)

## Babel

* [What is Babel?](https://kleopetrov.me/2016/03/18/everything-about-babel/)
* [How to set up Babel](http://www.react.express/babel)

	```
	$ npm install --save-dev babel-loader babel-core babel-preset-react babel-preset-env babel-preset-stage-1 babel-plugin-transform-runtime
	$ npm install --save babel-runtime
	```
* `babel-preset-es2015` is deprecated. Use `babel-preset-env` instead. [Read about it here](http://babeljs.io/env)

`babel-preset-env` node [not working](https://github.com/facebookincubator/create-react-app/issues/1125) in create-react-app




## WebApp Environmental
Accessing environment variables in Node.js is supported right out of the box. When your Node.js process boots up it will automatically provide access to all existing environment variables by creating an `env` object as property of the `process` global object. If you want to take a peek at the object run the the Node.js REPL with `node` in your command-line and type:

```javascript
console.log(process.env);
``` 
You should see that the value of `PORT` is `undefined` on your computer. Cloud hosts like Heroku or Azure, however, use the `PORT` variable to tell you on which port your server should listen for the routing to work properly. Therefore, the next time you set up a web server, you should determine the port to listen on by checking `PORT` first and giving it a default value otherwise:

```javascript
const app = require('http').createServer((req, res) => res.send('Ahoy!'));

// take the value of the PORT if it’s available or default 
// to 3000 as a fallback port to listen on.
const PORT = process.env.PORT || 3000;
 
app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

According to node's documentation on the [`process-env`](https://nodejs.org/docs/latest/api/process.html#process_process_env)
>It is possible to modify this object, but such modifications will not be reflected outside the Node.js process.

If you want to separately define your environmental variable, then install `dotenv` and use the following lines in your `server.js`.

What it is:
>`Dotenv` is a zero-dependency module that loads environment variables from a `.env` file into `process-env`.

Why you shoud use it:
>`Dotenv` is a simple way to allow you to create secret keys that your application needs to function and keep them from going public.
 
See more on why you should use of [`Dotenv`](https://github.com/motdotla/dotenv) from [this article](https://medium.com/@thejasonfile/using-dotenv-package-to-create-environment-variables-33da4ac4ea8f).


## More Things to Checkout
[Sample App](https://github.com/aybmab/express-redux-sample)
	
**A User System**
Use `passport` to implement a user system. Essentially, a session token is generated when a client connects, it is then associated with an account if the user successfully signs on and saved to a store (currently the dev session store, but soon to be redis - though it could also be saved in the DB). The token is then used to authorize subsequent requests.

**Redux**
Everytime something happens, a new state object is created to reflect the change, and the views update accordingly.

**Optimistic Updates:**
	
> After having a redux application connected to a backend, I wanted to implement optimistic updates (a.k.a. reflect user updates immediately, even though the change wasn't necessarily saved). This was implemented by generating a unique id on the client side and then using that to reconcile after hearing back from the server. By using the client-side-generated id, react nicely handles updating the view and notifying the user on the status of each change. 

**Live Update/Push Notification**
> After users were able to make changes, I didn't want them to have to refresh their page to see changes made by other users. I used [SocketIO](https://socket.io/) to alert each client of any update. Please let me know what you think about this! I've never used backbone, but it seems to have a nice model and event system that could be worth exploring.

**Sessions**
Sessions are basically cookies that also gives you the ability to define the backend storage used by the server part of your application
	
**Client Side Routing**	


## Resources
* Why [open authentication](https://scotch.io/tutorials/the-easiest-way-to-add-authentication-to-any-app)?
* [`passport-mongo-local`](https://github.com/saintedlama/passport-local-mongoose)
* [Connect mongoose to mongodb](http://mongoosejs.com/docs/connections.html)
* Passport Tutorial
	* Scotch.io - [set up passport-local](https://scotch.io/tutorials/easy-node-authentication-setup-and-local) 
	* Scotch.io - [set up passport-facebook](https://scotch.io/tutorials/easy-node-authentication-facebook) 
	* DJAMware - [Set up passport with facebook twitter, Google, and Github login](https://www.djamware.com/post/59a6257180aca768e4d2b132/node-express-passport-facebook-twitter-google-github-login)
* [node passport and postgres setup](http://mherman.org/blog/2016/09/25/node-passport-and-postgres/) - Good tutorial on integrating `passport` with `postgres` and  `knex`
* [postgres with passport](http://uitblog.com/postgres-with-passport/)
* [node passport and postgres](https://reallifeprogramming.com/node-authentication-with-passport-postgres-ef93e2d520e7)
* [codeMentor](https://www.codementor.io/devops/tutorial/getting-started-postgresql-server-mac-osx) - Getting Started Tutorial for postgresql
* [node passport and postgres setup](http://mherman.org/blog/2016/09/25/node-passport-and-postgres/)
* [node passport and mongo setup](http://mherman.org/blog/2013/11/11/user-authentication-with-passport-dot-js/#setup) 
* [postgres with passport](http://uitblog.com/postgres-with-passport/)
* [TutorialsPoint MongoDB Tutorial](https://www.tutorialspoint.com/mongodb/mongodb_overview.htm)
* [MongoDB official documentation](https://docs.mongodb.com/manual/mongo/)
* [Postgres vs Mongo - Youtube video](https://www.youtube.com/watch?v=eM7hzKwvTq8)
* [best practice for handling sessions in redux](https://github.com/reactjs/redux/issues/297)