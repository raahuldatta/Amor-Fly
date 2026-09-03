# 🪐 Amor Fly

### Social Learning, Reimagined Through Peer-to-Peer Pods

[![Next.js](https://img.shields.io/badge/Next.js-14-black.svg?style=for-the-badge&logo=nextdotjs&logoColor=white)](https://nextjs.org/)
[![Node.js](https://img.shields.io/badge/Node.js-18%2B-339933.svg?style=for-the-badge&logo=nodedotjs&logoColor=white)](https://nodejs.org/)
[![Supabase](https://img.shields.io/badge/Supabase-Database-3ECF8E.svg?style=for-the-badge&logo=supabase&logoColor=white)](https://supabase.com/)
[![Clerk](https://img.shields.io/badge/Clerk-Auth-6C47FF.svg?style=for-the-badge&logo=clerk&logoColor=white)](https://clerk.com/)
[![Vercel](https://img.shields.io/badge/Vercel-Deployed-000000.svg?style=for-the-badge&logo=vercel&logoColor=white)](https://vercel.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](#-license)

**🔗 Live App:** [amor-fly-platform.vercel.app](https://amor-fly-platform.vercel.app)

**A modern social learning platform that turns shared skills and interests into real, collaborative micro-communities called Pods — powered by real-time messaging, growth tracking, and intelligent peer matching.**

---

## 📌 Table of Contents

- [Overview](#-overview)
- [Core Concept: What Is a Pod?](#-core-concept-what-is-a-pod)
- [System Architecture](#️-system-architecture)
- [Feature Breakdown](#-feature-breakdown)
- [Database Schema](#️-database-schema)
- [Tech Stack](#-tech-stack)
- [Project Directory Structure](#-project-directory-structure)
- [Quickstart Guide](#-quickstart-guide)
  * [1. Prerequisites](#1-prerequisites)
  * [2. Clone & Install](#2-clone--install)
  * [3. Environment Setup](#3-environment-setup)
  * [4. Set Up Supabase](#4-set-up-supabase)
  * [5. Run Locally](#5-run-locally)
- [Development Scripts](#-development-scripts)
- [Deployment on Vercel](#-deployment-on-vercel)
- [Troubleshooting](#️-troubleshooting)
- [Roadmap](#-roadmap)
- [Contributing & License](#-contributing--license)

---

## 🔭 Overview

Traditional learning platforms are built around static courses and one-way content delivery. **Amor Fly** flips the model: instead of *consuming* a course, users *co-create* a learning experience with peers who share the same curiosity.

The platform matches people by **skills, interests, and personality signals**, groups them into small collaborative circles called **Pods**, and layers in real-time chat, connection requests, growth points, and engagement tracking — turning learning into something social, gamified, and sustained.

---

## 🌱 Core Concept: What Is a Pod?

A **Pod** is a small, purpose-built group of learners centered on a shared skill or topic.

```
                     ┌─────────────────────────┐
                     │        New User          │
                     └────────────┬─────────────┘
                                  │
                     Skills, Interests, Personality
                                  │
                                  ▼
                     ┌─────────────────────────┐
                     │   Matching & Discovery    │
                     └────────────┬─────────────┘
                                  │
              ┌───────────────────┼───────────────────┐
              ▼                   ▼                   ▼
        ┌───────────┐       ┌───────────┐       ┌───────────┐
        │   Pod A    │       │   Pod B    │       │   Pod C    │
        │  (Design)  │       │ (Backend)  │       │ (Language) │
        └─────┬─────┘       └─────┬─────┘       └─────┬─────┘
              │                   │                   │
              ▼                   ▼                   ▼
        Real-time Chat     Growth Points        Connections &
        & Attachments      & Engagement          Notifications
```

Each Pod has a creator, a capped member list, a category, and tags — keeping groups focused and small enough for genuine peer learning rather than broadcast-style content feeds.

---

## ⚙️ System Architecture

```
flowchart TD
    A[User Signs Up] --> B[Clerk Authentication]
    B --> C[Supabase User Profile Created]
    C --> D{Onboarding}
    D --> E[Skills / Interests / Personality Answers]
    E --> F[Anonymous Name Assigned]
    F --> G[Pod Discovery & Matching]

    G --> H1[Join Existing Pod]
    G --> H2[Create New Pod]

    H1 --> I[Pod Messaging Layer]
    H2 --> I

    I --> J[Real-time Messages + Attachments + Likes]
    I --> K[Connections: Request / Accept / Decline]
    I --> L[Notifications Engine]

    J --> M[Growth Points + Engagement Level]
    K --> M
    L --> M

    M --> N[Weekly Connection Limits & Activity Tracking]
```

    Loading

---

## 🧩 Feature Breakdown

| Module | Description |
|---|---|
| **🔐 Authentication** | Secure sign-up/sign-in flows via Clerk, backed by JWT-based session handling. |
| **🧠 Personality-Aware Onboarding** | Captures skills, interests, and personality answers to power smarter Pod matching. |
| **👥 Pods** | Small, capped, category-tagged groups built around a shared skill or interest. |
| **💬 Real-Time Messaging** | Pod-scoped chat with attachments and message likes. |
| **🤝 Connections** | Peer-to-peer connection requests with request/accept/decline status tracking. |
| **🔔 Notifications** | Structured, typed notification records with read/unread state. |
| **📈 Growth & Engagement Tracking** | Growth points and engagement levels quantify participation over time. |
| **🕶️ Anonymous Identity Layer** | Optional anonymous display names for lower-pressure early participation. |

---

## 🗄️ Database Schema

Amor Fly's data model runs on **Supabase (PostgreSQL)** with **Row Level Security (RLS)** enforced across every table.

<details>
<summary><strong>📍 users</strong></summary>

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email TEXT UNIQUE,
  name TEXT,
  bio TEXT,
  avatar TEXT,
  skills TEXT[],
  location TEXT,
  interests TEXT[],
  selected_skills TEXT[],
  personality_answers JSONB,
  anonymous_name TEXT,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now(),
  is_active BOOLEAN DEFAULT TRUE,
  is_verified BOOLEAN DEFAULT FALSE,
  role TEXT DEFAULT 'user',
  pod_id UUID,
  growth_points INTEGER DEFAULT 0,
  engagement_level INTEGER DEFAULT 0,
  weekly_connections_used INTEGER DEFAULT 0
);
```
</details>

<details>
<summary><strong>👥 pods</strong></summary>

```sql
CREATE TABLE pods (
  id UUID PRIMARY KEY,
  name TEXT,
  description TEXT,
  creator_id UUID REFERENCES users(id),
  members UUID[],
  max_members INTEGER,
  category TEXT,
  tags TEXT[],
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);
```
</details>

<details>
<summary><strong>💬 messages</strong></summary>

```sql
CREATE TABLE messages (
  id UUID PRIMARY KEY,
  pod_id UUID REFERENCES pods(id),
  sender_id UUID REFERENCES users(id),
  content TEXT,
  type TEXT,
  attachments TEXT[],
  likes UUID[],
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);
```
</details>

<details>
<summary><strong>🤝 connections</strong></summary>

```sql
CREATE TABLE connections (
  id UUID PRIMARY KEY,
  requester_id UUID REFERENCES users(id),
  recipient_id UUID REFERENCES users(id),
  status TEXT,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);
```
</details>

<details>
<summary><strong>🔔 notifications</strong></summary>

```sql
CREATE TABLE notifications (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  type TEXT,
  title TEXT,
  message TEXT,
  data JSONB,
  is_read BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT now()
);
```
</details>

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Frontend / Framework** | Next.js, Node.js 18+ |
| **Authentication** | Clerk |
| **Database & Backend Services** | Supabase (PostgreSQL, RLS, REST) |
| **Hosting / Deployment** | Vercel |
| **Session Security** | JWT |

---

## 📁 Project Directory Structure

```
amor-fly-platform/
├── app/ (or pages/)         # Next.js routes and views
├── components/              # Shared UI components
├── lib/                     # Supabase client, Clerk config, utilities
├── public/                  # Static assets
├── .env.local               # Environment variables (not committed)
├── package.json             # Project dependencies & scripts
└── README.md                # You are here
```

---

## 🚀 Quickstart Guide

### 1. Prerequisites

Make sure you have the following installed and set up:

- **Node.js** 18 or later
- **Supabase** account (database & backend services)
- **Clerk** account (authentication)
- **Vercel** account (deployment)

### 2. Clone & Install

```bash
git clone <your-repo-url>
cd amor-fly-platform
npm install
```

### 3. Environment Setup

Create a `.env.local` file in the root directory:

```env
NEXT_PUBLIC_SUPABASE_URL=https://<your-supabase-project>.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
JWT_SECRET=your-super-secret-jwt-key-at-least-32-characters-long
NODE_ENV=development
```

> 💡 You can find these values in your Supabase project under **Project Settings → API**.

### 4. Set Up Supabase

1. Go to your [Supabase Dashboard](https://app.supabase.com)
2. Create a new project and database
3. Use the SQL Editor to create the schema (`users`, `pods`, `messages`, `connections`, `notifications`)
4. Enable **Row Level Security (RLS)** and write appropriate policies
5. *(Optional)* Seed test data via the SQL Editor or REST API

### 5. Run Locally

```bash
npm run dev
```

Visit **[http://localhost:3000](http://localhost:3000)** to view the app.

---

## 🧪 Development Scripts

| Command | Description |
|---|---|
| `npm run dev` | Start the development server |
| `npm run build` | Build the app for production |
| `npm run start` | Start the production server |
| `npm run lint` | Run ESLint |
| `npm run seed` | *(Optional)* Seed the Supabase DB via API/script |

---

## ☁️ Deployment on Vercel

### 1. Configure Environment Variables

Go to **Vercel → Project Settings → Environment Variables** and add:

| Key | Value |
|---|---|
| `NEXT_PUBLIC_SUPABASE_URL` | Your Supabase project URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Your project's anonymous public API key |
| `SUPABASE_SERVICE_ROLE_KEY` | Service role key *(server-side only)* |
| `JWT_SECRET` | A strong random string, e.g. via `openssl rand -base64 32` |

### 2. Deploy via Vercel CLI

```bash
npm install -g vercel
vercel login
vercel --prod
```

Visit the generated URL to access your live app — or head straight to the current deployment: **[amor-fly-platform.vercel.app](https://amor-fly-platform.vercel.app)**

---

## 🩺 Troubleshooting

<details>
<summary><strong>🔐 Auth issues (Clerk)</strong></summary>

- Ensure Clerk frontend and backend integrations are configured properly
- Make sure `JWT_SECRET` matches across environments
</details>

<details>
<summary><strong>🧪 Supabase errors</strong></summary>

- Confirm correct `anon` and `service_role` keys
- Use Supabase logs and the SQL editor to debug
- Review RLS policies if data isn't returning
</details>

<details>
<summary><strong>⚙️ Build / deploy issues</strong></summary>

- Check Vercel logs for runtime errors
- Ensure all env variables are properly set in both local and production environments
</details>

---

## 🗺️ Roadmap

- [ ] AI-assisted Pod matching based on personality and skill vectors
- [ ] In-Pod video/voice sessions
- [ ] Public Pod leaderboards for growth points
- [ ] Mobile app companion

---

## ✨ Why Amor Fly?

- 🌱 Grow through collaborative learning Pods
- 🔐 Clerk + Supabase = secure, scalable auth + database
- 💬 Real-time messaging and engagement features
- 🚀 Fully deployable with a single command

---

## 🤝 Contributing & License

Contributions, issues, and feature requests are welcome — feel free to open a PR or file an issue.

This project is licensed under the **MIT License**.
