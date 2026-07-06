Project: Smart Library Management System
Architecture
                 Internet
                     │
                     ▼
              Amazon EC2 (Amazon Linux)
          Node.js + Express Application
                     │
                     ▼
          Amazon RDS PostgreSQL Database
                     │
                     ▼
            CloudWatch Logs & Metrics
Features
📚 Book management
👤 Student registration
📖 Borrow and return books
🔍 Search books
📊 Dashboard
📅 Borrow history
⭐ Responsive UI
Folder Structure
library-app/
│
├── server.js
├── package.json
├── db.js
├── public/
│     ├── css/
│     ├── js/
│     └── images/
│
├── views/
│     ├── index.ejs
│     ├── books.ejs
│     ├── users.ejs
│     └── borrow.ejs
│
└── sql/
      └── schema.sql
Install on Amazon Linux

Update the system:

sudo dnf update -y

Install Node.js:

sudo dnf install nodejs git -y

Verify:

node -v
npm -v
Create the Project
mkdir library-app
cd library-app
npm init -y

Install packages:

npm install express pg ejs dotenv
Database Connection (db.js)
const { Pool } = require("pg");

const pool = new Pool({
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
    port: 5432,
    ssl: {
        rejectUnauthorized: false
    }
});

module.exports = pool;
Server (server.js)
const express = require("express");
const pool = require("./db");

const app = express();

app.set("view engine", "ejs");
app.use(express.static("public"));

app.get("/", async (req, res) => {

    const books = await pool.query("SELECT * FROM books");

    res.render("index", {
        books: books.rows
    });

});

app.listen(3000, () => {
    console.log("Running...");
});
Database Schema
CREATE TABLE books (

id SERIAL PRIMARY KEY,

title VARCHAR(100),

author VARCHAR(100),

category VARCHAR(50),

available BOOLEAN DEFAULT TRUE

);

CREATE TABLE students(

id SERIAL PRIMARY KEY,

name VARCHAR(100),

email VARCHAR(100)

);

CREATE TABLE borrow(

id SERIAL PRIMARY KEY,

student_id INT REFERENCES students(id),

book_id INT REFERENCES books(id),

borrow_date DATE,

return_date DATE

);
Environment File
DB_HOST=your-rds-endpoint
DB_USER=postgres
DB_PASSWORD=password
DB_NAME=library
Start the Application
node server.js

Open:

http://EC2-Public-IP:3000
AWS Services Used
Amazon EC2 (Amazon Linux)
Amazon RDS for PostgreSQL
IAM Role (optional for secure AWS access)
Security Groups
Amazon CloudWatch
GitHub for source control


This project is more distinctive than a simple "visitor counter" or "student management" app and demonstrates practical cloud deployment, database integration, and full-stack development.
