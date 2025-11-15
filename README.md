# WEB LAB 5 REPORT
## ⚙️ Application Code Flow 

All requests are routed to the `StudentController` servlet, which is mapped to the URL pattern `/student`.

---

## A. List Students (Read)

This is the default flow when the application is opened.



1.  **Request:** User accesses `.../student` or `.../student?action=list`.
2.  **Controller:** The `doGet()` method in `StudentController` is triggered. It sees the `action` is "list" (or null) and calls its internal `listStudents()` method.
3.  **Model (DAO):** The `listStudents()` method calls `studentDAO.getAllStudents()`.
4.  **Database:** `StudentDAO` connects to the database, executes `SELECT * FROM students`, maps the `ResultSet` to a `List<Student>`, and returns this list to the Controller.
5.  **Controller:** The `listStudents()` method receives the `List<Student>`, sets it as a request attribute (e.g., `request.setAttribute("students", students)`), and forwards the request to the View.
6.  **View:** The `student-list.jsp` page receives the request. It uses a `<c:forEach>` tag to loop through the `${students}` attribute and renders the HTML table row by row.

---

## B. Create (Add New Student)

This flow is a two-step process: showing the form (GET) and processing the form (POST).



### Step 1: Show New Student Form (GET)

1.  **Request:** User clicks "Add New Student" on the list page, which links to `student?action=new`.
2.  **Controller:** `doGet()` in `StudentController` sees `action="new"` and calls `showNewForm()`.
3.  **Controller:** `showNewForm()` simply forwards the request to the `student-form.jsp`.
4.  **View:** `student-form.jsp` renders. It uses `<c:choose>` to check if a `${student}` object exists. Since it's a new student, the object is `null`, and the form is displayed empty. The hidden `action` field is set to `insert`.

### Step 2: Process New Student Form (POST)

1.  **Request:** User fills the blank form and clicks "Add Student". The form submits via `POST` to the `/student` controller, carrying the hidden `action="insert"`.
2.  **Controller:** The `doPost()` method in `StudentController` is triggered. It sees `action="insert"` and calls `insertStudent()`.
3.  **Controller:** `insertStudent()` gathers all form parameters (`request.getParameter(...)`), creates a new `Student` object, and passes it to the DAO by calling `studentDAO.addStudent(newStudent)`.
4.  **Model (DAO):** `addStudent()` runs an `INSERT INTO students ...` query.
5.  **Controller:** After the DAO method completes, `insertStudent()` performs a `response.sendRedirect()` back to the `student?action=list` URL (with a success message) to show the updated list.

---

## C. Update (Edit Student)

This is also a two-step process, very similar to "Create".



### Step 1: Show Edit Form (GET)

1.  **Request:** User clicks "Edit" on a specific student, which links to `student?action=edit&id=...`.
2.  **Controller:** `doGet()` in `StudentController` sees `action="edit"` and calls `showEditForm()`.
3.  **Controller:** `showEditForm()` gets the `id` from the request, then calls `studentDAO.getStudentById(id)` to fetch that student's data.
4.  **Model (DAO):** `getStudentById()` runs a `SELECT * FROM students WHERE id = ?` query and returns a single `Student` object.
5.  **Controller:** `showEditForm()` sets this existing `Student` object as a request attribute (e.g., `request.setAttribute("student", existingStudent)`) and forwards to `student-form.jsp`.
6.  **View:** `student-form.jsp` renders. This time, `${student}` is *not* `null`, so the form uses its data to pre-fill all the input fields (`value="${student.fullName}"`). The hidden `action` field is set to `update`.

### Step 2: Process Edit Form (POST)

1.  **Request:** User modifies the form and clicks "Update Student". The form submits via `POST` to `/student`, carrying the hidden `action="update"` and the hidden `id`.
2.  **Controller:** `doPost()` sees `action="update"` and calls `updateStudent()`.
3.  **Controller:** `updateStudent()` gathers all parameters (including the `id`), creates a `Student` object, and passes it to `studentDAO.updateStudent(student)`.
4.  **Model (DAO):** `updateStudent()` runs an `UPDATE students SET ... WHERE id = ?` query.
5.  **Controller:** After the update, `updateStudent()` redirects the user back to `student?action=list`.

---

## D. Delete (Remove Student)

This is a single-step `GET` request.



1.  **Request:** User clicks "Delete" on a specific student, which links to `student?action=delete&id=...`. A JavaScript `confirm()` dialog appears first.
2.  **Controller:** `doGet()` in `StudentController` sees `action="delete"` and calls `deleteStudent()`.
3.  **Controller:** `deleteStudent()` gets the `id` from the request and calls `studentDAO.deleteStudent(id)`.
4.  **Model (DAO):** `deleteStudent()` connects to the database and runs a `DELETE FROM students WHERE id = ?` query.
5.  **Controller:** After the DAO method completes, `deleteStudent()` redirects the user back to `student?action=list` with a success/error message.
