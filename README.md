# SWYNEX Project Architecture: Expense Tracker

**Task 1 – SWYNEX Technologies Internship**

A small full-stack web application that helps people record their daily expenses, organise them by category, and see how much they spend over time.

---

## 1. Product Idea

**Problem:** Many people (especially students and young professionals) lose track of where their money goes each month.

**Solution:** A simple web app where a user can register, log in, add expenses, and view a dashboard with their expense list and monthly total.

**Target users:** Students, interns, and anyone who wants a basic way to track spending.

**Core features (MVP):**
- User registration and login
- Add, edit, and delete expenses
- Categorise expenses (Food, Transport, Bills, Shopping, Other)
- View all expenses with a monthly total
- Filter expenses by category and month

**Out of scope (future ideas):** Charts, budgets and alerts, CSV export, multi-currency support.

---

## 2. Tech Stack

| Layer | Technology |
|-------|------------|
| Frontend | React (Vite) + CSS |
| Backend | Node.js + Express |
| Database | MongoDB (or PostgreSQL) |
| Authentication | JWT (JSON Web Tokens) + bcrypt for password hashing |
| Hosting (suggested) | Vercel (frontend), Render (backend), MongoDB Atlas (database) |

---

## 3. System Architecture

```
 [ Browser / React App ]
          |
          |  HTTP requests (JSON) + JWT token
          v
 [ Express REST API ]
   - Auth middleware (verifies JWT)
   - Controllers (business logic)
          |
          v
 [ Database: Users, Expenses ]
```

**Flow:** The user interacts with the React screens. React sends requests to the Express API. The API checks the JWT token, reads or writes the database, and returns JSON. React then updates the screen.

---

## 4. Data Models

### User
| Field | Type | Notes |
|-------|------|-------|
| id | ObjectId / UUID | Primary key |
| name | String | Required |
| email | String | Required, unique |
| password | String | Hashed with bcrypt, never stored as plain text |
| createdAt | Date | Auto-generated |

### Expense
| Field | Type | Notes |
|-------|------|-------|
| id | ObjectId / UUID | Primary key |
| userId | Reference to User | Owner of the expense |
| title | String | Required (e.g. "Lunch") |
| amount | Number | Required, greater than 0 |
| category | String | Food, Transport, Bills, Shopping, Other |
| date | Date | Date of the expense |
| note | String | Optional |
| createdAt | Date | Auto-generated |

**Relationship:** One User has many Expenses (one-to-many).

---

## 5. API Routes

Base URL: `/api`

### Authentication
| Method | Route | Description | Auth required |
|--------|-------|-------------|---------------|
| POST | `/api/auth/register` | Create a new user account | No |
| POST | `/api/auth/login` | Log in and receive a JWT token | No |
| GET | `/api/auth/me` | Get the logged-in user's profile | Yes |

### Expenses
| Method | Route | Description | Auth required |
|--------|-------|-------------|---------------|
| GET | `/api/expenses` | List the user's expenses (supports `?category=` and `?month=`) | Yes |
| GET | `/api/expenses/:id` | Get a single expense | Yes |
| POST | `/api/expenses` | Add a new expense | Yes |
| PUT | `/api/expenses/:id` | Update an expense | Yes |
| DELETE | `/api/expenses/:id` | Delete an expense | Yes |
| GET | `/api/expenses/summary` | Total spent per category for a month | Yes |

### Sample request: add expense
```json
POST /api/expenses
{
  "title": "Lunch",
  "amount": 450,
  "category": "Food",
  "date": "2026-10-25"
}
```

### Sample response
```json
{
  "id": "64f1c2...",
  "title": "Lunch",
  "amount": 450,
  "category": "Food",
  "date": "2026-10-25"
}
```

---

## 6. Frontend Screens

1. **Register Page**: name, email, password form. On success, redirects to Login.
2. **Login Page**: email and password form. On success, stores the token and opens the Dashboard.
3. **Dashboard**: shows the monthly total, a list of expenses, category and month filters, and an "Add Expense" button.
4. **Add / Edit Expense Page**: form with title, amount, category, date, and note. Used for both creating and editing.
5. **Navbar (on all logged-in pages)**: app name, Dashboard link, Logout button.

### Screen flow
```
Register --> Login --> Dashboard <--> Add/Edit Expense
                          |
                       Logout --> Login
```

---

## 7. Folder Structure (Starter Repository)

```
SWYNEX-Project-Architecture/
├── README.md
├── frontend/
│   ├── src/
│   │   ├── pages/        (Login, Register, Dashboard, ExpenseForm)
│   │   ├── components/   (Navbar, ExpenseCard, FilterBar)
│   │   ├── services/     (api.js for API calls)
│   │   └── App.jsx
│   └── package.json
└── backend/
    ├── src/
    │   ├── models/       (User.js, Expense.js)
    │   ├── routes/       (auth.js, expenses.js)
    │   ├── controllers/
    │   ├── middleware/   (authMiddleware.js)
    │   └── server.js
    └── package.json
```

---

## 8. Security Considerations

- Passwords are hashed with bcrypt before saving.
- Protected routes require a valid JWT in the `Authorization: Bearer <token>` header.
- Each user can only access their own expenses (the API checks `userId`).
- Input validation on all create and update requests.
- Secrets (JWT secret, database URL) are stored in a `.env` file and never committed to GitHub.

---

## 9. Development Plan

| Phase | Work |
|-------|------|
| 1 | Set up repo, folder structure, and database connection |
| 2 | Build authentication (register, login, JWT middleware) |
| 3 | Build expense CRUD APIs |
| 4 | Build React screens and connect to the API |
| 5 | Add filters, monthly summary, and testing |
| 6 | Deploy and document |

---

## 10. Key Learnings

- Planning data models and API routes before coding avoids rework.
- A clear folder structure keeps frontend and backend responsibilities separate.
- Thinking about security (hashing, tokens, ownership checks) from the start is easier than adding it later.

---

*Prepared as part of Task 1 – Project Architecture, SWYNEX Technologies Internship.*
