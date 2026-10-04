# LearnHub

LearnHub is a MERN Stack Learning Management System built as a college final project. It provides course browsing, free student enrollment, lesson access, progress tracking, and role-based dashboards for students, instructors, and administrators.

Course prices are retained as demonstration values and displayed in PKR. Enrollment is free; the project does not process payments.

## Main Features

- Public course catalog with title and description search and category filtering.
- Public Home page statistics for courses, instructors, and students.
- Account registration and login using JSON Web Tokens (JWT).
- Free enrollment in courses and student progress tracking.
- Lesson access restricted to enrolled students, the course instructor, and administrators.
- Course and lesson management for instructors.
- User, course, and analytics management for administrators.

## Student Features

- Register as a student and log in.
- Browse, search, and filter courses.
- Enroll in courses without payment.
- View lessons after enrolling.
- View enrolled courses and update a course's progress percentage.

## Instructor Features

- Register as an instructor and log in.
- Create, edit, and delete owned courses.
- Add, edit, and delete lessons for owned courses.
- View lessons belonging to owned courses.

## Admin Features

- Create or update an administrator account through the backend setup script.
- View user, course, and enrollment counts.
- View users and change user roles.
- Delete users other than the currently logged-in administrator.
- View and delete courses.
- Access lessons across courses.

## Technologies

- MongoDB Atlas and Mongoose
- Express.js and Node.js
- React and Vite
- Axios for frontend API requests
- Bootstrap for styling
- JSON Web Tokens for authentication
- bcryptjs for password hashing

## Project Structure

```text
LearnHub/
├── backend/
│   ├── config/             # Database connection
│   ├── controllers/        # API request handlers
│   ├── middleware/         # Authentication and role checks
│   ├── models/             # Mongoose models
│   ├── routes/             # Express API routes
│   ├── createAdmin.js      # Create or update an admin account
│   ├── seedData.js         # Seed sample instructor, courses, and lessons
│   ├── server.js           # Express server entry point
│   └── .env.example        # Environment variable template
└── frontend/
	 ├── src/
	 │   ├── components/     # Shared React components
	 │   ├── pages/          # Home, course, auth, and role dashboards
	 │   └── services/       # Axios API client and helpers
	 ├── index.html
	 └── package.json
```

## Requirements

- Node.js 18 or newer and npm.
- A MongoDB Atlas cluster, or another MongoDB instance reachable by the backend.
- Network access to the MongoDB server from the development machine.

## Environment Variables

The backend reads environment variables from `backend/.env`. Start with `backend/.env.example`; copy it to `.env` if a private `.env` file does not already exist. If one already exists, add the required keys without overwriting its database connection string.

```env
MONGO_URI=
JWT_SECRET=

ADMIN_NAME=
ADMIN_EMAIL=
ADMIN_PASSWORD=

INSTRUCTOR_NAME=
INSTRUCTOR_EMAIL=
INSTRUCTOR_PASSWORD=
LEGACY_INSTRUCTOR_EMAIL=
```

- `MONGO_URI`: MongoDB connection string from Atlas.
- `JWT_SECRET`: a long, random secret used to sign authentication tokens.
- `ADMIN_PASSWORD`: required by the admin creation script. The script stops with an error if it is missing.
- `ADMIN_NAME` and `ADMIN_EMAIL`: administrator account details.
- `INSTRUCTOR_NAME`, `INSTRUCTOR_EMAIL`, and `INSTRUCTOR_PASSWORD`: required by the sample data seeder to create or update its instructor account.
- `LEGACY_INSTRUCTOR_EMAIL`: optional; identifies an existing demo instructor account when migrating it to the configured instructor email.

Choose unique, strong values for the JWT secret and passwords. Do not put secrets in frontend files, commit a real `.env` file, or share secret values. The backend and frontend `.gitignore` files exclude `.env` files. The frontend uses `VITE_API_URL`; for local development, its value is `http://localhost:5000/api`.

## MongoDB Atlas Setup

1. Create a MongoDB Atlas cluster.
2. Create a database user with a strong password. This database user is separate from LearnHub admin and instructor accounts.
3. Add your development machine's public IP address under Atlas Network Access.
4. Copy the driver's connection string and place it in `MONGO_URI` in `backend/.env`. Replace the database username, password, cluster host, and database name with your own values. URL-encode reserved characters in the database password.
5. Keep database network access limited to the addresses that need it. Do not use a broad public allow-list for production.

## Local Installation

Open a terminal in the project root and install backend dependencies:

```powershell
cd backend
npm install
```

Create `backend/.env` from `backend/.env.example` if it does not exist, then fill in the environment variables. Keep the private `.env` file out of version control.

From the `backend` directory in PowerShell, create it with:

```powershell
Copy-Item .env.example .env
```

Install frontend dependencies in a second terminal:

```powershell
cd frontend
npm install
```

For local frontend development, create `frontend/.env` with:

```env
VITE_API_URL=http://localhost:5000/api
```

## Backend Setup

From the `backend` directory, configure `MONGO_URI` and `JWT_SECRET` in `.env`. The server connects to MongoDB and uses port `5000` by default; it uses the `PORT` environment variable when provided.

To create the configured admin account:

```powershell
npm run create-admin
```

Set `ADMIN_PASSWORD` before running this command. The password is hashed with bcrypt and is not printed. If the configured admin already exists, the script updates its name or password when needed. If another role already uses `ADMIN_EMAIL`, choose a different admin email.

To create or update the sample instructor and seed sample courses and lessons:

```powershell
node seedData.js
```

This command requires the `INSTRUCTOR_*` variables. If using a new instructor email to migrate an existing demo instructor, set `LEGACY_INSTRUCTOR_EMAIL` to the old account's email. The sample records are seeded without duplicating matching courses or lessons.

## Frontend Setup

The frontend reads its API base URL from `VITE_API_URL` and falls back to `http://localhost:5000/api` when it is not set. Its shared Axios client reads the saved JWT and sends it as a Bearer token when present.

Start the frontend from the `frontend` directory:

```powershell
npm run dev
```

Use the local URL printed by Vite. To build the production bundle, run:

```powershell
npm run build
```

## Run the Project

1. Start MongoDB Atlas and make sure your machine is allowed to connect.
2. In a terminal, run the backend from `backend`:

	```powershell
	npm run dev
	```

3. In a second terminal, run the frontend from `frontend`:

	```powershell
	npm run dev
	```

4. Open the frontend URL printed by Vite. Create an account, or use the admin account created with `npm run create-admin`.

## API Endpoint Summary

The local API base is `http://localhost:5000/api`. Protected endpoints require `Authorization: Bearer <token>` unless otherwise noted.

| Method | Endpoint | Access | Purpose |
|---|---|---|---|
| `GET` | `/` | Public | API health response |
| `POST` | `/api/auth/register` | Public | Register a student or instructor |
| `POST` | `/api/auth/login` | Public | Log in and receive a JWT |
| `GET` | `/api/stats` | Public | Course, instructor, and student counts |
| `GET` | `/api/courses` | Public | List courses |
| `GET` | `/api/courses/:id` | Public | Get course details |
| `POST` | `/api/courses` | Instructor/Admin | Create a course |
| `PUT` | `/api/courses/:id` | Owning instructor/Admin | Update a course |
| `DELETE` | `/api/courses/:id` | Owning instructor/Admin | Delete a course |
| `POST` | `/api/enroll` | Student | Enroll in a course for free |
| `GET` | `/api/my-courses` | Student | List the student's enrollments |
| `PUT` | `/api/enrollments/:id/progress` | Enrollment owner (Student) | Update progress percentage |
| `GET` | `/api/lessons/course/:courseId` | Enrolled Student, owning Instructor, or Admin | List course lessons |
| `POST` | `/api/lessons` | Instructor/Admin | Create a lesson |
| `PUT` | `/api/lessons/:id` | Owning Instructor/Admin | Update a lesson |
| `DELETE` | `/api/lessons/:id` | Owning Instructor/Admin | Delete a lesson |
| `GET` | `/api/admin/analytics` | Admin | Get user, course, and enrollment counts |
| `GET` | `/api/admin/users` | Admin | List users |
| `PUT` | `/api/admin/users/:id/role` | Admin | Change a user's role |
| `DELETE` | `/api/admin/users/:id` | Admin | Delete another user |

Course prices are demonstration values in PKR. The enrollment endpoint does not charge students or use a payment gateway.

## Authentication and Roles

Passwords are hashed with bcrypt. Login and registration return a JWT that expires after seven days. The frontend stores the token in browser `localStorage`, and the Axios request interceptor attaches it to authenticated requests as `Authorization: Bearer <token>`.

The authentication middleware verifies the token and loads the current user. Role middleware restricts endpoints to specified roles. Public registration accepts Student and Instructor roles; it does not create Admin accounts. The admin setup script creates or updates an Admin account.

Lesson reads require authentication. Students must be enrolled in the requested course; instructors may read lessons only for their own courses; Admins may read lessons across courses. Public course endpoints do not return lesson content or video links.

## Deployment Preparation

- Deploy the backend and frontend separately, and configure MongoDB network access for the backend host.
- Set backend environment variables in the backend host's secret/environment settings. Never deploy a real `.env` file.
- Set `VITE_API_URL` in the frontend host's build environment to the deployed backend API base URL, including `/api`. Do not place a production backend URL in React components.
- Vite embeds `VITE_API_URL` at build time. Rebuild and redeploy the frontend after changing it.
- Restrict backend CORS to the deployed frontend origin instead of allowing every origin.
- Use HTTPS, a strong `JWT_SECRET`, unique account passwords, and production-only database credentials.
- Do not expose admin or instructor passwords in source code, documentation, or frontend bundles.

## Testing Checklist

- [ ] Backend connects successfully to MongoDB Atlas.
- [ ] `GET /api/stats` works without a token and returns the current counts.
- [ ] Public visitors can list courses and view course details.
- [ ] Registration and login work for Student and Instructor roles.
- [ ] Students can enroll without payment and see their enrolled courses.
- [ ] An enrolled student can view course lessons; a non-enrolled student and a public visitor cannot.
- [ ] An instructor can manage only their own courses and lessons.
- [ ] An Admin can access admin endpoints and manage users and courses.
- [ ] Invalid or missing JWTs are rejected by protected endpoints.
- [ ] `npm run build` succeeds in the `frontend` directory.

There is currently no automated test script in either package. This checklist describes manual checks.

## Security Notes

- Keep `backend/.env` and `frontend/.env` private; `.env` is ignored by the project gitignore files.
- Use a unique, long random JWT secret and strong, separate account passwords.
- The admin password is required by the admin creation script; there is no password fallback.
- Admin credentials are created through the backend script, not public registration.
- Browser `localStorage` is convenient for this project but can be exposed by cross-site scripting. A production system should consider stronger token storage and additional protections.
- Set a restrictive CORS allow-list and HTTPS before public deployment.
- No payment processing is implemented. Course price values are for demonstration; enrollment is free.

## Future Improvements

- Add automated backend and frontend tests.
- Add email verification and password recovery.
- Track lesson completion instead of letting students set a percentage manually.
- Add pagination and course sorting.
- Add lesson ordering and duration metadata.
- Improve production logging, validation, rate limiting, and CORS configuration.
- Add a payment provider only if the project requirements later change from free enrollment.
