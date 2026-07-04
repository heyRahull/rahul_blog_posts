---
title: "Building a Full-Stack MERN Todo App"
datePublished: 2026-07-04T08:41:10.196Z
cuid: cmr644qvd00000akp276o1acy
slug: building-a-full-stack-mern-todo-app

---

Building a Todo application is a rite of passage for full-stack developers. But why stop at a basic list when you can build a seamless, real-time experience?

In this guide, we will break down a full-stack Todo application utilizing a Node.js/Express backend paired with a React frontend. This setup features full CRUD operational capabilities, MongoDB integration, and a sleek inline-editing interface.

* * *

## The Backend: Express & Mongoose

Our backend acts as the bridge between the database and the user interface. It defines how data is structured and handles incoming requests via an API.

Here is the complete implementation for `server.js`:

```javascript
import express from 'express';
import mongoose from 'mongoose';
import cors from "cors";

const app = express();
app.use(express.json());
app.use(cors());

// Define the schema for our tasks
const todoSchema = new mongoose.Schema({
    text: { type: String, required: true },
    completed: { type: Boolean, default: false },
    checked: { type: Boolean, default: false },
});

const Todo = mongoose.model('Todo', todoSchema);

// CREATE: Add a new task
app.post('/api/v1/todos', async (req, res) => {
    try {
        const newTodo = await Todo.create(req.body);
        res.status(200).json({
            success: true,
            message: "New Todo Entry Created",
            todo: newTodo,
        });
    } catch (err) {
        res.status(500).json({
            success: false,
            message: "Unable to create Todo",
            error: err.message
        });
    }
});

// READ: Fetch all tasks
app.get('/api/v1/todos', async (req, res) => {
    try {
        const allTodos = await Todo.find({});
        res.status(200).json({
            success: true,
            message: "All Todos Fetched Successfully",
            count: allTodos.length,
            todos: allTodos
        });
    } catch (err) {
        res.status(500).json({
            success: false,
            message: "Failed to retrieve todo data",
            error: err.message
        });
    }
});

// UPDATE: Modify a task's text or completion status
app.put('/api/v1/todos/:id', async (req, res) => {
    try {
        const { id } = req.params;
        const updatedTodo = await Todo.findByIdAndUpdate(id, req.body, { new: true });
        res.status(200).json({
            success: true,
            message: "Todo Updated Successfully",
            updatedTodo: updatedTodo
        });
    } catch (err) {
        res.status(500).json({
            success: false,
            message: "Failed to update Todo",
            error: err.message
        });
    }
});

// DELETE: Remove a task completely
app.delete('/api/v1/todos/:id', async (req, res) => {
    try {
        const { id } = req.params;
        const deletedTodo = await Todo.findByIdAndDelete(id);
        res.status(200).json({
            success: true,
            message: "Todo Deleted Successfully",
            deletedTodo: deletedTodo
        });
    } catch (err) {
        res.status(500).json({
            success: false,
            message: "Failed to delete Todo",
            error: err.message
        });
    }
});

// MongoDB Atlas Connection
mongoose.connect('mongodb://rahulag774_db_user:1234Rahul@ac-zykgbeb-shard-00-00.tdjfdzo.mongodb.net:27017,ac-zykgbeb-shard-00-01.tdjfdzo.mongodb.net:27017,ac-zykgbeb-shard-00-02.tdjfdzo.mongodb.net:27017/?ssl=true&replicaSet=atlas-14fdng-shard-0&authSource=admin&appName=Cluster0')
.then(() => console.log("Database Connected Successfully"))
.catch((err) => console.log("Error connecting to DB: ", err));

app.listen(5000, () => { console.log("Server is running on port 5000"); });

```

### Key Backend Takeaways

*   **CORS Middleware:** Essential for allowing our React frontend (running on a different port) to communicate with our Express server seamlessly.
    
*   **RESTful Routing:** The endpoints are structured cleanly under `/api/v1/todos` using standard HTTP methods (`GET`, `POST`, `PUT`, `DELETE`).
    
*   **Mongoose Lifecycle:** Using `{ new: true }` in `findByIdAndUpdate` guarantees that the database returns the *updated* document rather than the original, ensuring our frontend stays perfectly synced.
    

* * *

## The Frontend: React with State-Driven Inline Editing

Our React frontend does more than just render data—it smartly manages temporary interaction states to toggle between plain text layout and dynamic editing inputs.

Here is the code for `TodoComponent.jsx`:

```jsx
import { useEffect, useState } from "react";

const TodoComponent = () => {
    const [todos, setTodos] = useState([]);
    const [todo, setTodo] = useState("");
    const [editIndex, setEditIndex] = useState(null);
    const [editTodo, setEditTodo] = useState("");

    useEffect(() => {
        fetchTodo();
    }, []);

    // GET Request
    const fetchTodo = async () => {
        try {
            const todoDetails = await fetch("http://localhost:5000/api/v1/todos");
            const data = await todoDetails.json();
            setTodos(data.todos);
        } catch (err) {
            console.log("Error while fetching todos data");
        } 
    };

    // POST Request
    const addTask = async (e) => {
        e.preventDefault();
        try {
            const response = await fetch("http://localhost:5000/api/v1/todos", {
                method: "POST",
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ text: todo }),
            });
            const data = await response.json();
            setTodos([...todos, data.todo]);
            setTodo("");
        } catch (err) {
            console.log("Not able to add Todo");
        }
    };

    // DELETE Request
    const deleteTodo = async (deleteId) => {
        try {
            await fetch(`http://localhost:5000/api/v1/todos/${deleteId}`, {
                method: "DELETE",
                headers: { 'Content-Type': 'application/json' },
            });
            setTodos(todos.filter((t) => t._id !== deleteId));
        } catch (err) {
            console.log("Unable to delete Todo: ", err);
        }
    };

    // Trigger Inline Edit Mode
    const handleEdit = (editId) => {
        setEditIndex(editId);
        setEditTodo(todos.find(todo => todo._id === editId).text);
    };

    // PUT Request (Update Text)
    const handleSave = async (saveId) => {
        try {
            setEditIndex(null);
            const response = await fetch(`http://localhost:5000/api/v1/todos/${saveId}`, {
                method: "PUT",
                body: JSON.stringify({ text: editTodo }),
                headers: { 'Content-Type': 'application/json' }
            });
            const data = await response.json();
            setTodos(todos.map(todo => todo._id === saveId ? data.updatedTodo : todo));
            setEditIndex(null);
        } catch (err) {
            console.log("Not able to update Todo");
        }
    };

    // PUT Request (Mark as Complete)
    const handleDone = async (doneId) => {
        try {
            const response = await fetch(`http://localhost:5000/api/v1/todos/${doneId}`, {
                method: "PUT",
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ completed: true }),
            });
            const data = await response.json();
            setTodos(todos.map(todo => todo._id === doneId ? data.updatedTodo : todo));
        } catch (err) {
            console.log("Not able to mark todo as Done");
        }
    };

    return (
        <div>
            <h1>Todo List App</h1>

            <form onSubmit={addTask}>
                <input value={todo} onChange={(e) => setTodo(e.target.value)} />
                <button>Add Task</button>
            </form>

            <br /><br />
            <h2>Your Tasks</h2>
            {
                todos.map(todo => !todo.completed && (
                    <div key={todo._id}>
                        {todo._id === editIndex ? (
                            <input value={editTodo} onChange={(e) => setEditTodo(e.target.value)} />
                        ) : (
                            <p style={{ display: 'inline' }}>{todo.text}</p>
                        )}
                        
                        {todo._id === editIndex ? (
                            <button onClick={() => handleSave(todo._id)}>Save</button>
                        ) : (
                            <button onClick={() => handleEdit(todo._id)}>Edit</button>
                        )}
                        <button onClick={() => handleDone(todo._id)}>Done</button>
                    </div>
                ))
            }

            <br /><br />

            <h2>Completed Tasks</h2>
            {
                todos.map(todo => todo.completed && (
                    <div key={todo._id}>
                        <p style={{ display: 'inline' }}>{todo.text}</p>
                        <button onClick={() => deleteTodo(todo._id)}>Delete</button>
                    </div>
                ))
            }
        </div>
    );
};

export default TodoComponent;

```

### Key Frontend Takeaways

*   **State-Driven Inline Editing:** Instead of opening a modal or routing to a separate page, we use `editIndex` to store the active `_id` of the task being edited. When a task's `_id` matches `editIndex`, React conditionally swaps out the text element for an editable `<input />`.
    
*   **List Separation via UI Logic:** Rather than managing multiple database collections or separate states, the app relies on a single `todos` array and client-side filtering (`!todo.completed` vs `todo.completed`) to separate pending tasks from completed ones.
    

* * *

## Wrap Up

By putting these components together, you establish a reliable, real-time feedback loop between your database and client application. Next steps to elevate this project might include styled UI components (like Tailwind CSS), adding user authentication, or implementing loading states for better UX!