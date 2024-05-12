# The MERN Stack Deployment

The MERN stack, comprising MongoDB, Express, React, and Node.js, represents one of the most popular choices for developing modern single-page web applications.

![Animated screenshot of a todo application](#)

### Prerequisites

Before getting started, ensure you have the following prerequisites:

- **Node.js**: Make sure you have Node.js installed locally.
- **Code Editor**: Choose a code editor with JavaScript code highlighting.
- **API Testing Tool**: Install a tool like Postman for testing API endpoints.

- Node v14.2.0
- npm v6.14.5
- mongodb-community v4.2.6
- express v4.17.1
- mongoose v5.9.17

## Step 1 — Application Setup

Begin by creating a new directory for your project, such as `mern-todo`, and navigate into it:

```bash
mkdir mern-todo
cd mern-todo
```

Initialize your project with a `package.json` file, which outlines your app's details and dependencies:

```bash
npm init -y # This will use default values
```

## Step 2 — Node Server Configuration

Your backend JavaScript code requires a server for execution. You can either use Node's built-in `http` module or opt for the Express.js framework, which simplifies the creation of RESTful APIs.

Install Express with npm:

```bash
npm install express
```

Create an `index.js` file and input the following:

```javascript
const express = require("express");
const app = express();
const port = process.env.PORT || 5000;

app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  res.header(
    "Access-Control-Allow-Headers",
    "Origin, X-Requested-With, Content-Type, Accept"
  );
  next();
});

app.get("/", (req, res) => {
  res.send("Welcome to Express");
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

To handle CORS issues during development and testing, the above middleware is essential.

Start your server:

```bash
node index.js
```

You should see `Server running on port 5000` in your terminal.

## Step 3 — Route Creation

Your application will need to:

- Create tasks
- Display all tasks
- Remove completed tasks

For these functionalities, define routes in a `routes/api.js` file:

```javascript
const express = require("express");
const router = express.Router();

// Placeholder routes for GET, POST, and DELETE
router.get("/todos", (req, res, next) => {
  /* ... */
});
router.post("/todos", (req, res, next) => {
  /* ... */
});
router.delete("/todos/:id", (req, res, next) => {
  /* ... */
});

module.exports = router;
```

## Step 4 — Model Definitions

With MongoDB as your NoSQL database, define a model and schema. Models are structured using schemas, which specify the fields, validation, and defaults for documents.

Install Mongoose for easier MongoDB interactions:

```bash
npm install mongoose
```

Create a `models/todo.js` file:

```javascript
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const TodoSchema = new Schema({
  action: {
    type: String,
    required: [true, "The todo text field is required"],
  },
});

const Todo = mongoose.model("todo", TodoSchema);

module.exports = Todo;
```

Update `routes/api.js` to utilize the new model.

## Step 5 — Database Connection

Connect to your MongoDB database, set up via MongoDB Atlas or locally:

```javascript
const express = require("express");
const bodyParser = require("body-parser");
const mongoose = require("mongoose");
const routes = require("./routes/api");
require("dotenv").config();

const app = express();
const port = process.env.PORT || 5000;

mongoose
  .connect(process.env.DB, { useNewUrlParser: true })
  .then(() => console.log(`Database connected successfully`))
  .catch((err) => console.log(err));

mongoose.Promise = global.Promise;

app.use(bodyParser.json());
app.use("/api", routes);

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

**Note:** Express 4.16+ includes a built-in parser, rendering `body-parser` unnecessary for newer versions.

## Step 6 — API Testing

Once your RESTful API is set up, it's time to test it. Use API clients like Postman or Insomnia to ensure everything is working as expected.

Start your server:

```bash
node index.js
```

Then, test your API endpoints. For example, for a POST request:

```json
POST http://localhost:5000/api/todos
Body:
{
  "action": "build a MERN steghub project 2"
}
```

For a GET request, you should receive a response similar to:

```json
[
  {
    "_id": "5bd4edfc89d4c3228e1bbe0a",
    "action": "build a MERN steghub project 2"
  }
]
```

Test the DELETE method by sending a request to remove a specific todo item.

## Step 7 — Frontend Creation

With your API ready, it's time to build the client interface. Use the `create-react-app` command to scaffold your React application:

```bash
npx create-react-app client
```

This creates a `client` directory within your project where you can add your React code.

## Step 8 — Running the React App

Before launching the React app, install the necessary dependencies:

```bash
npm install concurrently --save-dev # Runs commands concurrently
npm install nodemon --save-dev # Monitors for any changes in your Node.js application and automatically restarts the server
```

Update your `package.json` scripts:

```json
"scripts": {
  "start": "node index.js",
  "start-watch": "nodemon index.js",
  "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
}
```

In the `client` directory, set up a proxy in `package.json` to simplify API calls:
Certainly! Here's the completion of the README content for the MERN stack todo application tutorial:

---

```json
"proxy": "http://localhost:5000"
```

This proxy setup in our `package.json` file will enable you to make API calls without having to type the full URL, just `/api/todos` will get all your todos.

Run the development environment with:

```bash
npm run dev
```

Ensure you are in the `mern-todo` directory and not in the `client` directory. Your app will be open and running on `localhost:3000`.

## Step 9 — Creating the React Components

React's component-based architecture enhances reusability and modularity. For the todo app, you'll create two stateful components and one stateless component.

Inside your `src` folder, create a `components` folder and within it, create three files: `Input.js`, `ListTodo.js`, and `Todo.js`.

### Input Component

The `Input` component allows users to add new todos. Here's the code for `Input.js`:

```javascript
import React, { Component } from "react";
import axios from "axios";

class Input extends Component {
  state = {
    action: "",
  };

  addTodo = () => {
    const task = { action: this.state.action };

    if (task.action && task.action.length > 0) {
      axios
        .post("/api/todos", task)
        .then((res) => {
          if (res.data) {
            this.props.getTodos();
            this.setState({ action: "" });
          }
        })
        .catch((err) => console.log(err));
    } else {
      console.log("input field required");
    }
  };

  handleChange = (e) => {
    this.setState({ action: e.target.value });
  };

  render() {
    let { action } = this.state;
    return (
      <div>
        <input type="text" onChange={this.handleChange} value={action} />
        <button onClick={this.addTodo}>add todo</button>
      </div>
    );
  }
}

export default Input;
```

### ListTodo Component

The `ListTodo` component displays the list of todos and allows users to delete them. Here's the code for `ListTodo.js`:

```javascript
import React from "react";

const ListTodo = ({ todos, deleteTodo }) => {
  return (
    <ul>
      {todos && todos.length > 0 ? (
        todos.map((todo) => {
          return (
            <li key={todo._id} onClick={() => deleteTodo(todo._id)}>
              {todo.action}
            </li>
          );
        })
      ) : (
        <li>No todo(s) left</li>
      )}
    </ul>
  );
};

export default ListTodo;
```

### Todo Component

The `Todo` component is the main container that holds both the `Input` and `ListTodo` components. Here's the code for `Todo.js`:

```javascript
import React, { Component } from "react";
import axios from "axios";
import Input from "./Input";
import ListTodo from "./ListTodo";

class Todo extends Component {
  state = {
    todos: [],
  };

  componentDidMount() {
    this.getTodos();
  }

  getTodos = () => {
    axios
      .get("/api/todos")
      .then((res) => {
        if (res.data) {
          this.setState({ todos: res.data });
        }
      })
      .catch((err) => console.log(err));
  };

  deleteTodo = (id) => {
    axios
      .delete(`/api/todos/${id}`)
      .then((res) => {
        if (res.data) {
          this.getTodos();
        }
      })
      .catch((err) => console.log(err));
  };

  render() {
    let { todos } = this.state;
    return (
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos} />
        <ListTodo todos={todos} deleteTodo={this.deleteTodo} />
      </div>
    );
  }
}

export default Todo;
```

Adjust your `App.js` to include the `Todo` component:

```javascript
import React from "react";
import Todo from "./components/Todo";
import "./App.css";

const App = () => {
  return (
    <div className="App">
      <Todo />
    </div>
  );
};

export default App;
```

Finally, style your application by updating `App.css`:

```css
.App {
  text-align: center;
  font-size: calc(10px + 2vmin);
  width: 60%;
  margin-left: auto;
  margin-right: auto;
}

/* Add additional styles here */
```

```bash
    npm run dev
```

---
