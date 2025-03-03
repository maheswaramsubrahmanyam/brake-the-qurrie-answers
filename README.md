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

### **Query 3: List professors who have at least one student enrolled in a course they are teaching**
```sql
SELECT DISTINCT p.ProfessorID, p.Name
FROM Professors p
JOIN Courses c ON p.ProfessorID = c.ProfessorID
JOIN Enrollments e ON c.CourseID = e.CourseID;
```
**Fixes:**
- Corrected `JOIN` condition between `Professors` and `Courses`.
- Ensured only professors with students enrolled are included.

---

### **Query 4: Find students who submitted assignments after the due date more than twice**
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
- Added `GROUP BY` to aggregate late submissions.
- Used `HAVING` to filter students with more than 2 late submissions.

---

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

### **Query 6: Retrieve the GPA of each student who has an average grade greater than 3.0**
```sql
SELECT s.StudentID, s.Name, 
       AVG(CASE e.Grade 
           WHEN 'A' THEN 4.0
           WHEN 'B' THEN 3.0
           WHEN 'C' THEN 2.0
           WHEN 'D' THEN 1.0
           WHEN 'F' THEN 0.0
           ELSE NULL END) AS GPA
FROM Students s
JOIN Enrollments e ON s.StudentID = e.StudentID
GROUP BY s.StudentID, s.Name
HAVING GPA > 3.0;
```
**Fixes:**
- Used `AVG()` to compute GPA.
- Converted letter grades to numerical values.
- Added `HAVING GPA > 3.0`.

---

### **Query 7: Retrieve Student, Course, Professor, and Submission Data (Complex Query)**
```sql
SELECT 
    s.StudentID, 
    s.Name AS StudentName, 
    c.CourseName, 
    p.Name AS ProfessorName, 
    COALESCE(sub.LatestSubmissionDate, 'No Submission') AS LatestSubmissionDate, 
    COALESCE(sub.LatestScore, 'No Submission') AS LatestScore
FROM Students s
LEFT JOIN Enrollments e ON s.StudentID = e.StudentID
LEFT JOIN Courses c ON e.CourseID = c.CourseID
LEFT JOIN Professors p ON c.ProfessorID = p.ProfessorID
LEFT JOIN (
    SELECT sub.StudentID, sub.AssignmentID, 
           MAX(sub.SubmissionDate) AS LatestSubmissionDate, 
           sub.Score AS LatestScore
    FROM Submissions sub
    GROUP BY sub.StudentID, sub.AssignmentID
) sub ON s.StudentID = sub.StudentID;
```
**Fixes:**
- Corrected `JOIN` between `Professors` and `Courses`.
- Used `LEFT JOIN` to include students without courses.
- Fixed subquery to fetch the latest assignment submission.

---

### **Query 8: List courses that have more than 5 unique students enrolled**
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

---
###9 Retrieve Student, Course, Professor, and Submission Data
```sql
WITH LatestSubmissions AS (
    SELECT 
        s1.StudentID, 
        s1.AssignmentID, 
        s1.SubmissionDate, 
        s1.Score
    FROM Submissions s1
    JOIN (
        SELECT StudentID, MAX(SubmissionDate) AS LatestDate
        FROM Submissions
        GROUP BY StudentID
    ) s2 ON s1.StudentID = s2.StudentID AND s1.SubmissionDate = s2.LatestDate
)
SELECT 
    s.StudentID, 
    s.Name AS StudentName, 
    c.CourseName, 
    p.Name AS ProfessorName, 
    COALESCE(ls.SubmissionDate, 'No Submission') AS LatestSubmissionDate, 
    COALESCE(CAST(ls.Score AS VARCHAR), 'No Submission') AS LatestScore
FROM Students s
LEFT JOIN Enrollments e ON s.StudentID = e.StudentID
LEFT JOIN Courses c ON e.CourseID = c.CourseID
LEFT JOIN Professors p ON c.ProfessorID = p.ProfessorID
LEFT JOIN LatestSubmissions ls ON s.StudentID = ls.StudentID;

```





## 🎯 **Key Takeaways**
✅ Fixed incorrect `JOIN` conditions.  
✅ Ensured `HAVING` is used correctly for aggregate filtering.  
✅ Used `COALESCE()` to handle `NULL` values properly.  
✅ Used proper `GROUP BY` clauses to avoid SQL errors.  

These queries now produce the expected results based on the provided database schema! 🚀
