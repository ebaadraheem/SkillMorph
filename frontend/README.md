# 💻 SkillMorph — Frontend

The web client for SkillMorph, built with **React**, **TypeScript**, and **Vite**. It connects to the [SkillMorph backend](../backend/README.md) for authentication, course data, payments, and the AI course assistant.

## Features

- **Landing Page & Public Pages** — About, Privacy Policy, Terms of Service
- **Authentication** — Sign up, log in, forgot/reset password flows
- **Skills Dashboard** — Single dashboard with views for browsing all courses, your enrolled courses, courses you've created (instructors), and payments/payouts
- **Course Creation & Management** — Instructors can create courses, upload videos, and edit course details
- **Video Playback** — In-app course video player
- **Stripe Checkout** — Enroll in and pay for courses directly from the dashboard
- **AI Chatbot** — Floating assistant that answers questions about available courses

## Tech Stack

- **React 18** + **TypeScript**
- **Vite** for dev server and builds
- **React Router** for client-side routing
- **Zustand** for state management
- **Tailwind CSS** for styling
- **Framer Motion** for animations
- **Axios** for HTTP requests
- **Stripe.js** for checkout
- **React Markdown** for rendering chatbot responses
- **React Hot Toast** for notifications
- **Lucide React** for icons

## Project Structure

```
frontend/
├── src/
│   ├── main.tsx                 # App entry point
│   ├── App.tsx                  # Route definitions
│   ├── Pages/
│   │   ├── LandingPage.tsx        # Public landing page
│   │   ├── Home.tsx
│   │   ├── LogIn.tsx / SignUp.tsx
│   │   ├── forgot_pass.tsx / reset_password.tsx
│   │   ├── SkillsDashboard.tsx    # Main authenticated dashboard
│   │   ├── About.tsx
│   │   ├── PrivacyPolicy.tsx
│   │   └── TermsOfService.tsx
│   ├── components/
│   │   ├── Courses.tsx             # Browse/search all courses
│   │   ├── Enrolled.tsx            # Student's enrolled courses
│   │   ├── Created.tsx             # Instructor's created courses
│   │   ├── CreaterDashboard.tsx    # Instructor payments/payouts view
│   │   ├── CourseForm.tsx          # Create/edit course form
│   │   ├── VideoForm.tsx           # Add/edit course video
│   │   ├── CoursePlay.tsx          # Course video player
│   │   ├── Chatbot.tsx             # AI course assistant widget
│   │   ├── Navbar.tsx / Footer.tsx
│   ├── Custom/                     # Shared UI helpers (scroll-to-top, cards, etc.)
│   └── store/
│       ├── AuthStore.tsx           # Auth state (user, tokens)
│       ├── CourseStore.tsx         # Course state
│       ├── InstructorStore.tsx     # Instructor-specific state
│       └── ApiCalls.tsx            # Shared fetch helpers
├── index.html
├── vite.config.ts
└── vercel.json                     # SPA rewrite rule for Vercel
```

## Prerequisites

- Node.js v18 or later
- npm
- The [SkillMorph backend](../backend/README.md) running locally or deployed
- A Stripe **publishable** key (for Checkout on the client)

## Installation

1. Clone the repository and move into the frontend folder:
   ```bash
   git clone https://github.com/ebaadraheem/SkillMorph.git
   cd SkillMorph/frontend
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

## Configuration

Create a `.env` file in the `frontend/` root directory:

```env
VITE_SERVER_URL=http://localhost:4000
VITE_STRIPE_PUBLIC_KEY=your-stripe-publishable-key
```

### Environment Variables Explained

| Variable | Description |
|---|---|
| `VITE_SERVER_URL` | Base URL of the running SkillMorph backend API |
| `VITE_STRIPE_PUBLIC_KEY` | Your Stripe **publishable** key (safe for client-side use), used to initialize Stripe.js for checkout |

> ⚠️ Never put your Stripe **secret** key here — only the publishable key belongs on the frontend. The secret key stays in the backend's `.env`.

## Running the App

Start the Vite dev server:

```bash
npm run dev
```

By default this runs at `http://localhost:5173`.

Other available scripts:

```bash
npm run build     # Type-check and build for production
npm run preview   # Preview the production build locally
npm run lint       # Run ESLint
```

## Deployment

This project includes a `vercel.json` with a catch-all rewrite rule so client-side routing works correctly on **Vercel**. To deploy:

1. Push the repo to GitHub.
2. Import the project into [Vercel](https://vercel.com), setting the **root directory** to `frontend`.
3. Add `VITE_SERVER_URL` and `VITE_STRIPE_PUBLIC_KEY` as environment variables in the Vercel project settings.
4. Deploy — Vercel will run `npm run build` automatically.

## Notes

- The backend repository/folder is referenced as `SkillMorph_Server` — see [`../backend/README.md`](../backend/README.md) for setup.
- The package name in `package.json` is `eduaid`, an earlier working name for the project — functionally this is the SkillMorph frontend.
