# 🌊 Data & Business Flows

This document explains the core data lifecycles within RideCity Rentals, helping new developers understand how the Frontend, Backend, and Third-Party Services (Paystack, Firestore) interact.

---

## 1. The Booking & Payment Flow
This is the most critical flow in the system, involving guest discovery, transaction processing, and record finalization.

```mermaid
sequenceDiagram
    participant G as Guest (Browser)
    participant B as Backend (API)
    participant F as Firestore (DB)
    participant P as Paystack (Gateway)

    G->>G: Calculate Duration & Total
    G->>B: POST /guest/bookings/create
    B->>B: Re-calculate Duration (Math.ceil)
    B->>F: Create Booking (status: awaiting_payment)
    B-->>G: Return Booking ID
    G->>P: Open Paystack Popup (amount, ref)
    P->>G: Payment Successful
    G->>B: POST /guest/bookings/record-payment (Transaction)
    B->>F: Verify Amount & Idempotency
    B->>F: Update Booking (status: paid)
    B-->>G: Success Response
    G->>G: Redirect to /booking/receipt
```

### Key Logic:
1. **Idempotency**: The `/record-payment` endpoint uses Firestore Transactions to ensure a booking is never processed twice (webhook replay protection).
2. **Amount Verification**: The Backend re-verifies the Paystack payment amount against the `totalPayment` stored in the database.
3. **Recording**: The booking is created *before* payment to ensure we have a record if the user closes their browser mid-payment.

---

## 2. Authentication, Refresh & Auth Sync
How we manage secure sessions and real-time database access without exposing credentials.

```mermaid
sequenceDiagram
    participant U as User (Browser)
    participant B as Backend (API)
    participant FA as Firebase Auth
    participant FS as Firestore

    U->>B: POST /shared/login
    B->>B: Generate JWT (Access)
    B->>B: Set HttpOnly Cookie (Refresh)
    B->>FA: Create Custom Token (UID)
    B-->>U: Return AccessToken + FirebaseToken
    U->>FA: signInWithCustomToken(FirebaseToken)
    U->>FS: Open onSnapshot() Listener
    FS-->>FS: Validate via Security Rules (request.auth.uid)
```

### Key Logic:
- **JWT Rotation**: Access tokens are short-lived. The `apiClient.ts` interceptor automatically uses the HttpOnly refresh cookie to get a new token when a `401` is received.
- **Auth Sync**: The `firebaseToken` synchronizes our custom backend identity with the Firebase SDK, allowing us to use **Participant-Only** security rules in Firestore.

---

## 3. Moderated Chat Flow
How we prevent revenue leakage and protect users from off-platform scams.

```mermaid
sequenceDiagram
    participant U as User (Sender)
    participant B as Backend (API)
    participant F as Firestore
    participant R as Receiver

    U->>B: POST /shared/chat/send (text, chatId)
    B->>B: Detect Phone/Email (Moderation)
    B->>F: Verify Booking Status (Not cancelled/completed)
    B->>F: Write Message + Update Metadata (Transaction)
    F-->>R: Real-time update via onSnapshot
```

### Key Logic:
- **Moderation**: Messages containing contact info are rejected with a warning.
- **Lifecycle Locking**: Once a trip is `cancelled` or `completed`, the API blocks all new messages for that `bookingId`.
- **Atomic Metadata**: Unread counts and "Last Message" are updated in the same transaction as the message itself.

---

## 4. Card Tokenization (Save Card) Flow
How we securely store payment methods without ever touching sensitive card data.

```mermaid
sequenceDiagram
    participant U as User (Host/Guest)
    participant B as Backend (API)
    participant P as Paystack
    participant F as Firestore

    U->>P: Pay ₦100 Authorization Charge
    P-->>U: Return Transaction Reference
    U->>B: POST /shared/payment/verify-card (ref)
    B->>P: Verify Reference (using Secret Key)
    P-->>B: Return "authorization" object (token)
    B->>F: Save token to /account_details/{uid}
    B-->>U: Success (Card Added)
```

### Key Logic:
- **Authorization Charge**: A small ₦100 transaction is required by Paystack to generate a reusable "authorization" token.
- **Security**: The Frontend never sees the token; it only sends the reference. The Backend retrieves the token using the `Secret Key` server-to-server.

---

## 5. Host Earnings Logic
1. **Host Input**: Sets `dailyRate`.
2. **Backend Config**: Fetches `rentalFee` (e.g., 15%) from `admin/fees`.
3. **Calculation**:
   - `totalPayment` = `dailyRate` * `days`
   - `transactionFee` = `totalPayment` * (`rentalFee` / 100)
   - `ownerBalance` = `totalPayment` - `transactionFee`
4. **Storage**: Both the total and the host's net share are stored in the `bookings` document under `hostMeta`.

---

© 2026 RideCity Rentals. All rights reserved.
