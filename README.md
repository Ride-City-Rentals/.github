# RideCity Rentals 🚗

RideCity Rentals is a premium peer-to-peer car rental platform designed to connect car owners (Hosts) with travelers (Guests). Built with a focus on luxury aesthetics and seamless user experience, the platform features a production-hardened security architecture, including automated session rotation and moderated real-time communication.

## 🏗 Project Architecture

The project is divided into four main components:

### [1. Web Platform (Frontend)](file:///c:/Users/dopem/Documents/RCR/software/website/README.md)
A modern, responsive web application built with **Next.js**, **Tailwind CSS**, and **Redux Toolkit**.

### [2. Cloud API (Backend)](file:///c:/Users/dopem/Documents/RCR/software/backend/RideCityRentals-backend/README.md)
A robust serverless backend powered by **Firebase Functions (v2)** and **Node.js**, featuring a custom JWT-based authentication system with per-user session revocation.

### [3. Admin Dashboard](file:///c:/Users/dopem/Documents/RCR/software/admin/README.md)
A separate Next.js console for platform staff — user/listing moderation, refunds, payouts, support, and analytics — authenticated against the same backend's admin-only JWT/role system.

### [🔍 Data & Business Flows](file:///c:/Users/dopem/Documents/RCR/software/DATA_FLOW.md)
A detailed guide on how data moves between the Frontend, Backend, and Paystack. **Highly recommended for new developers.**

---

## 🔐 Security & Production Hardening

This platform has undergone a rigorous security audit and features several enterprise-grade protections:

- **JWT Rotation & Revocation**: A secure **Access/Refresh Token** pattern using `HttpOnly` cookies to prevent XSS-based token theft, backed by a per-user `sessionVersion` counter checked on every authenticated request — logout, admin suspension, and staff deactivation immediately invalidate all of a user's outstanding sessions rather than waiting out the access token's natural expiry.
- **Firebase Auth Sync**: Uses **Custom Tokens** to synchronize backend authentication with Firestore real-time listeners, enabling secure, participant-only database rules.
- **Moderated Communication**: A custom **Secure Chat API** that scans messages for off-platform contact information (phones/emails) and blocks messages for cancelled or completed bookings.
- **Financial Integrity**: All payment processing uses **Firestore Transactions** and server-side amount verification to prevent race conditions and webhook replay attacks.

---

## 🚀 Quick Start

### 1. Clone the repository
```bash
git clone <repository-url>
cd RideCityRentals
```

### 2. Set up the Backend
```bash
cd backend/RideCityRentals-backend/functions
npm install
npm run deploy # Deploys to Firebase Cloud Run
```

### 3. Set up the Website
```bash
cd website
npm install
npm run dev # Starts development server at localhost:3000
```

### 4. Set up the Admin Dashboard
```bash
cd admin
npm install
npm run dev # See admin/README.md for details
```

---

## 🛠 Tech Stack

| Layer | Technologies |
| :--- | :--- |
| **Frontend** | Next.js, TypeScript, Redux Toolkit, Tailwind CSS, Lucide Icons |
| **Backend** | Firebase Functions v2, Express.js, Node.js |
| **Database** | Google Firestore (with participant-level Security Rules) |
| **Security** | JWT (HttpOnly Cookies), Firebase Custom Tokens, Paystack Webhooks |
| **Payments** | Paystack API |
| **Hosting** | Firebase App Hosting (Frontend, see `website/apphosting.yaml`), Firebase Cloud Functions (Backend) |

---

## 📁 Key Directories

- `website/`: The Next.js frontend application (guest + host).
- `backend/RideCityRentals-backend/`: The Firebase backend project.
  - `functions/`: The core API logic and controllers.
- `admin/`: The Next.js admin dashboard for platform staff.
- `mobile_frontend/`: (Internal) The mobile application codebase.
- `qa_agent/`: An in-development autonomous AI QA CLI tool for auditing this repo's own backend — see `qa_agent/README.md`. Not part of the production platform.

---

© 2026 RideCity Rentals. All rights reserved.
