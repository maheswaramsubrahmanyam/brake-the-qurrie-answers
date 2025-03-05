# brake-the-qurrie-answers



# SQL Challenges and Solutions

## ✅ Corrected Queries

### **Query 1: Get students who have never submitted an assignment but are enrolled in a course**
```sql
SELECT s.StudentID, s.Name
FROM Students s
JOIN Enrollments e ON s.StudentID = e.StudentID
LEFT JOIN Submissions sub ON s.StudentID = sub.StudentID
WHERE sub.StudentID IS NULL;
```
**Fixes:**
- Used `LEFT JOIN` instead of `WHERE NOT IN` to check if a student has no submissions.
- Ensures students are enrolled in a course.

---

### **Query 2: Retrieve the average grade per semester for each student**
```sql
SELECT e.StudentID, s.Name, e.Semester, 
       AVG(CASE e.Grade 
           WHEN 'A' THEN 4.0
           WHEN 'B' THEN 3.0
           WHEN 'C' THEN 2.0
           WHEN 'D' THEN 1.0
           WHEN 'F' THEN 0.0
           ELSE NULL END) AS AvgGPA
FROM Enrollments e
JOIN Students s ON e.StudentID = s.StudentID
GROUP BY e.StudentID, s.Name, e.Semester;
```
**Fixes:**
- Converted letter grades into numerical GPA.
- Grouped results by `StudentID`, `Name`, and `Semester`.

---

###  **Query 3: Retrieve students enrolled in "Computer Science" with a score > 80**
```sql
SELECT DISTINCT s.Name 
FROM Students s
JOIN Enrollments e ON s.StudentID = e.StudentID
JOIN Courses c ON e.CourseID = c.CourseID
JOIN Submissions sub ON s.StudentID = sub.StudentID
JOIN Assignments a ON sub.AssignmentID = a.AssignmentID
WHERE c.CourseName = 'Computer Science' AND sub.Score > 80;

```

### **Query 4: Find professor names who teach courses where at least one student got an 'A'**
```sql
SELECT DISTINCT p.Name 
FROM Professors p
JOIN Courses c ON p.Department = c.CourseName  -- Assuming department matches course name
JOIN Enrollments e ON c.CourseID = e.CourseID
WHERE e.Grade = 'A';
```


### **Query 5: Get the number of students who have failed at least one course**
```sql
SELECT COUNT(DISTINCT e.StudentID) AS FailedStudents
FROM Enrollments e
WHERE e.Grade = 'F';
```
**Fixes:**
- Used `DISTINCT` to count unique students.
- Ensured only students with `Grade = 'F'` are included.

---

### **Query 1: Find students who have never submitted an assignment but are enrolled in a course**
```sql
SELECT s.StudentID, s.Name
FROM Students s
JOIN Enrollments e ON s.StudentID = e.StudentID
LEFT JOIN Submissions sub ON s.StudentID = sub.StudentID
WHERE sub.StudentID IS NULL;

```
**Fixes:**
- Used `LEFT JOIN` instead of `NOT IN` for better performance and handling of `NULL` values.
- Checked `WHERE sub.StudentID IS NULL` to ensure students with no submissions are included.

---

### **Query 2: Find students who submitted assignments after the due date more than twice**
```sql
SELECT s.Name, COUNT(sub.SubmissionID) AS LateSubmissions
FROM Students s
JOIN Submissions sub ON s.StudentID = sub.StudentID
JOIN Assignments a ON sub.AssignmentID = a.AssignmentID
WHERE sub.SubmissionDate > a.DueDate
GROUP BY s.StudentID, s.Name
HAVING COUNT(sub.SubmissionID) > 2;

```
**Fixes:**
- Added `GROUP BY s.StudentID, s.Name` to properly count late submissions per student.
- Ensured `HAVING COUNT(sub.SubmissionID) > 2` works correctly after aggregation.

---

### **Query 3: Retrieve the GPA of each student who has an average grade greater than 3.0**
```sql
SELECT s.StudentID, s.Name, AVG(e.Grade) AS GPA  
FROM Students s
JOIN Enrollments e ON s.StudentID = e.StudentID
GROUP BY s.StudentID, s.Name
HAVING AVG(e.Grade) > 3.0;

```
**Fixes:**
- Added ` GROUP BY s.StudentID, s.Name` to ensure correct aggregation.
- Used `AVG(e.Grade)` in HAVING instead of GPA since aliasing doesn't work in HAVING.

---
### **Query 4: Retrieve Student, Course, Professor, and Submission Data**
```sql
SELECT 
    s.StudentID, 
    s.Name AS StudentName, 
    COALESCE(c.CourseName, 'No Course') AS CourseName, 
    COALESCE(p.Name, 'No Professor') AS ProfessorName, 
    COALESCE(sub.LatestSubmissionDate, 'No Submission') AS LatestSubmissionDate, 
    COALESCE(CAST(sub.LatestScore AS CHAR), 'No Submission') AS LatestScore
FROM Students s
LEFT JOIN Enrollments e ON s.StudentID = e.StudentID
LEFT JOIN Courses c ON e.CourseID = c.CourseID
LEFT JOIN Professors p ON c.ProfessorID = p.ProfessorID
LEFT JOIN (
    SELECT sub1.StudentID, sub1.CourseID, MAX(sub1.SubmissionDate) AS LatestSubmissionDate, sub1.Score
    FROM Submissions sub1
    JOIN (
        SELECT StudentID, CourseID, MAX(SubmissionDate) AS MaxDate
        FROM Submissions
        GROUP BY StudentID, CourseID
    ) sub2 
    ON sub1.StudentID = sub2.StudentID AND sub1.CourseID = sub2.CourseID AND sub1.SubmissionDate = sub2.MaxDate
) sub ON s.StudentID = sub.StudentID AND e.CourseID = sub.CourseID;


```


**Fixes:**

- Fixed `JOIN` condition for Professors: `c.ProfessorID = p.ProfessorID`.
- Used `LEFT JOIN` consistently instead of `RIGHT JOIN` for better results.
- Improved subquery logic to fetch the latest submission using `GROUP BY StudentID, CourseID`.
- Used `COALESCE(CAST(sub.LatestScore AS CHAR), 'No Submission')` to avoid data type mismatch.

### **Query 5: List courses that have more than 5 unique students enrolled**
```sql
SELECT c.CourseName, COUNT(DISTINCT e.StudentID) AS UniqueStudents
FROM Courses c
JOIN Enrollments e ON c.CourseID = e.CourseID
GROUP BY c.CourseID, c.CourseName
HAVING COUNT(DISTINCT e.StudentID) > 5;

```

**Fixes:**
- Grouped by `c.CourseID, c.CourseName` for clarity.
- Used `COUNT(DISTINCT e.StudentID)` to count unique enrollments.

## 🎯 **Key Takeaways**
✅ Fixed incorrect `JOIN` conditions.  
✅ Ensured `HAVING` is used correctly for aggregate filtering.  
✅ Used `COALESCE()` to handle `NULL` values properly.  
✅ Used proper `GROUP BY` clauses to avoid SQL errors.  

These queries now produce the expected results based on the provided database schema! 🚀
