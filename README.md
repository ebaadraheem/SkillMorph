# 🎓 SkillMorph

SkillMorph is a full-stack online learning platform where instructors can create and sell video courses, and students can browse, enroll in, and track progress on courses — with payments handled through **Stripe Connect** and an integrated **AI course assistant** powered by LangGraph.

This repository contains both halves of the project:

```
SkillMorph/
├── frontend/   → React + Vite + TypeScript client
└── backend/    → Express + PostgreSQL REST API
```

## Features

- 🔐 **Authentication** — JWT-based sign up / login with access & refresh tokens, plus email-based password reset
- 🧑‍🏫 **Instructor Tools** — Create, update, and delete courses; upload and manage course videos
- 🎬 **Video Courses** — Cloudinary-backed video & thumbnail uploads with streaming playback
- 🛒 **Enrollment** — Students can browse, search, and enroll in courses
- 💳 **Payments & Payouts** — Stripe Checkout for course purchases and Stripe Connect for instructor payouts, synced via webhooks
- 🤖 **AI Course Assistant** — A LangGraph agent (Gemini 2.5 Flash) that answers questions about the course catalog using a live database tool
- 📊 **Dashboards** — Unified dashboard for browsing courses, viewing enrollments, managing created courses, and tracking payments

## Tech Stack

| Layer        | Technology |
|--------------|------------|
| Frontend     | React, TypeScript, Vite, Tailwind CSS, Zustand, React Router |
| Backend API  | Node.js, Express |
| Database     | PostgreSQL |
| AI Agent     | LangChain, LangGraph, Google Gemini |
| Auth         | JWT (access + refresh tokens) |
| Payments     | Stripe (Checkout + Connect) |
| Media Storage| Cloudinary |
| Deployment   | Vercel (frontend) |

## Project Structure

```
SkillMorph/
├── frontend/
│   ├── src/
│   │   ├── Pages/              # Landing, dashboard, auth, legal pages
│   │   ├── components/         # Courses, video player, chatbot, dashboards
│   │   ├── Custom/              # Shared UI building blocks
│   │   └── store/               # Zustand stores + API call helpers
│   └── vite.config.ts
│
└── backend/
    ├── server.js                # Express app entry point
    ├── routes/                  # Auth, Courses, Videos, Payment, Chat, Webhooks, User
    ├── agent/                   # LangGraph AI agent + database tool
    ├── lib/                     # PostgreSQL client, Cloudinary config
    └── middleware/               # JWT auth, CORS, message formatting
```

## Getting Started

The frontend and backend are run and configured **independently**. See the dedicated README in each folder for full setup instructions:

- 💻 [`frontend/README.md`](./frontend/README.md) — running the React client
- 🖥️ [`backend/README.md`](./backend/README.md) — running the API server

### Quick Start

1. **Start the backend first** (the app needs a live API):
   ```bash
   cd backend
   npm install
   npm run dev
   ```

2. **Then start the frontend**, pointing it at your backend's URL:
   ```bash
   cd frontend
   npm install
   npm run dev
   ```

## Prerequisites

- Node.js v18+ and npm
- A PostgreSQL database
- A Cloudinary account (for video/image storage)
- A Stripe account with Connect enabled (for payments & payouts)
- A Google AI Studio API key (for the Gemini-powered chat agent)
- An email account for sending password-reset emails (e.g. Gmail with an app password)

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/my-feature`)
3. Commit your changes
4. Push to the branch and open a Pull Request

## License

This project is licensed under the MIT License.
