# MERN Online Course Platform (with Gemini AI Suggestions)

A full‑stack online learning platform built with **MongoDB, Express, React, Node.js** and integrated with **Google Gemini API** to power AI‑driven recommendations, search assist, and content insights.

<p align="center">
  <img src="https://img.shields.io/badge/MERN-Fullstack-43B02A" />
  <img src="https://img.shields.io/badge/Gemini-API-blue" />
  <img src="https://img.shields.io/badge/Status-Active-success" />
  <img src="https://img.shields.io/badge/License-MIT-lightgrey" />
</p>

## 📸 Demo & Screens

* **Live Demo**: *Add link here*
* **Backend Docs (Swagger/Redoc)**: *Add link here*
* **Screenshots**: */screenshots/home.png*, */screenshots/course.png*, */screenshots/ai.png*

---

## ✨ Key Features

**Learners**

* Browse/search/filter courses by category, level, price, rating
* Secure auth (JWT) + social login (optional)
* Video lessons with progress tracking & resume‑where‑you‑left
* Quizzes/assignments, downloadable resources
* Wishlist, cart, coupons, payments (Stripe/Razorpay ready)
* Ratings & reviews, Q\&A per lesson

**Instructors**

* Create courses, sections, lessons (video, PDF, text)
* Draft → submit for review → publish workflow
* Earnings dashboard & payouts

**Admins**

* User/course moderation, category/tags management
* Platform analytics, featured courses, coupons

**AI (Gemini) 🔮**

* Smart course recommendations based on goals/history
* Conversational search assist ("Find me a Python course for data analysis in Hindi")
* Auto‑generated course outlines/titles & lesson descriptions (drafts)
* Q\&A helper on lesson pages with safe‑guarded context windows

---

## 🧠 Tech Stack

* **Frontend**: React + Vite, React Router, Context/Redux Toolkit, TailwindCSS
* **Backend**: Node.js, Express.js, Mongoose, Zod/Joi validation
* **DB**: MongoDB (Atlas or self‑hosted)
* **Auth**: JWT (access + refresh), bcrypt, cookies/HTTP‑only
* **AI**: Google Gemini API (generative + embeddings optional)
* **Storage (optional)**: Cloudinary / AWS S3 for videos & assets
* **Payments (optional)**: Stripe or Razorpay
* **Docs/Tooling**: ESLint/Prettier, Jest + Supertest, Swagger, Docker (optional)

---

## 🗺️ Architecture (High Level)

```
[ React (Vite) ]  ──►  [ Express API ]  ──►  [ MongoDB ]
       │                    │                    │
       │   Gemini client    │  Gemini server SDK │
       └────────►  Google Gemini API ◄───────────┘
```

* **Client‑side** uses a lightweight Gemini client for search‑assist UI.
* **Server‑side** mediates all sensitive calls to Gemini with API key protection, rate‑limits, safety checks, and prompt templates.

---

## 📂 Folder Structure (suggested)

```
root
├── client/               # React app (Vite)
│   ├── src/
│   │   ├── app/
│   │   ├── components/
│   │   ├── features/     # RTK slices or contexts
│   │   ├── pages/
│   │   ├── hooks/
│   │   └── services/     # API clients
│   └── index.html
├── server/               # Node/Express
│   ├── src/
│   │   ├── config/
│   │   ├── middlewares/
│   │   ├── models/
│   │   ├── routes/
│   │   ├── controllers/
│   │   ├── services/     # Gemini, payments, storage
│   │   ├── utils/
│   │   └── tests/
│   └── swagger.yaml
├── .env.example
├── docker-compose.yml    # optional
└── README.md
```

---

## 🔧 Setup & Installation

### 1) Prerequisites

* Node.js ≥ 18, npm or pnpm
* MongoDB Atlas URI
* **Gemini API Key** (from Google AI Studio)

### 2) Clone & Install

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>

# client
cd client && npm i && cd ..

# server
cd server && npm i && cd ..
```

### 3) Environment Variables

Create **.env** files in both `client` and `server` (copy from `.env.example`).

**/server/.env**

```
PORT=5000
MONGO_URI=your_mongo_uri
JWT_ACCESS_SECRET=supersecret_access
JWT_REFRESH_SECRET=supersecret_refresh
GEMINI_API_KEY=your_gemini_api_key
CORS_ORIGIN=http://localhost:5173
CLOUDINARY_CLOUD_NAME=optional
CLOUDINARY_API_KEY=optional
CLOUDINARY_API_SECRET=optional
STRIPE_SECRET_KEY=optional
RAZORPAY_KEY_ID=optional
RAZORPAY_KEY_SECRET=optional
```

**/client/.env**

```
VITE_API_BASE=http://localhost:5000/api
```

### 4) Run Dev Servers

```bash
# in one terminal
cd server && npm run dev

# in another terminal
cd client && npm run dev
```

* Client: [http://localhost:5173](http://localhost:5173)
* Server: [http://localhost:5000](http://localhost:5000)

---

## 🤖 Gemini Integration

### Server‑side example (TypeScript/JS)

```ts
// server/src/services/gemini.ts
import { GoogleGenerativeAI } from "@google/generative-ai";

const genAI = new GoogleGenerativeAI(process.env.GEMINI_API_KEY!);
const model = genAI.getGenerativeModel({ model: "gemini-1.5-flash" });

export async function suggestCourses(prompt: string) {
  const system = `You are a course recommender for an e-learning site. Respond as JSON array of courses.`;
  const res = await model.generateContent([{ text: system }, { text: prompt }]);
  const text = res.response.text();
  return JSON.parse(text);
}
```

### Client‑side hook (search assist)

```ts
// client/src/services/ai.ts
export async function aiSuggest(query: string) {
  const r = await fetch(`${import.meta.env.VITE_API_BASE}/ai/suggest`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ query })
  });
  return r.json();
}
```

> **Security Tip:** Call Gemini from the server, not the browser. Never expose API keys in client code.

---

## 🛡️ Security & Best Practices

* HTTP‑only cookies for refresh tokens; short‑lived access tokens
* CORS locked to known origins
* Input validation (Zod/Joi) + centralized error handler
* Rate‑limit + IP throttling on `/auth`, `/ai` routes
* Signed URLs for media; restrict upload types; antivirus scan (optional)
* Prompt templates + safety settings for Gemini

---

## 📈 Performance & Scalability

* CDN for static assets, lazy‑load routes/components
* MongoDB indexes on `title`, `category`, `instructor`, `slug`, `createdAt`
* Caching hot endpoints (e.g., Redis) and HTTP caching headers
* Chunked uploads for large videos; background processing webhooks
* Pagination & server‑side filtering for course catalogs

---

## 🧪 Testing

```bash
# server
npm run test        # unit + integration (Jest + Supertest)

# client
npm run test        # vitest/react-testing-library
```

---

## 🚀 Deployment

* **Frontend**: Vercel/Netlify (build: `npm run build` in `client`)
* **Backend**: Render/Railway/Heroku/Fly.io (Node 18+)
* **Env**: Add `GEMINI_API_KEY`, DB URI, JWT secrets in provider dashboard
* **Domain**: Point custom domain to client host; set `CORS_ORIGIN`
* **Media**: Cloudinary/AWS S3 bucket with proper CORS & signed uploads



## 📚 API Overview (sample)

```
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/refresh
GET    /api/courses?search=&category=&sort=&page=
GET    /api/courses/:slug
POST   /api/courses            (instructor)
PATCH  /api/courses/:id        (instructor/admin)
POST   /api/ai/suggest         (Gemini‑powered)
POST   /api/ai/outline         (Gemini‑powered)
POST   /api/pay/checkout       (Stripe/Razorpay)
```

---

## 🧭 Roadmap

* [ ] Lesson transcripts with AI summaries & highlights
* [ ] Multilingual search and course subtitles
* [ ] Instructor co‑pilot for quiz generation
* [ ] Collaborative notes & flashcards
* [ ] Mobile app (React Native)

---

## 🤝 Contributing

1. Fork the repo & create a feature branch: `feat/<name>`
2. Run tests & lints before PR
3. Fill PR template with screenshots/loom for UX changes

---

## 📄 License

Released under the **MIT License**. See `LICENSE` for details.

---

## 🙌 Acknowledgements

* Google **Gemini API**
* MongoDB Atlas, Vercel, Cloudinary
* React community & awesome OSS packages

---

### ⭐ Tips

If this repo helps you, star it! It keeps the project discoverable and motivates future updates.
