If your goal is to **deploy a Node.js + PostgreSQL application on Amazon Linux using Amazon RDS**, follow this end-to-end process.

---

# Architecture

```text
                Internet
                    │
                    ▼
          Amazon EC2 (Amazon Linux 2023)
         Node.js + Express Application
                    │
          Port 3000 (or Nginx on Port 80)
                    │
                    ▼
         Amazon RDS PostgreSQL (Port 5432)
```

---

# Step 1: Launch an EC2 Instance

* AMI: Amazon Linux 2023
* Instance type: t2.micro or t3.micro (Free Tier if eligible)

### Security Group

| Type       | Port | Source                  |
| ---------- | ---- | ----------------------- |
| SSH        | 22   | Your IP                 |
| HTTP       | 80   | 0.0.0.0/0               |
| Custom TCP | 3000 | 0.0.0.0/0 (for testing) |

Connect:

```bash
ssh -i your-key.pem ec2-user@YOUR_PUBLIC_IP
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

# Step 3: Clone Your GitHub Repository

Replace with your repository URL:

```bash
git clone https://github.com/harshikanandpatil/employee-management.git

cd employee-management
```

---

# Step 4: Install Dependencies

```bash
npm install
```

---

# Step 5: Create an Amazon RDS PostgreSQL Database

In the AWS Console:

* Open **RDS**
* Create a **PostgreSQL** database
* Public access: **Yes** (for learning/demo)
* Save:

  * Endpoint
  * Username
  * Password

---

# Step 6: Configure RDS Security Group

Allow inbound traffic:

| Type       | Port | Source             |
| ---------- | ---- | ------------------ |
| PostgreSQL | 5432 | EC2 Security Group |

This is more secure than opening access to the internet.

---

# Step 7: Create the Database

Install the PostgreSQL client:

```bash
sudo dnf install postgresql15 -y
```

Connect:

```bash
psql \
-h YOUR_RDS_ENDPOINT \
-U postgres \
-d postgres
```

Create the database and table:

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

# Step 8: Create the `.env` File

```env
DB_HOST=YOUR_RDS_ENDPOINT
DB_USER=postgres
DB_PASSWORD=YOUR_PASSWORD
DB_NAME=employees
DB_PORT=5432
```

---

# Step 9: Start the Application

```bash
node server.js
```

Visit:

```text
http://EC2_PUBLIC_IP:3000
```

---

# Step 10 (Recommended): Use PM2

Install PM2:

```bash
sudo npm install -g pm2
```

Start your application:

```bash
pm2 start server.js --name employee-app
```

Save the PM2 process list:

```bash
pm2 save
```

Configure PM2 to start on boot:

```bash
pm2 startup
```

Run the command that PM2 outputs.

---

# Step 11 (Recommended): Configure Nginx

Install Nginx:

```bash
sudo dnf install nginx -y

sudo systemctl enable nginx
sudo systemctl start nginx
```

Create a reverse proxy configuration so users can access the app via port **80** instead of **3000**.

---

# Step 12: Test

Open:

```text
http://YOUR_EC2_PUBLIC_IP
```

You should see your Employee Management application.

---

## AWS Services Used

* Amazon EC2 (Amazon Linux 2023)
* Amazon RDS for PostgreSQL
* Security Groups
* IAM (optional)
* GitHub
* Nginx
* PM2

---

### Recommended Project Structure

```text
employee-management/
├── server.js
├── db.js
├── package.json
├── .env
├── public/
│   ├── style.css
│   └── script.js
├── views/
│   ├── index.ejs
│   └── add.ejs
└── README.md
```

This deployment demonstrates practical AWS skills, including EC2 provisioning, RDS integration, GitHub-based deployment, Node.js application hosting, and production process management with PM2.
