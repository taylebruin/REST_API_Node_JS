

# Lab 6A - Node.js RESTful API

## Overview

You've created your Todo app in three different frameworks so far:

1. Vanilla HTML, CSS, and JavaScript
2. PHP
3. Microsoft's .Net Core MVC Framework with Entity Framework

Now, you'll be creating what's called a "__RESTful API__" to handle all of your requests for you. __REST__ stands for **RE**presentational **S**tate **T**ransfer. It is an architectural style for web systems. __API__ stands for **A**pplication **P**rogramming **I**nterface, and ours (since it's RESTful) communicates with any front-end through the use of HTTP requests.

A simpler explanation is that it's a todo app, but the only way to interact with it is to make HTTP requests, instead of clicking things on an interface of some kind.

Our basic CRUD operations have their own HTTP verbs:

| CRUD Operation | HTTP Verb |
| -------------- | --------- |
| Create         | POST      |
| Read           | GET       |
| Update         | PUT       |
| Delete         | DELETE    |

These can and probably should all be utilized, but you'll find that most people will just use `GET` and `POST` for most things in the real world. [You can see some of the most used verbs here](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods).

The beauty of setting up an API is that there is no front-end. That means that you can create a front-end on any device or any framework, and they can all communicate with the same database! After this lab, you'll be able to create an app on your phone, or a website, or an app on a Samsung Smart Refrigerator if you want!

### Functionality

- Running API that handles RESTful HTTP calls to modify a database

### Concepts

- REST services
- HTTP Verbs
- Express and Express Middlewares
- NPM (Node Package Manager)

### Resources

- [Build A RESTful Api With Node.js And Express.Js Part One](https://medium.com/@purposenigeria/build-a-restful-api-with-node-js-and-express-js-d7e59c7a3dfb)
- [Building A REST API With MongoDB, Mongoose, And Node.js](https://www.thepolyglotdeveloper.com/2019/02/building-rest-api-mongodb-mongoose-nodejs/)
- [Quickstart: Use Node.js to query an Azure SQL database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connect-query-nodejs?tabs=windows)
- [SQL Database application development overview](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-develop-overview)
- [Tedious - Getting Started](http://tediousjs.github.io/tedious/getting-started.html)

### Assignments

Lab Writeup Instructions are in the "Content" tab in [Learning Suite](http://learningsuite.byu.edu).

## Instructions

### Step 1: Setup Your Project

We'll be returning to Visual Studio Code for this lab, because of its simplicity and the availability of tools for Node.js applications.

First, you'll want to clone the lab-6a git-hub repo from Learning Suite that contains the starter files for this lab onto your computer.

#### Install Postman

[Postman](https://www.getpostman.com/products) is a great, free tool that helps with developing APIs. Due to the nature of an API not having a UI, it's difficult to test your endpoints. You can use a web browser for `GET` requests, but not for `POST`, `PUT`, or `DELETE` requests.

[Postman](https://www.getpostman.com/products) has a UI that allows you to specify what the body of the HTTP request will be, which will be necessary for us.

#### Install Node.js

Go to the [Node.js downloads page](https://nodejs.org/en/download/) and install Node.js to your development machine

* If you think it's already installed, you can type `node --version` into a command prompt
* If it's installed, it will return the version, otherwise it will say it doesn't recognize the command
* If you still can't run the command, make sure that you have the Environment Variable set up
    * You can look at [this guide](https://helpdeskgeek.com/windows-10/add-windows-path-environment-variable/) for help

#### Use the Template

1. Clone the repo that you accepted from Learning Suite onto your computer and open it in VSCode

> Note: Before moving on, you'll need to make sure you have a `.gitignore` file for this directory. This file will tell Git that you don't want to save anything in the `node_modules` folder, which gets [very](https://i.redd.it/tfugj4n3l6ez.png), [very](https://img.devrant.com/devrant/rant/r_1546733_HyZ3h.jpg), [very](https://pics.awwmemes.com/a-modern-small-web-app-with-node-modules-installed-addtext-com-41803670.png), [very large](https://pics.esmemes.com/thomas-fuchs-thomasfuchs-follow-legendary-apollo-project-programmer-margaret-hamilton-44625978.png).

#### Add Dependencies

We've provided a project `package.json` for you, which contains much of the setup and scripts that you'll need for this project.

1. Open a shell/command prompt to your project directory and run the following command:

    ```sh
    npm i
    ```

    > Note: `i` is shorthand for `install`. You can also run `npm install` if you like wasting your precious time typing instead of coding. ;)

2. Install the dependencies in the table below using the command:
   ```sh
    npm i <name of dependency from table>
    ```

    > **Dependencies** are other JavaScript files, or **Libraries**, that we can use for our API. As you'll learn, there are lots of different ways of saying the same thing in programming.

| Dependency | Usage |
| ---------- | ----- |
| [`mongoose`](https://www.npmjs.com/package/mongoose) | Mongoose is a MongoDB object modeling tool designed to work in an asynchronous environment. |
| [`passport`](https://www.npmjs.com/package/passport) | Passport is Express-compatible authentication middleware for Node.js. |
| [`passport-google-oauth`](https://www.npmjs.com/package/passport-google-oauth) | Passport strategies for authenticating with Google using OAuth 1.0a and OAuth 2.0. |
| [`tedious`](https://www.npmjs.com/package/tedious) | Tedious is a pure-Javascript implementation of the TDS protocol, which is used to interact with instances of Microsoft's SQL Server. |
| [`connect-mongodb-session`](https://www.npmjs.com/package/connect-mongodb-session) | This module exports a single function that takes an instance of connect (or Express) and returns a MongoDBStore class that can be used to store sessions in MongoDB. |
| [`cors`](https://www.npmjs.com/package/cors) | CORS is a node.js package for providing a Connect/Express middleware that can be used to enable CORS with various options. |
| [`express-session`](https://www.npmjs.com/package/express-session) | Create a session middleware. |

    > Note: The rest of the dependencies, like `express`, were installed when you ran `npm i`

### Step 2: Setup Express

Now that everything is set up, we'll start by setting up our __endpoints__.

> __Endpoint__ is a fancy way of saying "this URL will handle HTTP requests for this kind of data". You'll see endpoints referred to as the second half of a URL, for instance, we can create an endpoint to handle all of the CRUD operations for our list of **tasks**: `/api/v1/tasks`. Everything before that text will be the actual address of our server. So, if you have a server at an IP address `155.122.2.33` on port `1337`, you could make a GET request to the endpoint by typing this into the browser:

> ```
> http://155.122.2.33:1337/api/v1/tasks
> ```

> Note: It's good practice to version your API (i.e. the `v1` in `/api/v1/endpoint`) so that if you ever change the endpoints, it doesn't break any front-end applications that are using your API (i.e. instead of changing `/api/v1/endpoint`, you should create a new one: `/api/v2/endpoint`).

#### Environment Variables

When developing an application, sometimes you need to change variables depending on the environment you're developing in. Perhaps when you're developing, you're developing on your laptop, and you are serving the API through port `3001`, but when it goes to production, you want to serve it through port `8080` (the default port). For this project, we will use port `1337` for development and production. 

1. Create a new file called `.env` in your root directory, and define your port there:

    ```shell
    PORT=1337
    ```

    > Note: You should not be pushing `.env` files to GitHub. They often contain sensitive information like passwords which you don't want to make public. Check the .gitignore to make sure that .env is in there before you push anything to GitHub.
    
    > Now when you run your server by executing the command `npm start`, it will run it on `http://localhost:1337` instead of `http://localhost:3000` and you can have that variable change on your production server!
    
    > Hint: If you run `npm start` without all your `.env` variables defined you will run into errors. To get around this just copy the contents of `.env.example` into your `.env` file and try running `npm start` again. 

2. Add the connection string for the Azure SQL Database you created in the last lab
    - Log in to the Azure Portal
    - Find the `SQL databases` link on the left-side menu
    - Click the database you created during the publish section of the last lab
    - In the `Overview` section of the database, you'll find a string labeled `Server name    :`
    - Copy that string and add a new entry into the `.env` file:

      ```shell
      AZURE_SERVER_NAME="<paste your server name here>"
      ```

3. Add an entry for the database's name

    ```shell
    AZURE_DB_NAME="<paste your database name here>"
    ```

4. Remember the username and password I told you to remember forever when you were publishing your website? Add entries for those too:

    ```shell
    AZURE_DB_ADMIN_USERNAME="<paste your admin username here>"
    AZURE_DB_ADMIN_PASSWORD="<paste your admin password here>"
    ```
    >Hint: If you run `npm start` at this point you might see an error about your client IP address. Read the message carefully and try to figure out how to fix this problem using the Azure Portal firewall settings. 

### Step 3: Connect to Cloud DB

Now we need to get our Atlas Database hooked up. We will use Mongoose, an **Object Data Manager** (ODM), which makes communicating with our database much easier, and makes sure we use the proper data structures.

1. Go to your [Atlas Dashboard](https://cloud.mongodb.com) in the browser

    - Click the `Clusters` link on the left-side menu
    - Click `CONNECT > Connect your application`
    - Choose `Node.js` for the `DRIVER`
    - Choose `3.0 or later` for the `VERSION`
    - Click `Copy`

2. In Visual Studio Code, open the `.env` file in the root of your project folder and add the following variable:

    ```shell
    ATLAS_CONNECTION_STRING="<Paste the connection string here>"
    ```

         > Note: Don't forget to replace `<password>` and `<dbname>` with your actual user information you created on Atlas.

3. Open the empty `mongoose.js` file and do the following:
    - Import the `connect` method from the mongoose package
    - Use it to create a connection to the database
        - Check the docs by Googling "mongoose js"; the getting started page should have what you need
        - HINT: Use `process.env` to access the environment variables you created (that's how you get the Atlas connection string)
        - __DO NOT LEAVE THE ACTUAL CONNECTION STRING AS PLAIN TEXT IN THIS FILE. IF A TA CAN FIND YOUR CONNECTION STRINGS IN GITHUB BY SEARCHING THROUGH THE REVISION HISTORY, YOU'LL LOSE POINTS. IT'S IN THE `.env` FILE FOR A REASON.__

    > Explanation: `mongoose.connect()` takes up to 3 parameters: The connection string (This is the only parameter you will be using), any options you may want to use, and a function that will be executed when the process is complete (a __callback function__). We got the connection string from our `.env` file. As per the instructions on Mongoose's [Getting Started](https://mongoosejs.com/docs/) page, we added an option to use the new URL Parser, a recent modification to Mongoose. Finally, we added a __Callback Function__ which takes an error (if there is one), and logs the results of `mongoose.connect()` to the console.
    - If you set up everything correctly you can test the connection by making sure you `require()` the mongoose file in `app/index.js` and then running `npm start`. You should see your MongoDB connection message in the console that you set up in your callback function. 

#### Models

To make sure our data stays consistent, Mongoose allows us to create what is called a **Model**, much like the C# `Task` model we made in lab 7. In C#, models are described by creating a **C# Class**. Here, we will create a **JavaScript Class**, through the use of the built-in `Schema` class and `model()` function that came with Mongoose.

1. Open the `Task.js` file in the `app/models` folder

2. [Use the Mongoose documentation](https://mongoosejs.com/docs/guide.html#definition) to create a schema/model for your Tasks:
    > Note: In the documentation it uses `import mongoose from 'mongoose';` you will replace that line with `var mongoose = require('mongoose');` instead.
 
    - IMPORTANT: Make sure you tell the schema constructor which collection your tasks should be stored in!
    - Be sure to export your new model using `module.exports` so you can use it in other files
    > Explanation: We have to set the collection because Mongoose automatically searches for a collection that's name is whatever the plural of the model is. In this case, Mongoose would be looking for an `"Tasks"` collection, so we specify instead to look for a `Task` model in the `"Tasks"` collection.

   | Property | Type      |
   | -------- | --------- |
   | `UserId` | `String`  |
   | `Text`   | `String`  |
   | `Done`   | `Boolean` |
   | `Date`   | `String`  |
   
### Step 4: Authentication

When you open an API to the world wide web, you have to secure it unless you want potentially malicious actors flooding it with requests. We'll be doing that via the `googleapis` package. This library makes it easy to implement 3rd party authentication. We'll be using it to make sure only authorized users can POST/PUT/DELETE using our API.

#### Setup

Useful References:
- [Passport.js docs: Configure](http://www.passportjs.org/concepts/authentication/oauth/)
   - Make sure you're looking at the section titled "oAuth 2.0" and not "oAuth 1.0"
- [Passport.js docs: Google](http://www.passportjs.org/concepts/authentication/google/)

1. Open the `.env` file, and add the following variables:

    ```shell
    GOOGLE_CLIENT_ID="<Your Google Client ID from Lab 4>"
    GOOGLE_CLIENT_SECRET="<Your Google Client Secret from Lab 4>"
    GOOGLE_CALLBACK_PATH="/api/v1/auth/google/callback"
    SESSION_SECRET="<a random string. can literally be anything you want.>"
    API_ORIGIN="<Domain of API (during testing it will be somthing like http://localhost:1337)>"
    ```

2. Go to your [Google Developer Console](https://console.developers.google.com/projectselector2/apis/dashboard?supportedpurview=project) and under the credentials, choose your OAuth credentials and add your callback URL to the list of authorized callback URLs at the bottom it will have the form of `https://<API_ORIGIN>/api/v1/auth/google/callback`

#### Coding

1. Open the file `app/passport.js` and do the following:

    - Read through the code that is already in this file and try to figure out what it does as best you can. 
        - Using the Useful References above as a template, finish  (`passport.use(new GoogleStrategy(...))`)
            - Pull your GOOGLE_CLIENT_SECRET from your .env instead of a hard-coded string. DO NOT PUT YOUR GOOGLE SECRET IN PLAIN TEXT!!!
            - Instead of hard-coding the callback url, you already defined it in your `.env` file, and can access it like this:
            ```js
            callbackURL: `${process.env.API_ORIGIN}${process.env.GOOGLE_CALLBACK_PATH}`,
            ```
            - Use the `getUserFromAzure()` method we provided to get the user. Import it with the following code: `const { getUserFromAzure } = require('./util')`.
            - The `profile` object will contain the email via `profile.emails[0].value`
     - When you want to `require()` this passport file in `app/index.js` do it like this:
     ```js
     const store = require(`./passport`)(session)
     ```
     > Remember this for when we set up the cookie in the next step. To help understand what is being passed to `app/index.js` look at what is exported at the bottom of `passport.js`.
     
2. Set up your session and cookie using [the express session middleware](https://www.npmjs.com/package/express-session) documentation, specifically the section that explains using secure cookies in production, but allowing for testing in development. Add your `const sess = {/.../}`, `if` statement, and `app.use(session(sess))` code in `app/index.js` just below where you `.use()` the `cookieParser()`.
    Here is an example from the documentation:
    ```js
    const sess = {
        secret: 'keyboard cat',
        cookie: {}
    }
 
    if (app.get('env') === 'production') {
        app.set('trust proxy', 1) // trust first proxy
        sess.cookie.secure = true // serve secure cookies
    }
 
    app.use(session(sess))  // use the session with the attributes defined above
    ```
    > In this example, the `sess` variable contains all of the attributes of the session (`secret` and `cookie`). You can add additional attributes to the session here like `name`, `resave`, and `saveUninitialized`. 

    > The `if` statement uses an environment variable to check if the application is running in a `production` environment. You will set up this environment variable if you deploy your code on a live server. If the application is running in a `production` environment, it will use secure cookies. This is necessary because using secure cookies requires an HTTPS connection, so they will not work when you are testing locally in your `development` environment. 

    Modify the above example in the following ways:
    - For the secret, use `process.env` to access the environment variables that you created
    - Name your session `it210_session` so you can find easily it using the inspector and because it matches your python unit tests
    - For the cookie, just set the `maxAge` to whatever you want (I set it for a week, which is 604,800,000‬ milliseconds!)
    - set `resave: false` and `saveUninitialized: true`
    - Add the word `store` to the end of the session initialization:
     ```js
     const sess = {
       /.../,
       store
     }
     ```

3. Add some code to prevent the cookies from being blocked. In the next lab, you will create a frontend to use your API backend. Since the front end will have a different URL than the API, some browsers might block the cookie. To fix this issue, you can set the cookie's `sameSite` attribute to `'none'`, which will allow the cookie to be sent to a different URL than the one it came from. To do this:
     - Set the `sameSite` attribute of the cookie to `'none'` inside the `if (app.get('env') === 'production')` statement, right after the `secure` attribute of the cookie is set to `true`. 
    > NOTE: Some browsers may still block these cookies in incognito mode or if you have configured your browser to block Cross-Site Cookies.

4. Create a new file in the `app/routes` folder called `auth.js`. In this file you will define all endpoints that deal with google authentication. You should use `tasks.js` and the [Passport.js docs: Google](http://www.passportjs.org/concepts/authentication/google/) as your references to make this file.


- There are three routes you need to have:
   - `router.get(/google)`
      - This route will redirect the user to google for authentication.
      - Instead of a route handler, just use `passport.authenticate('google', ....)`
      - Make sure to include the correct `scope` for the google strategy:
        ```js
        { scope: [`https://www.googleapis.com/auth/userinfo.email`] }
        ```
   - `router.get(/google/callback)`
      - This route will define what happens when google redirects back to your application.

      - You should have already set up what the callback route will be on your developers console (`https://<API_ORIGIN>/api/v1/auth/google/callback`).
      - If the session is saved correctly, redirect to a CLIENT_ORIGIN you will need to set up in your .env.
      - In the future you will set your CLIENT_ORIGIN to a vue front end application but for the moment set your CLIENT_ORIGIN to `"<Your API ORIGIN>/api/v1/tasks"` so you can see the user's tasks after logging in if everything is working.
  ```js
     router.get('/google/callback', passport.authenticate('google'), async (req, res) => {
       // This tries to save the session, and if it fails it makes sure the passport session is deleted via req.logout()
       req.session.save(err => {
           if (err) {
             req.logout()
             res.sendStatus(500)
           }
           else res.redirect(process.env.CLIENT_ORIGIN)
         })
       })
  ```

   - `router.get(/logout)`
      ```js
         router.get(`/logout`, async (req,res) => {
           req.session.destroy()
           req.logout()
           res.redirect(process.env.CLIENT_ORIGIN)
         })
      ```       
- Make sure to add an appropriate `app.use()` statement in `app/index.js` that uses your `auth.js` router and matches the callback path we set up in `.env`. 
5. In `app/index.js`, make sure your `app` `.use()`s `passport.initialize()` and `passport.session()` BEFORE it `.use()`s any routes.

6. Test your API by calling your login route in a browser at `<API_ORIGIN>/api/v1/auth/google`

    - If everything is set up correctly, you should be redirected to the Google Login page
    - When it returns, you should see the tasks that belong to the logged-in user from the redirect in the `/google/callback` route.
    - Once that's done, you'll have a session cookie, and can test the path for getting the current user's object:
    - If you hit the `/api/v1/auth/logout` endpoint the /api/v1/user endpoint should say "Unauthorized"


### Step 5: Create The Routes

__Useful Resources__
- [Express Documentation: Router](https://expressjs.com/en/5x/api.html#router)

If we want the server to respond to HTTP requests, we need to tell it how to handle them. But first, we need our API to listen for requests.

For our endpoints, express provides functions for each of the CRUD operations. We need two for the **Read** operation, as we'll need to be able to get just one task as well:

| Operation | Express Function |
| --------- | ---------------- |
| Create    | `.post()`        |
| Read      | `.get()`         |
| Update    | `.put()`         |
| Delete    | `.delete()`      |

1. In the file `app/routes/tasks.js`, import your `Task` model

2. Add 4 new routes:

    > Hint: Check what the information you used in Lab 5A for the HTTP request to see what data and headers you need to use.
    - `.post('/')`
        - Create a new `Task` object using the `request.body` and `request.user` objects to populate it with data
        - Make an asynchronous request using Mongoose to `.save()` the new `task`, and save the results in a variable
        - Use the `res` variable to `.send()` the result back to the requester with a `201` status (Created)
        - Surround the process in a `try catch` statement, and catch any errors
        - If there are errors, log them to the console with `console.error()` and use the `res` object to send a `500` error (Server Error)
    - `.get('/')`
        - Use the `Task` class to `.find()` all of the tasks for the authenticated user
        - Use the `res` object to `.send()` the tasks back to the client
    - `.put('/:id')`
        - Use the `Task` class to update the task
        - Use the `res` object to `.send()` the task back to the client
    - `.delete('/:id')`
        - Use the `Task` class to delete the task
        - Use the `res` object to `.send()` a message back to the client

    > Explanation of `async/await`: Using `async` and `await` is a newer JavaScript feature which allows for cleaner code. Essentially, adding `async` before a function definition will tell JavaScript that it may have to stop executing code for a while if it finds an `await` statement. Here, you'll put `await` in front of any request to the database. JavaScript will stop executing on that line, and won't run the next line until the `await` line is finished.

    > Explanation of response codes: [There are plenty of generally accepted response codes available](https://www.restapitutorial.com/httpstatuscodes.html) to use in your app. The most important thing is that they make sense to anyone who wants to use your API. (my favorite is `420`)

3. In `app/index.js`, just above the export, have your `app` `.use()` the routers]
    - Make sure to include a version to your API in the path:
        - `/api/v1/tasks`
    - Also, make sure to use the `authenticate()` method we provide to you in `util`
        - This is imported with the following code:
            - `const { authenticate } = require('./util')`
        - You can easily just add it as a parameter to the `.use()` method AFTER the path parameter, but BEFORE the router parameter

4. In Postman, create new requests to test all of your endpoints.

    - Make sure each request uses the proper HTTP Verb
    - Fill in the URL with the appropriate endpoint attached (`http://localhost:1337/api/v1/tasks`)
    - For requests that need a body:
        - Select the `Body` tab
        - Select the `raw` radio button
        - Select `JSON (application/json)` from the dropdown to the right of the radio buttons
        - Type in some appropriate JSON for the selected endpoint
    - Test your endpoints and make sure you see the changes in your database on Atlas
   >Hint: you may want to temporarily remove the `authenticate` parameter in your `/api/v1/tasks` `app.use` statement for testing purposes. Just don't forget to put it back after you are done testing. 

### Step 6: Testing

Use your Lab 5 python unit tests to test and fine tune your code. You will have to do some error checking in each of your endpoints and return different status codes to get all your python unit tests to pass. You will have to login to your application using the routes you created. Copy the cookie using the inspector and edit your `test_api.py` file to run the tests on your API until they all pass. When all tests have passed you know you have a working API!

### Step 7: Hosting

Once your code is pushed to your GitHub repo, open an SSH session to your AWS server and do the following:
1. Assigning a static IP address
    - In the AWS console under the `Network & Security` section select `Elastic IPs`
    - On the right hand side select `Allocate Elastic IP address`
    - Leave the default settings and select `Allocate`
    - Select on the IP address under the `Allocated IPv4 address` column
    - Select `Associate Elastic IP address`
    - In the text box labeled `Instance` select the name of the instance you want to be assosiated with the static IP address and then select `Associate`
    - To test if it worked correctly SSH into your server using the static IP address

2. Update Firewall rules

    - Go to your `Instance summary` and select the `Security` tab
    - Under the heading `Security Groups` click on the link then click on `Edit inbound rules`
    - Add a rule for HTTPS and set the `source` to `Anywhere-IPv4`
    - Add another rule where the type is `Custom TCP`, the `Port range` is `1337` and the `source` is `Anywhere-IPv4`
    - Select `Save rules`
  
3. MongoDB

    - Add the public IP of your server (The elastic IP address) to the `Network Access` on MongoDB Atlas  

4. Azure Portal
    - Add the public IP of your server (The elastic IP address) to the `Set Server Firewall`
5. Domain name
    - Go to this [website](https://www.noip.com) and create an account.
    - During the account creation process you will have the option to create a `Hostname`. Whatever you choose to create as a hostname will be the domain name for your website.
    - Activate your account through the email they send you
    - Log into your account and go to `Active Hostnames` or `No-IP Hostnames`.
    - Click on the domain you chose during set up and change the IP address to the Elastic IP address you accosiated with your server in step 6.-It will take a few minutes for the change in IP address to take affect.
6. Google Developer Console
    - Add your public domain to the authorized Redirect URIs in the Google Developer Console `https://<your_domain.ddns.net>/api/v1/auth/google/callback`

7. Server Set Up

    - Installing nodejs 
    ```sh
    curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
    sudo apt-get install -y nodejs
    ```

    - Clone your repo onto the server, the home directory (`~`) is fine

    - Inside your repo, run `sudo npm i` to install dependencies. Make sure to install all the dependencies mentioned in the "Add Dependencies" section at the top of these instructions.

    - Create a `.env` file in the root of your project using `nano` or `vim` or `vi` or `emacs` if you think you're really something env file
    - Add all of the variables from the `.env` file in your production environment editing `API_ORIGIN` and `CLIENT_ORIGIN` to use the base-url of `https://<your_domain.ddns.net>:1337`. Also, add `NODE_ENV=production` to your `.env` file so that your server will run in the `production` environment and add `DOMAIN=<your_domain.ddns.net>`

    - We now need to create an SSL certifacte that will be used by the front and back end
        - Make sure when you enter the domain it is just ``<your_domain.ddns.net>`` and nothing else

     ```sh
    sudo snap install core; sudo snap refresh core
    sudo snap install --classic certbot
    sudo ln -s /snap/bin/certbot /usr/bin/certbot
    sudo certbot --apache
  
    ```
   
    - Since the SSL certificate is owned by root we need to change it to be owned by ubuntu so nodejs can use it.
    ```sh
    sudo chown ubuntu -R /etc/letsencrypt
    ```

    - Try to start your nodejs application by running `npm start` 


    - Test your API by calling your login (`api/v1/auth/google`) route in a browser, this time using your public domain, make sure you are using `https` and not `http` to access your API.

    - If everything is set up correctly, you should be redirected to the Google Login page
    - When it returns, you should be able to get the cookie from your browser and run your python unit tests on the live server as well.

And just like that, you have created a beautiful RESTful API!!


### Step 8: Documentation

Part of a good API is good documentation. You've used plenty of documentation so far, and you know that sometimes the documentation isn't very good (*COUGH COUGH PHP COUGH COUGH*). Your documentation will, therefore, be incredible.

Using proper markdown syntax (what this lab writeup was written with!), edit your `README.md` to have an entry for every endpoint. Each entry should include:

1. Description (purpose) of the endpoint
2. Path Parameters (if applicable) and corresponding descriptions, in a markdown table
3. Request Body JSON Example (if applicable) in a markdown multiline code block, with syntax highlighting
4. Response Body JSON Example in a markdown multiline code block, with syntax highlighting

Use appropriate headers where applicable

> Hint: Click the `RAW` button at the top of this document to see what I typed with my bare hands that made this document look so incredibly spectacular!

# Node.js RESTful API Pass-off

## Pass-off Requirements

- [ ] 10 Points - `README.md` provides useful notes on how to use the API
- [ ] 5 Points - `.env` file is not found on GitHub
- [ ] 5 Points - There are no hard-coded secrets in `passport.js`
- [ ] 5 Points - Code is backed up on GitHub
- [ ] 5 Points - Code is neat and tidy with appropriate comments when useful
- [ ] 20 Points - Demonstrate how API passes all unit tests from lab 5A. 
- [ ] 10 Points - User is unable to access `/api/v1/user` in browser unless logged-in
- [ ] 10 Points - User can log in with Google via `api/v1/auth/google` endpoint
- [ ] 10 Points - After logging in, there is a cookie called `it210_session`
- [ ] 10 Points - User is able to access `/api/v1/user` and `/api/v1/tasks` in browser after logging in
- [ ] 10 Points - There are no insecure hard-coded strings that can be found in any commit in your GitHub repo (passwords, connection strings, etc)

> For extra credit to apply all passoff requirements need to be met

# Extra Credit

> Note: TAs cannot help you with extra credit!

- [ ] 5 Points - Routers use named functions instead of anonymous functions, and the named functions have [JSDoc comments](https://jsdoc.app/about-getting-started.html) describing what they are

# Writeup Questions

- List three advantages to using a web API.
- What are the differences between these four HTTP methods: GET, POST, PUT, and DELETE? Which ones are idempotent?
