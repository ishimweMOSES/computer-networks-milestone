# 📘 computer networks 

📄 **Project Solution**  
Access the full solution using the link below:

🔗 [View Assignment Answer on Google Drive](https://drive.google.com/file/d/1rgnpqosILquyBqm6v4NB6vJOoZfAebln/view?usp=sharing)


# ⚖️ Smart Legal Case Tracker – Capstone Project Documentation

## 👤 Author
**Moise Ishimwe**  
🎓 Bachelor of Information Management  
🏛️ Adventist University of Central Africa (AUCA)

---

## 📘 Project Overview

**Smart Legal Case Tracker** is an advanced Oracle-based database system designed for modern law firms. It ensures secure, efficient, and automated management of clients, lawyers, cases, tasks, and billing records using a robust mix of SQL and PL/SQL programming.

---

## 🎯 Objectives

- 📊 **Efficiently organize** legal case-related data.
- 🧰 **Implement core operations** using DDL and DML.
- ⚙️ **Automate workflows** with procedures, functions, and packages.
- 🛡️ **Enforce security** using triggers and auditing mechanisms.
- 🔍 **Ensure transparency** by logging all user activity.

---

## 📚 Table of Contents

1. [Problem Statement](#-1-problem-statement)  
2. [Entity Relationship Design](#-2-entity-relationship-design)  
3. [SQL Table Creation & Insertions](#-3-sql-table-creation--insertions)  
4. [DML/DDL Operations](#-4-dmlddl-operations)  
5. [Procedures & Functions](#-5-procedures--functions)  
6. [Packages & Cursor Use](#-6-packages--cursor-use)  
7. [Triggers & Auditing](#-7-triggers--auditing)  
8. [Testing & Evidence](#-8-testing--evidence)  
9. [Summary](#-summary)  
10. [Recommendations](#-recommendations)  
11. [Contact](#-contact)  

---

## 🔹 1. Problem Statement

Manual legal case tracking leads to inefficiencies, data loss, and unauthorized access risks. Our system automates and secures every part of case handling.

> ✅ **Goal**: Enhance legal workflows with structured data control, logging, and automation for optimal integrity and performance.

---

## 🔹 2. Entity Relationship Design

### 📌 Core Entities

- 👤 **Clients**: Client information  
- ⚖️ **Cases**: Legal case data  
- 🧑‍💼 **Lawyers**: Lawyer details  
- 📋 **Tasks**: Assigned legal work  
- 💰 **Billing**: Services and payment logs  

📷 _ERD Diagram_  
**👉 [Insert ERD Screenshot Here]**

---

## 🔹 3. SQL Table Creation & Insertions

### 🗂️ Clients Table
```sql
CREATE TABLE Clients (
  Client_ID INT PRIMARY KEY,
  Name VARCHAR2(100),
  Contact_Info VARCHAR2(100) NOT NULL
);
```

### ➕ Sample Insert
```sql
INSERT INTO Clients VALUES (1, 'John Doe', 'john@example.com');
```

📷 _Data Output Screenshot_  
**👉 [Insert Screenshot: Table + Insert Results]**

---

## 🔹 4. DML/DDL Operations

### 🔄 Update Case
```sql
UPDATE Cases SET Status = 'Closed' WHERE Case_ID = 101;
```

### ❌ Delete Task
```sql
DELETE FROM Tasks WHERE Task_ID = 2;
```

### ➕ Add Billing Field
```sql
ALTER TABLE Billing ADD Transaction_Method VARCHAR2(50);
```

📷 _DML/DDL Output Screenshot_  
**👉 [Insert Screenshot: Output Confirmation]**

---

## 🔹 5. Procedures & Functions

### 🧪 Procedure: Get Case Details
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

### 🔍 Function: Get Lawyer Name
```sql
CREATE OR REPLACE FUNCTION get_lawyer_name (p_lawyer_id IN NUMBER)
RETURN VARCHAR2 IS
  v_name VARCHAR2(100);
BEGIN
  SELECT Name INTO v_name FROM Lawyers WHERE Lawyer_ID = p_lawyer_id;
  RETURN v_name;
END;
```

📷 _Procedure Output Screenshot_  
**👉 [Insert Screenshot: Execution Results]**

---

## 🔹 6. Packages & Cursor Use

### 📦 Package Specification
```sql
CREATE OR REPLACE PACKAGE case_pkg IS
  PROCEDURE show_case_status(p_case_id NUMBER);
  FUNCTION get_lawyer_name(p_lawyer_id NUMBER) RETURN VARCHAR2;
END case_pkg;
```

### 📦 Package Body
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

📷 _Package Output Screenshot_  
**👉 [Insert Screenshot: Package Execution Output]**

---

## 🔹 7. Triggers & Auditing

### 📅 Holidays Table
```sql
CREATE TABLE Holidays (
  holiday_date DATE PRIMARY KEY
);
```

### 🛡️ Audit Table
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

### 🚫 Trigger: Restrict DML on Holidays & Weekdays
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

📷 _Trigger Execution Screenshot_  
**👉 [Insert Screenshot: Compilation + Result]**

---

## 🔹 8. Testing & Evidence

### 🔁 Attempt to Update on Weekday
```sql
UPDATE Cases SET Status = 'Pending' WHERE Case_ID = 101;
```

### 📜 View Audit Trail
```sql
SELECT * FROM audit_log ORDER BY log_date DESC;
```

📷 _Audit Log Screenshot_  
**👉 [Insert Screenshot: Trigger Output & Audit Log View]**

---

## ✅ Summary

| Phase       | Description                              | Status  |
|-------------|------------------------------------------|---------|
| Phase I     | Problem Definition                       | ✅ Done |
| Phase II    | ERD & Entity Modeling                    | ✅ Done |
| Phase III   | Logical Design                           | ✅ Done |
| Phase IV    | Table Creation & Insertions              | ✅ Done |
| Phase V     | DML/DDL Operations                       | ✅ Done |
| Phase VI    | Procedures, Functions, Cursors, Packages | ✅ Done |
| Phase VII   | Triggers & Auditing                      | ✅ Done |

---

## 💡 Recommendations

- 📅 Regularly update the `Holidays` table to reflect non-working days.
- 🧑‍⚖️ Extend the system with a **web dashboard** for legal staff.
- 🔒 Introduce **user roles and privileges** for stricter access control.

---

## 📬 Contact

📧 Email: ishimwemugemamoise@gmail.com  
📞 Phone: 0789291093 | 0721412296  

🔗 _For collaboration, demo requests, or technical feedback, feel free to reach out!_

---

> 🚀 _Smart Legal Case Tracker empowers law firms with digital control, accountability, and legal intelligence._
