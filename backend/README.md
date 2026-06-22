# 🖥️ SkillMorph — Backend

The REST API server for SkillMorph, built with **Node.js**, **Express**, and **PostgreSQL**. It handles authentication, course and video management, Stripe payments/payouts, and powers an AI course assistant built with **LangGraph** and **Google Gemini**.

## Features

- 🔐 **Authentication** — JWT access/refresh tokens, bcrypt password hashing, email-based password reset (Nodemailer)
- 🧑‍🏫 **Course Management** — Create, update, delete, search, and paginate courses
- 🎬 **Video Management** — Upload, update, and delete course videos via Cloudinary
- 🛒 **Enrollment** — Track which students are enrolled in which courses
- 💳 **Payments** — Stripe Checkout for course purchases, Stripe Connect for instructor onboarding & payouts
- 🔁 **Webhooks** — Stripe webhooks keep enrollment and instructor account status in sync
- 🤖 **AI Course Assistant** — A LangGraph state-machine agent (Gemini 2.5 Flash) that queries the course database to answer student questions

## Tech Stack

- **Node.js** (ES Modules) + **Express**
- **PostgreSQL** (`pg`)
- **LangChain** + **LangGraph** + **Google Gemini** (`@langchain/google-genai`) for the AI agent
- **JWT** (`jsonwebtoken`) for auth
- **bcryptjs** for password hashing
- **Cloudinary** (`multer-storage-cloudinary`) for video/image storage
- **Stripe** for payments and Connect payouts
- **Nodemailer** for transactional emails

## Project Structure

```
backend/
├── server.js                      # App entry point, middleware, route mounting
├── routes/
│   ├── Auth.js                      # Register, login, refresh, forgot/reset password, logout
│   ├── Courses.js                   # Course CRUD, search, pagination, enrollment listing
│   ├── Videos.js                    # Add/update/delete course videos
│   ├── Payment.js                   # Stripe Connect onboarding, checkout, balances, payouts
│   ├── Webhooks.js                  # Stripe webhook handlers (Connect + payment)
│   ├── Chat.js                      # AI chat agent endpoint
│   └── User.js                      # Authenticated user info
├── agent/
│   ├── LangGraphAgent.js             # LangGraph state machine + Gemini model + system prompt
│   └── tools.js                      # course_db_tool: queries the courses table
├── lib/
│   ├── db.js                         # PostgreSQL client
│   └── cloudinary.js                 # Cloudinary upload config
└── middleware/
    ├── Authenticate.js                # JWT verification middleware
    ├── Cors.js                        # CORS configuration
    └── Functions.js                   # Message format helpers for the AI agent
```

## Prerequisites

- Node.js v18 or later
- npm
- A PostgreSQL database (with `users`, `courses`, `student_enrollments`, and `instructor_details` tables)
- A Cloudinary account
- A Stripe account with **Connect** enabled
- A Google AI Studio API key (Gemini)
- An email account for sending password-reset links (e.g. Gmail with an [app password](https://support.google.com/accounts/answer/185833))

## Installation

1. Clone the repository and move into the backend folder:
   ```bash
   git clone https://github.com/ebaadraheem/SkillMorph.git
   cd SkillMorph/backend
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

## Configuration

Create a `.env` file in the `backend/` root directory:

```env
# Server
PORT=4000
FRONTEND_URL=http://localhost:5173

# PostgreSQL Database
DB_USER=your-postgresql-username
DB_PASS=your-postgresql-password
DB_HOST=your-postgresql-host
DB_PORT=your-postgresql-port
DB_NAME=your-postgresql-database-name

# JWT
JWT_SECRET=your-jwt-secret
REFRESH_TOKEN_SECRET=your-refresh-token-secret

# Cloudinary
CLOUDINARY_CLOUD_NAME=your-cloudinary-cloud-name
CLOUDINARY_API_KEY=your-cloudinary-api-key
CLOUDINARY_API_SECRET=your-cloudinary-api-secret

# Stripe
STRIPE_SECRET_KEY=your-stripe-secret-key
STRIPE_CONNECT_WEBHOOK_SECRET=your-stripe-connect-webhook-secret
STRIPE_PAYMENT_WEBHOOK_SECRET=your-stripe-payment-webhook-secret

# Email (password reset)
EMAIL=your-email-address
EMAIL_PASS=your-email-app-password

# AI Agent
GEMINI_API_KEY=your-google-gemini-api-key
```

### Environment Variables Explained

| Variable | Description |
|---|---|
| `PORT` | Port the Express server listens on (defaults to `4000`) |
| `FRONTEND_URL` | URL of the deployed/local frontend — used for CORS and password-reset links |
| `DB_USER` / `DB_PASS` / `DB_HOST` / `DB_PORT` / `DB_NAME` | PostgreSQL connection credentials |
| `JWT_SECRET` | Secret used to sign short-lived access tokens |
| `REFRESH_TOKEN_SECRET` | Secret used to sign long-lived refresh tokens |
| `CLOUDINARY_CLOUD_NAME` / `CLOUDINARY_API_KEY` / `CLOUDINARY_API_SECRET` | Cloudinary credentials for video/thumbnail uploads |
| `STRIPE_SECRET_KEY` | Stripe secret key for creating checkout sessions and Connect accounts |
| `STRIPE_CONNECT_WEBHOOK_SECRET` | Signing secret for the Stripe Connect webhook endpoint |
| `STRIPE_PAYMENT_WEBHOOK_SECRET` | Signing secret for the Stripe payment webhook endpoint |
| `EMAIL` / `EMAIL_PASS` | Credentials used by Nodemailer to send password-reset emails |
| `GEMINI_API_KEY` | Google AI Studio API key for the Gemini model powering the chat agent |

> ⚠️ Never commit your `.env` file. Add it to `.gitignore` if it isn't already.

## Running the Server

**Development** (auto-restarts on file changes via Nodemon):
```bash
npm run dev
```

The server starts on `http://localhost:4000` (or your configured `PORT`).

## API Endpoints

### Auth — `/auth`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/auth/register` | Create a new user account |
| POST | `/auth/login` | Log in and receive an access token (+ refresh token cookie) |
| POST | `/auth/refresh` | Exchange a valid refresh token for a new access token |
| POST | `/auth/forgot-password` | Send a password-reset email |
| POST | `/auth/reset-password` | Reset password using a valid reset token |
| POST | `/auth/logout` | Clear the refresh token cookie |

### Courses — `/course`

| Method | Endpoint | Description |
|---|---|---|
| GET | `/course/all-courses` | Paginated, searchable list of all courses |
| GET | `/course/coursedata/:id` | Get details for a single course |
| GET | `/course/enrolled-courses/:id` | Get courses a student is enrolled in |
| DELETE | `/course/disenroll/:student_id/:course_id` | Remove a student's enrollment |
| GET | `/course/instructor-courses/:id` | Get courses created by an instructor |
| POST | `/course/create-course` | Create a new course (with thumbnail upload) |
| POST | `/course/update-course` | Update an existing course |
| DELETE | `/course/delete-course/:courseid/:instructor_id` | Delete a course |

### Videos — `/videos`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/videos/add-video` | Upload and attach a video to a course |
| PUT | `/videos/update-video/:id` | Replace an existing course video |
| DELETE | `/videos/deletevideo/:id` | Delete a course video |

### Payments — `/payment`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/payment/create-or-fetch-connect-account` | Create or fetch an instructor's Stripe Connect account |
| POST | `/payment/process-payment` | Create a Stripe Checkout session for a course |
| GET | `/payment/balance/:accountId` | Get an instructor's Stripe balance |
| GET | `/payment/payouts/:accountId` | List an instructor's payouts |
| GET | `/payment/payments/:accountId` | List payments received by an instructor |
| POST | `/payment/payout` | Trigger a payout to an instructor |

### Webhooks — `/webhook`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/webhook/connect-webhook` | Stripe Connect account status updates |
| POST | `/webhook/payment-webhook` | Confirms checkout completion and creates enrollments |

### User — `/user`

| Method | Endpoint | Description |
|---|---|---|
| GET | `/user/info` | Get the authenticated user's profile (requires JWT) |

### Chat — `/chat`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/chat/query` | Send a message to the AI course assistant; returns its response |

## AI Course Assistant

The `/chat/query` endpoint runs a **LangGraph** agent (`backend/agent/LangGraphAgent.js`) backed by **Gemini 2.5 Flash**. The agent is restricted to answering questions about the SkillMorph course catalog and uses a dedicated `course_db_tool` to query the database directly for:

- Course counts by category
- Course search by title/keyword
- Filtering by maximum price
- Listing courses within a category

This keeps responses grounded in real data rather than the model guessing course details.

## Webhooks Setup

Stripe webhooks require a publicly reachable URL. For local development, use the [Stripe CLI](https://stripe.com/docs/stripe-cli) to forward events:

```bash
stripe listen --forward-to localhost:4000/webhook/payment-webhook
stripe listen --forward-to localhost:4000/webhook/connect-webhook
```

Use the signing secrets the CLI prints for `STRIPE_PAYMENT_WEBHOOK_SECRET` and `STRIPE_CONNECT_WEBHOOK_SECRET` in your `.env`.
