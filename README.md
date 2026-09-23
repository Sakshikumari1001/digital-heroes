# Digital Heroes 🏌️‍♀️

A subscription-driven web platform combining **golf performance tracking, charity fundraising, and a monthly draw-based reward engine** — built for the **Digital Heroes trainee selection process (PRD Level 1)**.

<p align="center">
  <a href="https://digital-heroes-5kvz.vercel.app/">
    <img src="https://img.shields.io/badge/Live%20Demo-Digital%20Heroes-success?style=for-the-badge&logo=vercel" alt="Live Demo"/>
  </a>
</p>

---

## ✨ What It Does

Digital Heroes is a web platform that connects golf performance tracking with charity contributions and monthly draw-based rewards.

### 👤 User Features

* 🔐 User registration and login
* 💳 Monthly/yearly subscription plans via Stripe
* ⛳ Enter the last 5 golf scores using Stableford format (1–45)
* ❤️ Select a charity to support
* 💰 Choose the portion of subscription allocated to charity
* 🎟️ Participate in monthly draw-based prize pools
* 🏆 View winnings and participation history
* 📊 Track payout status from the personal dashboard

### 🛠️ Admin Features

* 👥 Manage users
* ❤️ Manage charities
* 🎯 Configure and manage monthly draws
* 🎲 Simulate and publish draws
* 🏆 Review and verify winners
* 💸 Manage payout status

---

## 📸 Screenshots

### 👤 User Dashboard

![Dashboard](./Screenshot%202026-09-22%20162156.png)

### 🛠️ Admin Panel

![Admin Panel](./Screenshot%202026-09-22%20162246.png)

### 🔐 Login

![Login](./Screenshot%202026-09-22%20162411.png)

### 📝 Signup

![Signup](./Screenshot%202026-09-22%20162431.png)

---

## 🧱 Tech Stack

### ⚡ Frontend & Framework

<p align="left">
  <img src="https://cdn.simpleicons.org/nextdotjs/white" alt="Next.js" width="55" height="55"/>
  &nbsp;&nbsp;&nbsp;
  <img src="https://cdn.simpleicons.org/tailwindcss/06B6D4" alt="Tailwind CSS" width="55" height="55"/>
</p>

**Next.js** • **Tailwind CSS**

### 🗄️ Database & Authentication

<p align="left">
  <img src="https://cdn.simpleicons.org/supabase/3ECF8E" alt="Supabase" width="55" height="55"/>
</p>

**Supabase**

### 💳 Payments

<p align="left">
  <img src="https://cdn.simpleicons.org/stripe/635BFF" alt="Stripe" width="55" height="55"/>
</p>

**Stripe**

### ☁️ Deployment

<p align="left">
  <img src="https://cdn.simpleicons.org/vercel/white" alt="Vercel" width="55" height="55"/>
</p>

**Vercel**

---

## 🗂️ Key Routes

| Route            | Description                                                         |
| ---------------- | ------------------------------------------------------------------- |
| `/login`         | User login                                                          |
| `/signup`        | User registration                                                   |
| `/dashboard`     | Subscriber dashboard — scores, charity, participation, and winnings |
| `/subscribe`     | Plan selection and Stripe checkout                                  |
| `/charities`     | Charity directory                                                   |
| `/admin`         | Admin panel — draws and winners management                          |
| `/admin/draws`   | Configure, simulate, and publish monthly draws                      |
| `/admin/winners` | Verify winner proof and mark payouts                                |

---

## 🔄 User Workflow

```text
Create Account
      ↓
Select Charity & Donation Share
      ↓
Choose Subscription
      ↓
Complete Stripe Checkout
      ↓
Enter Golf Scores
      ↓
Participate in Monthly Draw
      ↓
Track Winnings & Payout Status
```

## 🔄 Admin Workflow

```text
Admin Login
     ↓
Open Admin Panel
     ↓
Manage Draws
     ↓
Simulate / Publish Draw
     ↓
Review Winners
     ↓
Verify Winner Proof
     ↓
Mark Payout as Paid
```

---

## 🗄️ Database Schema (Supabase / PostgreSQL)

Row-Level Security is enabled on every table, so a user can only read/write their own data; admin-only writes (publishing draws, marking payouts) go through a service-role key used exclusively inside server-verified admin actions.

| Table            | Purpose                                                          |
| ----------------- | ----------------------------------------------------------------- |
| `profiles`         | User profile, role (subscriber/admin), chosen charity & %       |
| `subscriptions`     | Plan, status, Stripe customer/subscription IDs, renewal date    |
| `scores`            | Rolling 5-score window per user (DB trigger auto-prunes oldest) |
| `charities` / `charity_events` | Charity directory & upcoming event listings          |
| `donations`         | Independent, one-off contributions (not tied to gameplay)       |
| `draws`             | Monthly draw config, winning numbers, pool total, rollover      |
| `draw_entries`      | Snapshot of each participant's scores at draw time               |
| `winners`           | Match tier, prize amount, verification & payment status         |

---

## ⚙️ How the Draw Engine Works

* **Random mode** — 5 unique numbers (1–45) picked uniformly using a cryptographically secure RNG (`node:crypto`)
* **Algorithmic mode** — numbers players log most often as scores are weighted higher (`weight = 1 + frequency`), so frequently-played numbers are more likely to be drawn, while every number stays possible
* **Prize pool** — 50% of active subscribers' monthly-equivalent fees fund the pool each month (yearly plans normalised as `yearly price ÷ 12`)
* **Tiers** — 5-number match gets 40% of the pool (jackpot), 4-number gets 35%, 3-number gets 25%
* **Splitting** — a tier's pool is split equally among all winners in that tier
* **Rollover** — if nobody hits the 5-number jackpot, that tier's full amount carries into next month's jackpot (4- and 3-number tiers do **not** roll over, per the PRD)
* **Simulate → Publish** — admins can simulate a draw (preview numbers, pool, and projected winners) as many times as needed before publishing; publishing locks the entries and generates winner records

---

## 🧩 Assumptions & Design Decisions

The PRD intentionally left some mechanics open to interpretation. Documented here for transparency:

| Area | Assumption Made |
|---|---|
| **Prize pool size** | 50% of each subscriber's monthly-equivalent fee funds the pool |
| **Draw eligibility** | Only active subscribers with **all 5 scores** on file are entered into that month's draw |
| **Match counting** | Matches are counted on **distinct** score values — a duplicate score can only count once |
| **"Oldest" score** | Determined by **date played**, not entry order — the earliest-dated score is replaced first when a 6th is added |
| **Rollover scope** | Only the 5-match jackpot rolls over when unclaimed; 4- and 3-match pools do not |

---

## ✅ Testing Checklist

- [x] User signup & login (charity selection at onboarding)
- [x] Subscription flow — monthly and yearly, via Stripe Checkout
- [x] Score entry — 5-score rolling logic, duplicate-date rejection, edit/delete
- [x] Draw system — random & algorithmic simulation, publish, prize maths verified
- [x] Charity selection and contribution % changes
- [x] Independent donation flow
- [x] Winner verification — proof upload → admin approve/reject → payout tracking
- [x] User dashboard — all modules functional
- [x] Admin panel — draws & winners management
- [x] Responsive layout (mobile & desktop)
- [x] Error handling across forms and payment flows

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/Sakshikumari1001/digital-heroes.git
```

### 2. Navigate to the Project

```bash
cd digital-heroes
```

### 3. Install Dependencies

```bash
npm install
```

### 4. Configure Environment Variables

Create a `.env.local` file and add your own **Supabase** and **Stripe** credentials.

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_supabase_service_role_key

# Stripe
STRIPE_SECRET_KEY=your_stripe_secret_key
STRIPE_PRICE_MONTHLY=your_monthly_price_id
STRIPE_PRICE_YEARLY=your_yearly_price_id
STRIPE_WEBHOOK_SECRET=your_stripe_webhook_secret

NEXT_PUBLIC_SITE_URL=your_deployed_site_url
```

> ⚠️ Never commit your real API keys or secrets to GitHub.

### 5. Run the Development Server

```bash
npm run dev
```

Open the local development server in your browser.

---

## 🧪 Testing

The application can be tested using **Stripe Test Mode**.

### Test Card

```text
Card Number: 4242 4242 4242 4242
Expiry Date: Any future date
CVC: Any 3 digits
```

> Use Stripe's test environment when testing payments. No real payment is processed.

---

## 🌐 Live Application

**Live App:**
https://digital-heroes-5kvz.vercel.app/

**GitHub Repository:**
https://github.com/Sakshikumari1001/digital-heroes

---

## 📌 Project Highlights

* 🔐 Authentication and user account management
* 💳 Subscription-based payment flow
* ⛳ Golf score tracking
* ❤️ Charity contribution management
* 🎟️ Monthly draw participation
* 🏆 Winner verification and payout tracking
* 👨‍💼 Dedicated admin dashboard
* 📱 Responsive web interface
* ☁️ Vercel deployment

---

## 🔭 Future Scope

Given more time beyond the assignment window, the following would be natural next steps:

* **Admin: Users & Charities CRUD** — full user management (edit/suspend), charity content management with media uploads
* **Admin: Reports & Analytics** — total users, prize pool history, charity contribution totals, and draw statistics as visual dashboards
* **Homepage & marketing site** — an emotion-led, animation-rich landing page per the PRD's UI/UX brief
* **Email notifications** — draw results, win confirmations, and payout updates
* **Automated monthly draws** — a scheduled job to auto-run the draw on a fixed date instead of manual admin triggering
* **Leaderboards & score history charts** — visualising a player's Stableford trend over time
* **Multi-currency support** — localised pricing for international rollout
* **Native mobile app** — reusing the same Supabase backend
* **Audit logging** — a trail of admin actions (who published which draw, who approved which payout)

---

## 📄 About

Digital Heroes was built as a **sample full-stack assignment for the Digital Heroes trainee selection process**, based on **PRD Level 1 (March 2026)**.

The project demonstrates a complete web application workflow involving user authentication, subscriptions, golf score management, charity selection, draw participation, winner verification, and administrative management.

---

## 👥 Authors

| Name                 | Role                 |
| -------------------- | -------------------- |
| **Prem Kumar Gupta** | Full Stack Developer |
| **Sakshi Kumari**    | Full Stack Developer |

---

## ⭐ Acknowledgement

Built with **Next.js, Supabase, Stripe, Tailwind CSS, and Vercel**.

If you found this project interesting, feel free to explore the live application and repository.
