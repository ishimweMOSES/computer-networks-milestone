# 📘 computer networks 

📄 **Project Solution**  
Access the full solution using the link below:

🔗 [View Assignment Answer on Google Drive](https://drive.google.com/file/d/1rgnpqosILquyBqm6v4NB6vJOoZfAebln/view?usp=sharing)

# ⚖️ Smart Legal Case Tracker – Oracle PL/SQL Project

## 👥 Group Info

* **Group Name:** TUES
* **Student Name:** Moise Ishimwe
* **Student ID:** 27657

---

## 🚨 Problem Statement

Law firms traditionally rely on fragmented paper records and manual spreadsheets to manage legal cases, deadlines, billing, and staff tasks. This often results in missed deadlines, unbilled services, and poor accountability.

### 🧠 Project Purpose

This project centralizes and automates the management of clients, legal cases, billing, tasks, and lawyer assignments in an Oracle PL/SQL environment.

### 👤 Users

* Law Firm Admins
* Lawyers
* Finance/Billing Officers

### 🎯 Objectives

* Streamline and digitize legal operations
* Enable deadline and task tracking
* Automate billing and reduce errors
* Provide audit trails and enforce data security

---

## 🏗️ Phase IV – Database Creation

```sql
-- Create pluggable database (run by sysdba)
CREATE PLUGGABLE DATABASE TUES_MOISE_SMARTLEGALCASETRACKER_DB
  ADMIN USER moise_admin IDENTIFIED BY moise
  FILE_NAME_CONVERT=('pdbseed','TUES_MOISE_SMARTLEGALCASETRACKER_DB');
```

### 🔐 Admin Credentials

* **PDB Name:** `TUES_MOISE_SMARTLEGALCASETRACKER_DB`
* **Admin User:** `moise_admin`
* **Password:** `moise`

### 📸 Screenshot Placeholder

*→ *Add screenshot from SQL Developer or Enterprise Manager showing your PDB opened.**

---

## 🧱 Phase V – Table Creation & Data Insertion

### 🔨 Table Definitions

```sql
CREATE TABLE client (
  client_id NUMBER PRIMARY KEY,
  name VARCHAR2(100) NOT NULL,
  email VARCHAR2(100),
  phone VARCHAR2(20)
);

CREATE TABLE lawyer (
  lawyer_id NUMBER PRIMARY KEY,
  name VARCHAR2(100) NOT NULL,
  email VARCHAR2(100),
  specialty VARCHAR2(50)
);

CREATE TABLE legal_case (
  case_id NUMBER PRIMARY KEY,
  title VARCHAR2(100),
  description VARCHAR2(255),
  client_id NUMBER REFERENCES client(client_id),
  lawyer_id NUMBER REFERENCES lawyer(lawyer_id),
  start_date DATE,
  status VARCHAR2(20) CHECK (status IN ('OPEN', 'CLOSED'))
);

CREATE TABLE task (
  task_id NUMBER PRIMARY KEY,
  case_id NUMBER REFERENCES legal_case(case_id),
  description VARCHAR2(255),
  assigned_to NUMBER REFERENCES lawyer(lawyer_id),
  due_date DATE,
  status VARCHAR2(20)
);

CREATE TABLE billing (
  billing_id NUMBER PRIMARY KEY,
  case_id NUMBER REFERENCES legal_case(case_id),
  amount NUMBER(10,2),
  billing_date DATE,
  status VARCHAR2(20)
);
```

### 🧾 Sample Data Insertions

```sql
-- Clients
INSERT INTO client VALUES (1, 'Alice Smith', 'alice@email.com', '072100001');
INSERT INTO client VALUES (2, 'Bob Johnson', 'bob@email.com', '072100002');
INSERT INTO client VALUES (3, 'Cathy Brown', 'cathy@email.com', '072100003');

-- Lawyers
INSERT INTO lawyer VALUES (1, 'John Doe', 'john@law.com', 'Civil');
INSERT INTO lawyer VALUES (2, 'Emma Davis', 'emma@law.com', 'Criminal');
INSERT INTO lawyer VALUES (3, 'Mark Smith', 'mark@law.com', 'Corporate');

-- Cases
INSERT INTO legal_case VALUES (1, 'Divorce Filing', 'Mutual separation case', 1, 1, SYSDATE, 'OPEN');
INSERT INTO legal_case VALUES (2, 'Fraud Defense', 'Client accused of fraud', 2, 2, SYSDATE, 'OPEN');
INSERT INTO legal_case VALUES (3, 'Contract Review', 'Corporate contract audit', 3, 3, SYSDATE, 'OPEN');

-- Tasks
INSERT INTO task VALUES (1, 1, 'Collect financial docs', 1, SYSDATE + 5, 'PENDING');
INSERT INTO task VALUES (2, 2, 'Draft court motion', 2, SYSDATE + 3, 'PENDING');
INSERT INTO task VALUES (3, 3, 'Review clauses', 3, SYSDATE + 7, 'PENDING');

-- Billing
INSERT INTO billing VALUES (1, 1, 250000, SYSDATE, 'UNPAID');
INSERT INTO billing VALUES (2, 2, 450000, SYSDATE, 'UNPAID');
INSERT INTO billing VALUES (3, 3, 300000, SYSDATE, 'UNPAID');
```

### 📸 Screenshot Placeholder

*→ *Add screenshot after inserting data into each table.**

---

## 🔁 Phase VI – Procedures, Cursors, Packages

### 📌 Stored Procedure – Get Case Details

```sql
CREATE OR REPLACE PROCEDURE fetch_case_info(p_case_id NUMBER) IS
  v_title legal_case.title%TYPE;
  v_status legal_case.status%TYPE;
BEGIN
  SELECT title, status INTO v_title, v_status FROM legal_case WHERE case_id = p_case_id;
  DBMS_OUTPUT.PUT_LINE('Title: ' || v_title || ' | Status: ' || v_status);
EXCEPTION
  WHEN NO_DATA_FOUND THEN
    DBMS_OUTPUT.PUT_LINE('No case found.');
END;
```

### 🔁 Cursor – List All Pending Tasks

```sql
DECLARE
  CURSOR task_cursor IS
    SELECT task_id, description FROM task WHERE status = 'PENDING';
BEGIN
  FOR task_rec IN task_cursor LOOP
    DBMS_OUTPUT.PUT_LINE('Task #' || task_rec.task_id || ': ' || task_rec.description);
  END LOOP;
END;
```

### 📦 Package – Mark Billing Paid

```sql
CREATE OR REPLACE PACKAGE billing_ops AS
  PROCEDURE mark_paid(p_case_id NUMBER);
END billing_ops;

CREATE OR REPLACE PACKAGE BODY billing_ops AS
  PROCEDURE mark_paid(p_case_id NUMBER) IS
  BEGIN
    UPDATE billing SET status = 'PAID' WHERE case_id = p_case_id;
  END;
END billing_ops;
```

### 📸 Screenshot Placeholder

*→ *Add screenshot after executing procedures, cursors, or packages.**

---

## 🔐 Phase VII – Advanced Programming & Auditing

### 📅 Holidays Table

```sql
CREATE TABLE holidays (
  holiday_date DATE PRIMARY KEY,
  description VARCHAR2(100)
);

INSERT INTO holidays VALUES (TO_DATE('2025-06-01', 'YYYY-MM-DD'), 'Independence Day');
```

### ⛔ Trigger – Restrict on Weekdays & Holidays

```sql
CREATE OR REPLACE TRIGGER block_weekday_ops
BEFORE INSERT OR UPDATE OR DELETE ON legal_case
BEGIN
  IF TO_CHAR(SYSDATE, 'DY') IN ('MON','TUE','WED','THU','FRI') OR
     EXISTS (SELECT 1 FROM holidays WHERE holiday_date = TRUNC(SYSDATE)) THEN
    RAISE_APPLICATION_ERROR(-20001, 'Operations blocked during weekdays or holidays.');
  END IF;
END;
```

### 🛡️ Audit Log

```sql
CREATE TABLE audit_log (
  log_id NUMBER GENERATED ALWAYS AS IDENTITY,
  user_id VARCHAR2(50),
  action_time TIMESTAMP,
  operation VARCHAR2(30),
  status VARCHAR2(10)
);
```

### 🔍 Audit Trigger

```sql
CREATE OR REPLACE TRIGGER log_case_change
AFTER INSERT OR UPDATE OR DELETE ON legal_case
BEGIN
  INSERT INTO audit_log(user_id, action_time, operation, status)
  VALUES (USER, SYSTIMESTAMP, 'CASE_CHANGE', 'LOGGED');
END;
```

### 📸 Screenshot Placeholder

*→ *Add screenshot of logs or restriction error message.**

---

## ✅ Final Notes

* 📂 Add your screenshots above for each phase
* 💾 Ensure all SQL code is committed in GitHub
* 📚 Make your README visually clear using sections, emojis, and code blocks

> 🏁 **Next Phase:** Final Documentation and Demonstration
