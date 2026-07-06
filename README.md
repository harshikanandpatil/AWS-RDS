
# Project Overview

We'll build an **Employee Management System** using:

* Amazon Linux 2023
* Python Flask
* Amazon RDS PostgreSQL
* Gunicorn
* Nginx
* GitHub
* AWS EC2

---

# Step 1: Launch an EC2 Instance

1. Sign in to the AWS Console.
2. Go to **EC2**.
3. Click **Launch Instance**.
4. Configure:

   * **Name:** `postgresql-app`
   * **AMI:** Amazon Linux 2023
   * **Instance type:** `t2.micro` (or `t3.micro`)
   * Create or select a key pair.
5. Create a security group allowing:

   * SSH (22) from your IP
   * HTTP (80) from anywhere
   * HTTPS (443) from anywhere (optional)
6. Launch the instance.

---

# Step 2: Connect to EC2

```bash
ssh -i your-key.pem ec2-user@<EC2_PUBLIC_IP>
```

---

# Step 3: Update the Server

```bash
sudo dnf update -y
```

---

# Step 4: Install Required Software

```bash
sudo dnf install git python3 python3-pip postgresql15 nginx -y
```

Check the versions:

```bash
python3 --version
pip3 --version
psql --version
nginx -v
```

---

# Step 5: Create the Project Directory

```bash
mkdir postgresql-app
cd postgresql-app
```

---

# Step 6: Create a Virtual Environment

```bash
python3 -m venv venv
```

Activate it:

```bash
source venv/bin/activate
```

---

# Step 7: Install Python Packages

```bash
pip install flask psycopg2-binary gunicorn python-dotenv
```

Save dependencies:

```bash
pip freeze > requirements.txt
```

---

# Step 8: Create an Amazon RDS PostgreSQL Database

Create a PostgreSQL instance in Amazon RDS.

Use:

* Engine: PostgreSQL
* DB name: `employee_db`
* Username: `postgres`
* Choose a password
* Make it reachable from your EC2 instance (configure security groups accordingly)

---

# Step 9: Connect to PostgreSQL

```bash
psql -h <RDS_ENDPOINT> -U postgres -d postgres
```

Create your application database and tables.

---

# Step 10: Build the Flask Application

Create:

* `app.py`
* `database.py`
* `.env`
* `templates/`
* `static/`

We'll write all of these from scratch.

---

# Step 11: Run the App

```bash
python3 app.py
```

Visit:

```text
http://<EC2_PUBLIC_IP>:5000
```

---

# Step 12: Deploy with Gunicorn

```bash
gunicorn -w 4 -b 0.0.0.0:8000 app:app
```

---

# Step 13: Configure Nginx

Create a reverse proxy so users access the app on port 80 instead of the Flask development server.

Restart Nginx after adding the configuration.

---

# Step 14: Verify

Open:

```text
http://<EC2_PUBLIC_IP>
```

You should see the application running.

---

## I recommend we build this as a **10-part hands-on project**. In each part, I'll provide:

* Commands to run
* File contents
* Explanation of what each step does
* Expected output
* Troubleshooting tips

By the end, you'll have an original PostgreSQL + Flask application deployed on AWS that demonstrates the same concepts without copying another repository.
