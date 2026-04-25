# Student Database Management System (SDMS)

Full-stack **Node.js + Express + MySQL** application with **vanilla HTML/CSS/JavaScript** frontend. It provides role-based access (**admin**, **teacher**, **student**), JWT authentication, bcrypt password hashing, parameterized SQL (SQL injection safe), audit logging, CSV export, and file uploads for profile photos and documents.

## Prerequisites

- **Node.js** 18+
- **MySQL** 8.x (or compatible MariaDB with JSON support for audit `details`)

## 1. Database setup

Create the database and load the schema (from the project root):

```bash
mysql -u root -p < database/schema.sql
```

Copy environment variables and edit DB credentials and `JWT_SECRET`:

```bash
copy .env.example .env
```

On Windows PowerShell you can use:

```powershell
Copy-Item .env.example .env
```

## 2. Install dependencies

```bash
npm install
```

## 3. Seed demo data (optional)

```bash
npm run seed
```

This creates sample users:

| Role    | Email              | Password    |
|---------|--------------------|------------|
| Admin   | sairajshirole2@gmail.com   | sairaj123  |
| Teacher | teacher@school.edu | Teacher@123|
| Student | student@school.edu | Student@123|

## 4. Run the server

```bash
npm start
```

Development with auto-restart (Node 18+):

```bash
npm run dev
```

- **API base:** `http://localhost:3000/api`
- **UI:** `http://localhost:3000/` or `http://localhost:3000/index.html` (the login file is `index.html` at the **project root**, not under `src/`)

Health check: `GET http://localhost:3000/api/health`

## Project layout

```
├── index.html           # Login page (project root)
├── css/app.css          # Shared stylesheet (served at /css/app.css)
├── js/api.js            # Fetch helper (served at /js/api.js)
├── database/
│   ├── schema.sql       # MySQL DDL (tables, indexes, FKs)
│   └── seed.js          # Demo data
├── public/              # App pages (dashboard, students, …)
├── src/
│   ├── app.js           # Express app, middleware, route mounting
│   ├── server.js        # HTTP listener
│   ├── config/          # DB pool, JWT
│   ├── controllers/     # Request handlers (MVC)
│   ├── middleware/      # Auth, roles, uploads, errors
│   ├── models/          # SQL data access
│   ├── routes/          # REST routes + validation
│   └── utils/           # Grades, CSV, audit helper, pagination
└── uploads/             # Profile images & documents (created at runtime)
```

## API overview (examples)

| Method | Path | Roles | Description |
|--------|------|-------|-------------|
| POST | `/api/auth/login` | Public | JWT login |
| GET | `/api/auth/me` | All | Current user |
| GET | `/api/dashboard` | All | Role-specific stats |
| GET/POST | `/api/students` | Admin; list for Teacher | CRUD / directory |
| GET/POST | `/api/teachers` | Admin | Staff |
| GET/POST/PUT/DELETE | `/api/courses` | Admin CUD; all read scoped | Courses |
| GET/POST/DELETE | `/api/courses/:courseId/enrollments` | Admin, Teacher | Enroll / unenroll |
| GET/POST | `/api/attendance` | Admin, Teacher (report); Student own | Attendance |
| GET/POST/PUT/DELETE | `/api/marks` | Admin, Teacher; Student own list | Marks + auto grade |
| GET | `/api/audit` | Admin | Audit log |
| GET | `/api/export/students.csv` | Admin | CSV export |
| GET/POST | `/api/students/:id/documents` | Assigned teacher / admin | Documents |

**Authorization:** send header `Authorization: Bearer <token>`.

**Grades:** letter grades are derived from percentage using `src/utils/grade.js`.

## Security notes

- Passwords are hashed with **bcrypt** (cost factor 12 in seed; registration paths use the same).
- Queries use **parameterized** `?` placeholders via `mysql2`.
- Inputs are validated with **express-validator** on routes.
- Use a **strong `JWT_SECRET`** in production and HTTPS in front of the API.

## Email notifications

Not implemented in code to keep dependencies minimal. You can add `nodemailer` and call it from controllers after enrollments or grade updates.

---

For issues, verify MySQL is running, the database exists, `.env` matches your server, and the schema applied without errors.
