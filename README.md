# 📘 computer networks 

📄 **Project Solution**  
Access the full solution using the link below:

🔗 [View Assignment Answer on Google Drive](https://drive.google.com/file/d/1rgnpqosILquyBqm6v4NB6vJOoZfAebln/view?usp=sharing)

# 📚 Smart Legal Case Tracker – Capstone Project Documentation

## 👤 Author
**Moise Ishimwe**  
Bachelor of Information Management  
Adventist University of Central Africa (AUCA)

---

## 🧾 Project Description
**Smart Legal Case Tracker** is a complete legal case management database system that helps law firms manage and track clients, lawyers, tasks, billing, and cases more effectively using Oracle SQL and PL/SQL.

---

## ✅ Project Objectives
- 📂 Design a relational database to manage legal information.
- ✍️ Implement DML and DDL operations.
- 🔁 Automate operations using stored procedures and functions.
- 🔒 Secure the database using triggers and auditing.
- 📊 Track user activity and enforce restrictions using PL/SQL.

---

## 📌 Table of Contents
1. Problem Statement
2. Entity Relationship Design
3. SQL Table Creation & Insertions
4. DML/DDL Operations
5. Procedures & Functions
6. Packages & Cursor Use
7. Triggers & Auditing
8. Testing & Evidence

---

## 🔹 1. Problem Statement

Manual tracking of legal cases is inefficient and error-prone. This project solves that by introducing a database that supports automation, restrictions, and user accountability through advanced SQL and PL/SQL features.

> 🎯 We aim to prevent unauthorized data changes, automate workflows, and maintain integrity through strict controls and logging.

---

## 🔹 2. Entity Relationship Design

### Key Entities
- 👤 **Clients**: Stores client info
- ⚖️ **Cases**: Stores legal case details
- 🧑‍💼 **Lawyers**: Manages lawyer data
- 📋 **Tasks**: Tracks assigned legal tasks
- 💰 **Billing**: Logs services and payments

📸 _Screenshot Placeholder_  
**[Insert Screenshot: ERD Model]**

---

## 🔹 3. SQL Table Creation & Insertions

### Example: Clients Table
```sql
CREATE TABLE Clients (
  Client_ID INT PRIMARY KEY,
  Name VARCHAR2(100),
  Contact_Info VARCHAR2(100) NOT NULL
);
```
*Creates the Clients table with basic attributes.*

### Example Insert
```sql
INSERT INTO Clients VALUES (1, 'John Doe', 'john@example.com');
```
*Adds a sample client.*

📸 _Screenshot Placeholder_  
**[Insert Screenshot: Tables + Insert Data Output]**

---

## 🔹 4. DML/DDL Operations

### DML: Update a Case Status
```sql
UPDATE Cases SET Status = 'Closed' WHERE Case_ID = 101;
```
*Updates the status of a legal case.*

### DML: Delete Task
```sql
DELETE FROM Tasks WHERE Task_ID = 2;
```
*Removes a task record.*

### DDL: Alter Billing Table
```sql
ALTER TABLE Billing ADD Transaction_Method VARCHAR2(50);
```
*Adds a new column to store payment method.*

📸 _Screenshot Placeholder_  
**[Insert Screenshot: DML/DDL Success]**

---

## 🔹 5. Procedures & Functions

### Procedure: Get Case Details
```sql
CREATE OR REPLACE PROCEDURE get_case_details (p_case_id IN NUMBER) IS
  v_status VARCHAR2(50);
BEGIN
  SELECT status INTO v_status FROM Cases WHERE Case_ID = p_case_id;
  DBMS_OUTPUT.PUT_LINE('Case Status: ' || v_status);
EXCEPTION
  WHEN NO_DATA_FOUND THEN
    DBMS_OUTPUT.PUT_LINE('No such case found.');
END;
```
*Fetches and displays case status by ID.*

### Function: Get Lawyer Name
```sql
CREATE OR REPLACE FUNCTION get_lawyer_name (p_lawyer_id IN NUMBER)
RETURN VARCHAR2 IS
  v_name VARCHAR2(100);
BEGIN
  SELECT Name INTO v_name FROM Lawyers WHERE Lawyer_ID = p_lawyer_id;
  RETURN v_name;
END;
```
*Returns the name of a lawyer by ID.*

📸 _Screenshot Placeholder_  
**[Insert Screenshot: Procedure/Function Output]**

---

## 🔹 6. Packages & Cursor Use

### Package Spec
```sql
CREATE OR REPLACE PACKAGE case_pkg IS
  PROCEDURE show_case_status(p_case_id NUMBER);
  FUNCTION get_lawyer_name(p_lawyer_id NUMBER) RETURN VARCHAR2;
END case_pkg;
```

### Package Body
```sql
CREATE OR REPLACE PACKAGE BODY case_pkg IS
  PROCEDURE show_case_status(p_case_id NUMBER) IS
    v_status VARCHAR2(100);
  BEGIN
    SELECT status INTO v_status FROM Cases WHERE Case_ID = p_case_id;
    DBMS_OUTPUT.PUT_LINE('Status: ' || v_status);
  END;

  FUNCTION get_lawyer_name(p_lawyer_id NUMBER) RETURN VARCHAR2 IS
    v_name VARCHAR2(100);
  BEGIN
    SELECT name INTO v_name FROM Lawyers WHERE Lawyer_ID = p_lawyer_id;
    RETURN v_name;
  END;
END case_pkg;
```
*This package provides reusable logic for accessing case and lawyer data.*

📸 _Screenshot Placeholder_  
**[Insert Screenshot: Package Output]**

---

## 🔹 7. Triggers & Auditing

### Holiday Reference Table
```sql
CREATE TABLE Holidays (
  holiday_date DATE PRIMARY KEY
);
```
*Stores static public holidays.*

### Audit Table
```sql
CREATE TABLE audit_log (
  log_id NUMBER GENERATED ALWAYS AS IDENTITY,
  user_id VARCHAR2(100),
  log_date DATE,
  operation VARCHAR2(20),
  table_name VARCHAR2(50),
  status VARCHAR2(10)
);
```
*Logs user activity with timestamp and result.*

### Trigger: Restrict DML on Weekdays and Holidays
```sql
CREATE OR REPLACE TRIGGER trg_restrict_dml
BEFORE INSERT OR UPDATE OR DELETE ON Cases
FOR EACH ROW
DECLARE
  v_day VARCHAR2(10);
  v_today DATE := TRUNC(SYSDATE);
  v_holiday_count NUMBER;
BEGIN
  v_day := TO_CHAR(v_today, 'DY', 'NLS_DATE_LANGUAGE=ENGLISH');
  SELECT COUNT(*) INTO v_holiday_count FROM Holidays WHERE holiday_date = v_today;

  IF v_day IN ('MON','TUE','WED','THU','FRI') OR v_holiday_count > 0 THEN
    INSERT INTO audit_log(user_id, log_date, operation, table_name, status)
    VALUES (USER, SYSDATE, 'DML Attempt', 'Cases', 'Denied');
    RAISE_APPLICATION_ERROR(-20001, 'DML operations are not allowed today.');
  ELSE
    INSERT INTO audit_log(user_id, log_date, operation, table_name, status)
    VALUES (USER, SYSDATE, 'DML Attempt', 'Cases', 'Allowed');
  END IF;
END;
```
*Blocks insert, update, delete during weekdays and public holidays.*

📸 _Screenshot Placeholder_  
**[Insert Screenshot: Trigger Compilation & Result]**

---

## 🔹 8. Testing & Evidence

### Test Trigger (Expected to Fail on Weekday)
```sql
UPDATE Cases SET Status = 'Pending' WHERE Case_ID = 101;
```

### View Audit Log
```sql
SELECT * FROM audit_log ORDER BY log_date DESC;
```

📸 _Screenshot Placeholder_  
**[Insert Screenshot: Trigger Output or Audit Table]**

---

## 🧾 Summary

| Phase | Description | Status |
|-------|-------------|--------|
| Phase I | Problem Statement | ✅ Done |
| Phase II | ERD & Modeling | ✅ Done |
| Phase III | Logical Design | ✅ Done |
| Phase IV | Table Creation & Insertion | ✅ Done |
| Phase V | DML/DDL Operations | ✅ Done |
| Phase VI | Procedures, Functions, Cursors, Packages | ✅ Done |
| Phase VII | Triggers & Auditing | ✅ Done |

---

## 🧠 Recommendations
- 📅 Add more holiday entries each month to maintain restriction.
- 🖥️ Future improvement can include a UI and report generation module.
- 👥 Ensure roles and privileges are enforced for multi-user environments.

---

## 📧 Contact
For questions, demo or collaboration:  
📩 ishimwemugemamoise@gmail.com  
📞 0789291093 | 0721412296

---

_This README serves as comprehensive documentation of the Smart Legal Case Tracker database project. All critical SQL, PL/SQL logic, and business rules are implemented and tested as part of this work._
