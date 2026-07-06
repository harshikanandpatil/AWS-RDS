
# Project: Smart Library Management System

### Architecture

```text
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
```

## Features

* 📚 Book management
* 👤 Student registration
* 📖 Borrow and return books
* 🔍 Search books
* 📊 Dashboard
* 📅 Borrow history
* ⭐ Responsive UI

---

# Folder Structure

```text
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
```

---

# Install on Amazon Linux

Update the system:

```bash
sudo dnf update -y
```

Install Node.js:

```bash
sudo dnf install nodejs git -y
```

Verify:

```bash
node -v
npm -v
```

---

# Create the Project

```bash
mkdir library-app
cd library-app
npm init -y
```

Install packages:

```bash
npm install express pg ejs dotenv
```

---

# Database Connection (`db.js`)

```javascript
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
```

---

# Server (`server.js`)

```javascript
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
```

---

# Database Schema

```sql
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
```

---

# Environment File

```text
DB_HOST=your-rds-endpoint
DB_USER=postgres
DB_PASSWORD=password
DB_NAME=library
```

---

# Start the Application

```bash
node server.js
```

Open:

```text
http://EC2-Public-IP:3000
```

---

# AWS Services Used

* Amazon EC2 (Amazon Linux)
* Amazon RDS for PostgreSQL
* IAM Role (optional for secure AWS access)
* Security Groups
* Amazon CloudWatch
* GitHub for source control

---

# Resume Highlights

* Built a full-stack Library Management System using Node.js, Express, and PostgreSQL.
* Deployed the application on Amazon EC2 (Amazon Linux).
* Integrated Amazon RDS PostgreSQL as the backend database.
* Configured security groups, networking, and database connectivity.
* Implemented CRUD operations for books, students, and borrowing records.

This project is more distinctive than a simple "visitor counter" or "student management" app and demonstrates practical cloud deployment, database integration, and full-stack development.
