# Test Mongoose Models Using Mongo Memory Server

## Creating the Mongoose Model

Execute this command to install the mongoose package:

```
npm install mongoose
```

Create a new file called todo.model.js and define the todo schema:

```
const mongoose = require("mongoose")
const { Schema } = mongoose

const TodoSchema = new Schema({
    item: {
        type: String,
        required: true
    },
    compeleted: {
        type: Boolean,
        required: true
    }
})

module.exports = mongoose.model("Todo", TodoSchema)
```

## Setting Up the Database

Run the following command to install mongodb-memory-server:

```
npm install mongodb-memory-server
```

Create a new file called setuptestdb.js and import mongoose and mongodb-memory-server:

```
const mongoose = require("mongoose");
const { MongoMemoryServer } = require("mongodb-memory-server");

let mongo = null;

const connectDB = async () => {
  mongo = await MongoMemoryServer.create();
  const uri = mongo.getUri();

  await mongoose.connect(uri, {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  });
};

const dropDB = async () => {
  if (mongo) {
    await mongoose.connection.dropDatabase();
    await mongoose.connection.close();
    await mongo.stop();
  }
};

const dropCollections = async () => {
  if (mongo) {
    const collections = await mongoose.connection.db.collections();
    for (let collection of collections) {
      await collection.deleteMany();
    }
  }
};

module.exports = { connectDB, dropDB, dropCollections}
```

## Writing the Tests

### Using Jest to write the tests

Run the following command to install jest:

```
npm install jest
```

In the package.json file, configure jest. Replace your existing "scripts" block with the following:

```
"scripts": {
    "test": "jest --runInBand --detectOpenHandles"
},
"jest": {
    "testEnvironment": "node"
},
```

Create a new file called todo.model.test.js and import the mongoose library, the todo model, and the conenctDB(), dropDB(), and dropCollections() functions:

```
const mongoose = require("mongoose");
const { connectDB, dropDB, dropCollections } = require("./setuptestdb");
const Todo = require("./todo.model");

beforeAll(async () => {
  await connectDB();
});

afterAll(async () => {
  await dropDB();
});

afterEach(async () => {
  await dropCollections();
});

describe("Todo Model", () => {
  it("should create a todo item successfully", async () => {
    let validTodo = {
      item: "Do the dishes",
      compeleted: false,
    };
    const newTodo = await Todo(validTodo);
    await newTodo.save();
    expect(newTodo._id).toBeDefined();
    expect(newTodo.item).toBe(validTodo.item);
    expect(newTodo.completed).toBe(validTodo.completed);
  });
});

it("should fail for todo item without required fields", async () => {
    let invalidTodo = {
      item: "Do the dishes",
    };
    try {
      const newTodo = new Todo(invalidTodo);
      await newTodo.save();
    } catch (error) {
      expect(error).toBeInstanceOf(mongoose.Error.ValidationError);
      expect(error.errors.compeleted).toBeDefined();
    }
  });

it("should fail for todo item with fields of wrong type", async () => {
    let invalidTodo = {
      item: "Do the dishes",
      compeleted: "False"
    };
    try {
      const newTodo = new Todo(invalidTodo);
      await newTodo.save();
    } catch (error) {
      expect(error).toBeInstanceOf(mongoose.Error.ValidationError);
      expect(error.errors.completed).toBeDefined();
    }
  });
```
