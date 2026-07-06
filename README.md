
## Project: Employee Management System

### Technologies

* **Operating System:** Amazon Linux 2023
* **Backend:** Node.js + Express
* **Database:** PostgreSQL (Amazon RDS)
* **Frontend:** HTML, CSS, JavaScript, EJS

---

## Project Structure

```text
employee-management/
│── server.js
│── db.js
│── package.json
│── .env
│── public/
│   ├── style.css
│── views/
│   ├── index.ejs
│   ├── add.ejs
│── routes/
│   ├── employees.js
```

---

# Step 1: Launch an Amazon Linux EC2 Instance

* Amazon Linux 2023
* Allow ports:

  * SSH (22)
  * HTTP (80)
  * Custom TCP (3000)

Connect:

```bash
ssh -i your-key.pem ec2-user@YOUR-EC2-IP
```

---

# Step 2: Install Node.js

```bash
sudo dnf update -y
sudo dnf install nodejs git -y
```

Verify:

```bash
node -v
npm -v
```

---

# Step 3: Create Project

```bash
mkdir employee-management
cd employee-management

npm init -y
```

Install packages:

```bash
npm install express pg dotenv ejs
```

---

# Step 4: Create `.env`

```env
DB_HOST=your-rds-endpoint.amazonaws.com
DB_USER=postgres
DB_PASSWORD=YourPassword
DB_NAME=employees
DB_PORT=5432
```

---

# Step 5: Database Connection (`db.js`)

```javascript
require("dotenv").config();

const { Pool } = require("pg");

const pool = new Pool({
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
    port: process.env.DB_PORT,
    ssl: {
        rejectUnauthorized: false
    }
});

module.exports = pool;
```

---

# Step 6: Server (`server.js`)

```javascript
const express = require("express");
const db = require("./db");

const app = express();

app.set("view engine", "ejs");
app.use(express.urlencoded({ extended: true }));
app.use(express.static("public"));

app.get("/", async (req, res) => {
    const result = await db.query("SELECT * FROM employees ORDER BY id");
    res.render("index", { employees: result.rows });
});

app.get("/add", (req, res) => {
    res.render("add");
});

app.post("/add", async (req, res) => {
    const { name, department, salary } = req.body;

    await db.query(
        "INSERT INTO employees(name, department, salary) VALUES($1,$2,$3)",
        [name, department, salary]
    );

    res.redirect("/");
});

app.listen(3000, () => {
    console.log("Server running on port 3000");
});
```

---

# Step 7: PostgreSQL Table

Connect to your RDS instance using `psql` and run:

```sql
CREATE DATABASE employees;

\c employees

CREATE TABLE employees(
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(100),
    salary INT
);
```

---

# Step 8: `views/index.ejs`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Employee Management</title>
    <link rel="stylesheet" href="/style.css">
</head>
<body>

<h1>Employee Management System</h1>

<a href="/add">Add Employee</a>

<table border="1">

<tr>
<th>ID</th>
<th>Name</th>
<th>Department</th>
<th>Salary</th>
</tr>

<% employees.forEach(emp=>{ %>

<tr>
<td><%= emp.id %></td>
<td><%= emp.name %></td>
<td><%= emp.department %></td>
<td><%= emp.salary %></td>
</tr>

<% }) %>

</table>

</body>
</html>
```

---

# Step 9: `views/add.ejs`

```html
<!DOCTYPE html>
<html>

<head>
<title>Add Employee</title>
</head>

<body>

<h2>Add Employee</h2>

<form method="POST" action="/add">

<input type="text" name="name" placeholder="Employee Name" required>

<input type="text" name="department" placeholder="Department" required>

<input type="number" name="salary" placeholder="Salary" required>

<button>Add Employee</button>

</form>

</body>

</html>
```

---

# Step 10: `public/style.css`

```css
body{
font-family:Arial;
margin:40px;
background:#f4f4f4;
}

table{
width:80%;
border-collapse:collapse;
background:white;
}

th,td{
padding:10px;
text-align:center;
}

th{
background:#0077cc;
color:white;
}

a{
padding:10px;
background:green;
color:white;
text-decoration:none;
}
```

---

# Step 11: Run the Application

```bash
node server.js
```

Open in your browser:

```text
http://YOUR-EC2-PUBLIC-IP:3000
```

---

## AWS Services Used

* Amazon EC2 (Amazon Linux)
* Amazon RDS for PostgreSQL
* Security Groups
* IAM (optional)
* GitHub (for source control)

## Possible Enhancements

You can make this project stand out by adding:

* User authentication (login/logout)
* Edit and delete employee records
* Employee profile photos (stored in Amazon S3)
* File upload support
* Search and filter functionality
* Pagination
* Deployment behind a reverse proxy such as Nginx
* HTTPS with a TLS certificate
* CI/CD using GitHub Actions

This project is suitable for showcasing your skills in Node.js, PostgreSQL, and AWS deployment.
