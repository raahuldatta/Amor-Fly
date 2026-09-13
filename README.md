<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=180&section=header&text=Amor%20Fly&fontSize=52&fontColor=ffffff&animation=fadeIn&fontAlignY=38&desc=Social%20Learning%2C%20Reimagined%20Through%20Peer-to-Peer%20Pods&descAlignY=58&descSize=15" width="100%"/>

<h1 align="center">🪐 Amor Fly</h1>
<p align="center"><i>Social Learning, Reimagined Through Peer-to-Peer Pods</i></p>

<img src="https://img.shields.io/badge/Next.js-14-8B5CF6.svg?style=for-the-badge&logo=nextdotjs&logoColor=white&labelColor=1a1a2e"/>
<img src="https://img.shields.io/badge/Node.js-18%2B-7C3AED.svg?style=for-the-badge&logo=nodedotjs&logoColor=white&labelColor=1a1a2e"/>
<img src="https://img.shields.io/badge/Supabase-Database-6D28D9.svg?style=for-the-badge&logo=supabase&logoColor=white&labelColor=1a1a2e"/>
<img src="https://img.shields.io/badge/Clerk-Auth-4C1D95.svg?style=for-the-badge&logo=clerk&logoColor=white&labelColor=1a1a2e"/>
<img src="https://img.shields.io/badge/Vercel-Deployed-A78BFA.svg?style=for-the-badge&logo=vercel&logoColor=white&labelColor=1a1a2e"/>
<img src="https://img.shields.io/badge/License-MIT-8B5CF6.svg?style=for-the-badge&labelColor=1a1a2e"/>

<br/><br/>

**🔗 Live App:** [amor-fly-platform.vercel.app](https://amor-fly-platform.vercel.app)

<p align="center">
<b>A modern social learning platform that turns shared skills and interests into real, collaborative micro-communities called Pods — powered by real-time messaging, growth tracking, and intelligent peer matching.</b>
</p>

</div>

<br/>

---

## <img src="https://img.shields.io/badge/-Overview-8B5CF6?style=flat-square"/>

Traditional learning platforms are built around static courses and one-way content delivery. **Amor Fly** flips the model: instead of *consuming* a course, users *co-create* a learning experience with peers who share the same curiosity.

The platform matches people by **skills, interests, and personality signals**, groups them into small collaborative circles called **Pods**, and layers in real-time chat, connection requests, growth points, and engagement tracking — turning learning into something social, gamified, and sustained.

<br/>

---

## <img src="https://img.shields.io/badge/-Core%20Concept%3A%20What%20Is%20a%20Pod%3F-8B5CF6?style=flat-square"/>

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

<br/>

---

## <img src="https://img.shields.io/badge/-System%20Architecture-8B5CF6?style=flat-square"/>

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

<br/>

---

## <img src="https://img.shields.io/badge/-Feature%20Breakdown-8B5CF6?style=flat-square"/>

<div align="center">

| Module | Description |
|:--|:--|
| **🔐 Authentication** | Secure sign-up/sign-in flows via Clerk, backed by JWT-based session handling. |
| **🧠 Personality-Aware Onboarding** | Captures skills, interests, and personality answers to power smarter Pod matching. |
| **👥 Pods** | Small, capped, category-tagged groups built around a shared skill or interest. |
| **💬 Real-Time Messaging** | Pod-scoped chat with attachments and message likes. |
| **🤝 Connections** | Peer-to-peer connection requests with request/accept/decline status tracking. |
| **🔔 Notifications** | Structured, typed notification records with read/unread state. |
| **📈 Growth & Engagement Tracking** | Growth points and engagement levels quantify participation over time. |
| **🕶️ Anonymous Identity Layer** | Optional anonymous display names for lower-pressure early participation. |

</div>

<br/>

---

## <img src="https://img.shields.io/badge/-Database%20Schema-8B5CF6?style=flat-square"/>

Amor Fly's data model runs on **Supabase (PostgreSQL)** with **Row Level Security (RLS)** enforced across every table.

<details>
<summary><b>📍 users</b></summary>
<br/>

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
<summary><b>👥 pods</b></summary>
<br/>

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
<summary><b>💬 messages</b></summary>
<br/>

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
<summary><b>🤝 connections</b></summary>
<br/>

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
<summary><b>🔔 notifications</b></summary>
<br/>

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

<br/>

---

## <img src="https://img.shields.io/badge/-Tech%20Stack-8B5CF6?style=flat-square"/>

<div align="center">

| Layer | Technology |
|:--|:--|
| **Frontend / Framework** | Next.js, Node.js 18+ |
| **Authentication** | Clerk |
| **Database & Backend Services** | Supabase (PostgreSQL, RLS, REST) |
| **Hosting / Deployment** | Vercel |
| **Session Security** | JWT |

</div>

<br/>

---

## <img src="https://img.shields.io/badge/-Project%20Directory%20Structure-8B5CF6?style=flat-square"/>

```
amor-fly-platform/
├── app/ (or pages/)         # Next.js routes and views
├── components/              # Shared UI components
├── lib/                     # Supabase client, Clerk config, utilities
├── public/                  # Static assets
├── .env.local               # Environment variables (not committed)
└── package.json             # Project dependencies & scripts
```

<br/>

---

## <img src="https://img.shields.io/badge/-Quickstart%20Guide-8B5CF6?style=flat-square"/>

**1. Prerequisites** — Node.js 18+, a Supabase account, a Clerk account, and a Vercel account.

**2. Clone & Install**

```bash
git clone https://github.com/raahuldatta/Amor-Fly.git
cd Amor-Fly
npm install
```

**3. Environment Setup**

Create a `.env.local` file in the root directory:

```env
NEXT_PUBLIC_SUPABASE_URL=https://<your-supabase-project>.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
JWT_SECRET=your-super-secret-jwt-key-at-least-32-characters-long
NODE_ENV=development
```

> 💡 You can find these values in your Supabase project under **Project Settings → API**.

**4. Set Up Supabase**

1. Go to your [Supabase Dashboard](https://app.supabase.com)
2. Create a new project and database
3. Use the SQL Editor to create the schema (`users`, `pods`, `messages`, `connections`, `notifications`)
4. Enable **Row Level Security (RLS)** and write appropriate policies
5. *(Optional)* Seed test data via the SQL Editor or REST API

**5. Run Locally**

```bash
npm run dev
```

Visit **[http://localhost:3000](http://localhost:3000)** to view the app.

<br/>

---

## <img src="https://img.shields.io/badge/-Development%20Scripts-8B5CF6?style=flat-square"/>

| Command | Description |
|:--|:--|
| `npm run dev` | Start the development server |
| `npm run build` | Build the app for production |
| `npm run start` | Start the production server |
| `npm run lint` | Run ESLint |
| `npm run seed` | *(Optional)* Seed the Supabase DB via API/script |

<br/>

---

## <img src="https://img.shields.io/badge/-Deployment%20on%20Vercel-8B5CF6?style=flat-square"/>

**1. Configure Environment Variables** — go to **Vercel → Project Settings → Environment Variables** and add:

| Key | Value |
|:--|:--|
| `NEXT_PUBLIC_SUPABASE_URL` | Your Supabase project URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Your project's anonymous public API key |
| `SUPABASE_SERVICE_ROLE_KEY` | Service role key *(server-side only)* |
| `JWT_SECRET` | A strong random string, e.g. via `openssl rand -base64 32` |

**2. Deploy via Vercel CLI**

```bash
npm install -g vercel
vercel login
vercel --prod
```

Visit the generated URL to access your live app — or head straight to the current deployment: **[amor-fly-platform.vercel.app](https://amor-fly-platform.vercel.app)**

<br/>

---

## <img src="https://img.shields.io/badge/-Troubleshooting-8B5CF6?style=flat-square"/>

<details>
<summary><b>🔐 Auth issues (Clerk)</b></summary>
<br/>

- Ensure Clerk frontend and backend integrations are configured properly
- Make sure `JWT_SECRET` matches across environments

</details>

<details>
<summary><b>🧪 Supabase errors</b></summary>
<br/>

- Confirm correct `anon` and `service_role` keys
- Use Supabase logs and the SQL editor to debug
- Review RLS policies if data isn't returning

</details>

<details>
<summary><b>⚙️ Build / deploy issues</b></summary>
<br/>

- Check Vercel logs for runtime errors
- Ensure all env variables are properly set in both local and production environments

</details>

<br/>

---

## <img src="https://img.shields.io/badge/-Roadmap-8B5CF6?style=flat-square"/>

- [ ] AI-assisted Pod matching based on personality and skill vectors
- [ ] In-Pod video/voice sessions
- [ ] Public Pod leaderboards for growth points
- [ ] Mobile app companion

<br/>

---

## <img src="https://img.shields.io/badge/-Why%20Amor%20Fly%3F-8B5CF6?style=flat-square"/>

- 🌱 Grow through collaborative learning Pods
- 🔐 Clerk + Supabase = secure, scalable auth + database
- 💬 Real-time messaging and engagement features
- 🚀 Fully deployable with a single command

<br/>

---

## <img src="https://img.shields.io/badge/-Contributing%20%26%20License-8B5CF6?style=flat-square"/>

Contributions, issues, and feature requests are welcome — feel free to open a PR or file an issue.

This project is licensed under the **MIT License**.

<br/>

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=20,11,6&height=120&section=footer" width="100%"/>

</div>
