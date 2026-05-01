# RideCity Rentals 🚗

RideCity Rentals is a premium peer-to-peer car rental platform designed to connect car owners (Hosts) with travelers (Guests). Built with a focus on luxury aesthetics and seamless user experience, the platform handles everything from vehicle listing and earnings calculation to secure payments and booking management.

## 🏗 Project Architecture

The project is divided into two main repositories:

### [1. Web Platform (Frontend)](file:///c:/Users/dopem/Documents/RCR/software/website/README.md)
A modern, responsive web application built with **Next.js 16**, **Tailwind CSS**, and **Redux Toolkit**. 

### [2. Cloud API (Backend)](file:///c:/Users/dopem/Documents/RCR/software/backend/RideCityRentals-backend/README.md)
A robust serverless backend powered by **Firebase Functions (v2)** and **Node.js**.

### [🔍 Data & Business Flows](file:///c:/Users/dopem/Documents/RCR/software/DATA_FLOW.md)
A detailed guide on how data moves between the Frontend, Backend, and Paystack. **Highly recommended for new developers.**

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

---

## 🛠 Tech Stack

| Layer | Technologies |
| :--- | :--- |
| **Frontend** | Next.js, TypeScript, Redux Toolkit, Tailwind CSS, Lucide Icons |
| **Backend** | Firebase Functions v2, Express.js, Node.js |
| **Database** | Google Firestore |
| **Payments** | Paystack API |
| **Hosting** | Vercel (Frontend), Firebase Cloud Run (Backend) |

---

## 📁 Key Directories

- `website/`: The Next.js frontend application.
- `backend/RideCityRentals-backend/`: The Firebase backend project.
  - `functions/`: The core API logic and controllers.
- `mobile_frontend/`: (Internal) The mobile application codebase.

---

© 2026 RideCity Rentals. All rights reserved.
