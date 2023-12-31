---
title: Creating Node Server
categories: [js,fullstack]
tags: [node,express,server]

---


## Overview
In this post I’d like to share how to set up a server using Node and Express!
The purpose of setting up a server with Node and Express is to create some aspects of backend functionality for a web application. This includes building route handlers, as well as functions that trigger when the routes are accessed. For this post we will be focusing on how to create a basic server with a few example routes. So let’s get started!
## Create Directory and Install Dependencies
You’ll want to start by creating a new directory for your project. You can do this through the command line with the mkdir command. Or you can do it through your file explorer’s UI. (If you use mkdir make sure when you run this command you’re in the correct directory that you’d like to use).

After this we’re going to initialize the project itself using the 
```shell
npm init -y command
```

This command creates your project.json file and the y flag says yes to all of the setup options. If you want to specify these options just don’t include "-y". 
Next install the Express.js Framework using

```shell
 npm install express
```
## Create Server File and Variables
With these steps completed you have all the dependencies you need installed, and you can start creating your server! Create a JavaScript file called server.js. If you didn’t guess this is where we'll set up the Express server.

In Node it’s common practice to import the dependencies you’re going to use with the require function, and assigning them to variables. Here is an example with express.
```javascript
const express = require("express");

```
This of course means this express variable has access to all the functions in the express module.

Next we’re going to create an instance of the express application. To do this we now assign a variable called app to a function call using our express variable.

```javascript
const app = express()
```
After that we want to create a local environment variable that will act as the port number our server will use.
The way I normally do this is by giving the variable two options, which is the environments port or defaulting to port 3000; It would look something like this.
```javascript
const PORT = process.env.PORT || 3000;
```
## Creating Routes
The next step would be to create our route handlers. These are functions that execute whenever the specific route is accessed. 

To do this we’d use our app variable that’s using express, define the route, with a request and response as arguments in the function and finally return something. This can be data or a message etc. 

Here is an example:
```javascript
app.get('/', (req, res) => {
  res.send('Hello, World!');
});

app.get('/testroute', (req, res) => {
  res.send('A test route');
});
```
## Testing our Server

Now we’re ready to start our server! We’re going to use the app variable again to listen on the port specified in our PORT variable, and return a message in the console. I think it’s an important step to use string interpolation here to see what port your server is running on.
```javascript
app.listen(port, ()=> {
console.log(`server is running on port ${port}`)
});
```
After starting our server now we can run it in our project directory using: node server.js;

You should see whatever message you typed in the app.listen() function above, in your terminal.

Finally you can access the server in your localhost with the port number, or a tool like Postman.

## Conclusion

I hope you enjoyed this piece of information and I’ll expand on how to create additional functionality in routes, error handling and middleware as well, in a future post. Until then!
