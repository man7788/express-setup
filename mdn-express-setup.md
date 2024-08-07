# Setting up Express

## Using npm

Use the npm init command to create a package.json file for your application:

```
npm init -y
```

Install Express in the myapp directory and save it in the dependencies list of your package.json file:

```
npm install express
```

## Using Express Application Generator

Install the generator tool site-wide using the npm package manager:

```
npm install express-generator -g
```

> The generator has a number of options, which you can view on the command line using

```
express --help (or -h) command
```

## Creating the project

Navigate to where you want to create the project and then run the Express Application Generator in the command prompt as shown:

```
express <your-app-name> (--view=pug)
```

> Note: The generator-created files define all variables as var. Open all of the generated files and change the var declarations to const before you continue (the remainder of the tutorial assumes that you have done so).

Install all the dependencies for the app using npm as shown:

```
npm install
```

## Enable server restart on file changes

Use the following command in the root directory:

```
npm install --save-dev nodemon
```

Find the scripts section of your package.json and update as follow

```
  "scripts": {
    "start": "node ./bin/www",
    "devstart": "nodemon ./bin/www",
    "serverstart": "DEBUG=express-locallibrary-tutorial:* npm run devstart"
  },
```

## The generated project

Replace the dependencies section of your package.json file with the latest versions of these libraries:

```
  "dependencies": {
    "cookie-parser": "^1.4.6",
    "debug": "^4.3.5",
    "express": "^4.19.2",
    "http-errors": "~2.0.0",
    "morgan": "^1.10.0",
    "pug": "3.0.3"
  },
```

Then update your installed dependencies using the command:

```
npm install
```

## Using Mongoose

Installed Mongoose in your project:

```
npm install mongoose
```

### Connect to MongoDB

Open /app.js (in the root of your project) and copy the following text below where you declare the Express application object (after the line const app = express();). Replace the database URL string ('insert_your_database_url_here') with the location URL representing your own database:

```
// Set up mongoose connection
const mongoose = require("mongoose");
mongoose.set("strictQuery", false);
const mongoDB = "insert_your_database_url_here";

main().catch((err) => console.log(err));
async function main() {
  await mongoose.connect(mongoDB);
}
```

## Routes and controllers

### Handling exceptions in route functions

The controllers will use the express-async-handler module, install it into the library using npm:

```
npm install express-async-handler
```

## Displaying data

### Date formatting using luxon

install it into the library using npm:

```
npm install luxon
```

## Working with forms

### Validation and sanitization

Install express vaildator by running the following command in the root of the project:

```
npm install express-validator
```

## Getting your website ready to publish

### Database configuration

Install dotenv:

```
npm install dotenv --save

```

Replace the line with the following code that uses process.env.MONGODB_URI to get the connection string from an environment variable named MONGODB_URI:

```
const mongoDB = process.env.MONGODB_URI
```

### Set NODE_ENV to 'production'

```
NODE_ENV=production
```

> Note: This is actually a change you make in your environment setup rather than your app.

### Log appropriately

Install debug to minimize "debug" logging in production

```
npm install debug
```

The debug variable is declared with the name 'author', and the prefix "author" will be automatically displayed for all logs from this object.

```
const debug = require("debug")("author");
```

You can then enable a particular set of logs by specifying them as a comma-separated list in the DEBUG environment variable.

```
#Windows
set DEBUG=author,book

#Linux
export DEBUG="author,book"

```

### Use gzip/deflate compression for responses

Install this at the root of your project by running the following command:

```
npm install compression
```

Open ./app.js and require the compression library as shown. Add the compression library to the middleware chain with the use() method (this should appear before any routes you want compressed — in this case, all of them!)

```
const catalogRouter = require("./routes/catalog"); // Import routes for "catalog" area of site
const compression = require("compression");

// Create the Express application object
const app = express();

// …

app.use(compression()); // Compress all routes

app.use(express.static(path.join(__dirname, "public")));

app.use("/", indexRouter);
app.use("/users", usersRouter);
app.use("/catalog", catalogRouter); // Add catalog routes to middleware chain.

// …
```

### Use Helmet to protect against well known vulnerabilities

Install this at the root of your project by running the following command:

```
npm install helmet

```

Open ./app.js and require the helmet library as shown. Then add the module to the middleware chain with the use() method.

```
const compression = require("compression");
const helmet = require("helmet");

// Create the Express application object
const app = express();

// Add helmet to the middleware chain.
// Set CSP headers to allow our Bootstrap and Jquery to be served
app.use(
  helmet.contentSecurityPolicy({
    directives: {
      "script-src": ["'self'", "code.jquery.com", "cdn.jsdelivr.net"],
    },
  }),
);

// …
```

### Add rate limiting to the API routes

Install express-rate-limit at the root of your project by running the following command:

```
npm install express-rate-limit
```

Open ./app.js and require the express-rate-limit library as shown. Then add the module to the middleware chain with the use() method.

```
const compression = require("compression");
const helmet = require("helmet");

const app = express();

// Set up rate limiter: maximum of twenty requests per minute
const RateLimit = require("express-rate-limit");
const limiter = RateLimit({
  windowMs: 1 * 60 * 1000, // 1 minute
  max: 20,
});
// Apply rate limiter to all requests
app.use(limiter);

// …
```

### Set node version

The only important information missing from our current package.json is the version of node required by the library. You can find the version of node that was used for development by entering the command:

```
>node --version
v16.17.1
```

Open package.json, and add this information as an engines > node as shown (using the version number for your system).

```
  "engines": {
    "node": ">=16.17.1"
  },

```

### Get dependencies and re-test

we will need to fetch our dependencies. You can do this by running the following command in your terminal at the root of the project:

```
npm install
```

Now run the site and check that the site still behaves as you expect.
