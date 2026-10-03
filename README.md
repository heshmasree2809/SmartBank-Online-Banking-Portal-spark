# SmartBank: Online Banking Portal (Front-End Simulation)

A role-based banking web app built with **React 19**, **TypeScript**, **Tailwind CSS v4** and **Vite**. It simulates retail banking, a branch teller console and an admin/risk console, with double-entry ledger logic, simulated OTP confirmation, a rule-based fraud flag and an audit log.

> **Scope note:** SmartBank is a client-side project. All data (users, accounts, transactions) lives in the browser's `localStorage`, seeded from mock data. There is no real backend, no real database and no real money movement. OTP, bill payments and card controls are simulated.

**Live demo:** `<add your public URL>`  |  **Source:** https://github.com/heshmasree2809/SmartBank-Online-Banking-Portal-spark

---

## Table of Contents
1. [About](#1-about)
2. [Demo Walkthrough](#2-demo-walkthrough)
3. [Features](#3-features)
4. [Tech Stack](#4-tech-stack)
5. [Architecture](#5-architecture)
6. [Data Models](#6-data-models)
7. [Service Layer](#7-service-layer)
8. [Setup](#8-setup)
9. [Security Notes and Limitations](#9-security-notes-and-limitations)
10. [Engineering Challenges](#10-engineering-challenges)
11. [Future Enhancements](#11-future-enhancements)
12. [Author](#12-author)

---

## 1. About

SmartBank brings three banking experiences into one interface: a retail customer portal, a branch teller console and a central admin console. It was built to practice modeling banking rules (ledger entries, transfer limits, beneficiary cooling periods, fraud flags) in a typed React application.

### Goals
- **Responsive feel:** balances update immediately after a transfer through an observable store.
- **Role separation:** distinct views for Customers, Branch Officers and Admins.
- **Traceability:** every state change is written to an audit log, and statements can be printed.

---

## 2. Demo Walkthrough

Open the app and use the one-click role buttons on the login screen to switch between the three seeded demo profiles. Each walkthrough takes about a minute.

| Role | Try this | What to look for |
|---|---|---|
| **Customer** | Go to Transfers, pick an account, choose a saved beneficiary and send a small amount | The OTP modal appears, then the balances update immediately and a receipt is shown |
| **Customer** | Add a new beneficiary, then try to send money to it right away | The payee is in a 30-minute cooling period and the transfer is blocked |
| **Customer** | Send a transfer above ₹1,00,000 | The transaction is marked suspicious and appears in the admin fraud queue |
| **Customer** | Open Cards and lock a card, then change a daily limit | The card status and limit update straight away |
| **Teller** | Open the Cash Desk and record a deposit or withdrawal | A cash voucher is generated and the account balance changes |
| **Teller** | Open KYC review and change a customer's KYC tier | The new tier shows on the customer's profile |
| **Admin** | Open the AML queue, then the Audit Trail | The flagged transfer is listed, and each state change shows its actor, time and before/after values |

**Keyboard shortcuts:** `Alt + ←` goes back through your view history. Other shortcuts are listed in `App.tsx`.

**Reset the demo:** clear this site's data in your browser (DevTools → Application → Local Storage → Clear) and reload to restore the seed data.

---

## 3. Features

### Retail Customer
- **Multiple account types:** Savings, Current, Salary and Fixed Deposit, with calculated balances.
- **Fund transfers:** Internal, NEFT, RTGS and IMPS modes with account masking and input validation.
- **Simulated OTP confirmation:** a 6-digit OTP modal with resend cooldown (the code is generated client-side for demo purposes).
- **Beneficiary manager:** add, nickname and delete payees, with a 30-minute cooling period on new payees.
- **Bill payments:** simulated BBPS-style billers (mobile, electricity, water, gas, broadband, credit card) with receipts.
- **Card controls:** lock/unlock, contactless and international toggles, and daily ATM/POS limits.
- **Statement generator:** filter by date range, account and type, then print or save as PDF.
- **Service desk:** raise tickets (chargeback, cheque book, KYC update, card replacement) with SLA timers.
- **Profile and security settings:** contact details, address, avatar and 2FA preference.

### Branch Teller Console
- **Cash desk:** deposits and withdrawals with a voucher and balance checks.
- **KYC review:** view submitted details and update KYC tier.
- **Loan desk:** credit score check, EMI calculator and disbursement flow.
- **Ticket resolution:** triage customer tickets with SLA timers.
- **Vault balance:** compare physical cash against ledger reserves.

### Admin and Risk Console
- **Fraud/AML queue:** rule-based flags for high-value transactions and rapid consecutive transfers.
- **User and role management:** change user status (`ACTIVE`, `SUSPENDED`) and role (`CUSTOMER`, `BANK_EMPLOYEE`, `ADMIN`).
- **Configuration:** adjust interest rates, daily transfer limits and the cooling period.
- **Audit trail:** log of actors, timestamps and operations with before/after values.

---

## 4. Tech Stack

| Layer | Technology |
|---|---|
| UI framework | React 19 |
| Language | TypeScript 5.8 |
| Styling | Tailwind CSS v4 |
| Icons | Lucide React |
| Animation | Motion |
| Charts | Recharts |
| Build tool | Vite 6 |
| State and persistence | Custom observable store (`BankingStore`) backed by `localStorage` |

No backend server or database is used.

---

## 5. Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                       React UI (Vite)                        │
│   Customer Portal   |   Teller Console   |   Admin Console   │
└──────────────────────────────┬───────────────────────────────┘
                               │
                    App shell: view router,
                    role guard, hotkeys, history stack
                               │
┌──────────────────────────────▼───────────────────────────────┐
│                 BankingStore (singleton service)             │
│  - Observable subscriptions (UI re-renders on change)        │
│  - Double-entry ledger logic for transfers                   │
│  - Daily limit and cooling-period checks                     │
│  - Rule-based AML flagging                                   │
│  - Simulated OTP generation and check                        │
└──────────────────────────────┬───────────────────────────────┘
                               │
┌──────────────────────────────▼───────────────────────────────┐
│              localStorage + seeded mock data                 │
│   accounts, users, transactions, cards, loans, audit logs    │
└──────────────────────────────────────────────────────────────┘
```

### Project structure
```
src/
├── App.tsx                # App controller, hotkeys, navigation
├── main.tsx               # React root
├── types.ts               # Domain types and role definitions
├── data/mockData.ts       # Seed users, accounts, billers, audit entries
├── services/bankingStore.ts   # Store and ledger logic
└── components/
    ├── accounts/  admin/  auth/  beneficiaries/  bills/  cards/
    ├── common/  dashboard/  employee/  profile/  statements/
    └── support/  transactions/  transfers/
```

---

## 6. Data Models

Entities are modeled as TypeScript types with relational-style references (stored as JSON in `localStorage`, not in a SQL database).

- **User:** `id`, `email`, `role`, `kycTier`, `twoFactorEnabled`
- **BankAccount:** `id`, `userId`, `accountNumber`, `accountType`, `currentBalance`, `dailyLimit`
- **Beneficiary:** `id`, `userId`, `accountNumber`, `ifsc`, `status` (`COOLING` / `ACTIVE`), `coolingEndsAt`
- **BankCard:** `id`, `userId`, `accountId`, masked number, `isLocked`, `dailyLimit`
- **Transaction:** `id`, `accountId`, `amount`, `flow` (`DEBIT` / `CREDIT`), `isSuspicious`
- **BillPayment** and **ServiceRequest:** linked to accounts and users by id

---

## 7. Service Layer

`bankingStore` exposes synchronous methods used by the UI:

| Area | Methods |
|---|---|
| Session | `login`, `logout`, `switchRole` (demo persona switcher), `updateUser` |
| Transfers | `processTransfer`, `getTransactions`, `flagSuspiciousTransaction` |
| Beneficiaries | `addBeneficiary`, `deleteBeneficiary` |
| Cards | `toggleCardLock`, `updateCardLimits`, `toggleCardFeature` |

`login` is a demo flow with no password verification, and `switchRole` exists for testing the three role views.

---

## 8. Setup

**Requirements:** Node.js 18+ and npm 9+

```bash
git clone https://github.com/heshmasree2809/SmartBank-Online-Banking-Portal-spark.git
cd SmartBank-Online-Banking-Portal-spark
npm install
npm run dev      # http://localhost:3000
npm run build    # production bundle in dist/
npm run lint     # TypeScript type check (tsc --noEmit)
```

---

## 9. Security Notes and Limitations

SmartBank demonstrates banking *rules and flows*, not production security. Because everything runs in the browser:

| Area | What the project does | Limitation |
|---|---|---|
| Role access | Role-based views and route guards in the UI | Not enforced by a server; a user could alter local state |
| OTP | Simulated 6-digit confirmation step | Generated client-side; not a real second factor |
| Daily limits | Per-account daily transfer limit check | Enforced client-side |
| Fraud flagging | Rules for high-value (above ₹1,00,000) and rapid consecutive transfers | Simple heuristics, not a risk model |
| Cooling period | 30-minute hold on new beneficiaries | Based on local timestamps |
| Audit log | Records state changes with actor, time and before/after values | Stored in `localStorage` and not tamper-proof |
| Data masking | Account and card numbers shown masked in the UI | Display masking only |
| Input handling | TypeScript types and form validation; React escapes rendered text | Not a substitute for server-side validation |

A production system would need a server-side API, a real database, real authentication, and server-enforced authorization and limits.

---

## 10. Engineering Challenges

### 1. Keeping transfer state consistent
**Problem:** a transfer must debit one account and credit another without leaving a half-applied state.
**Approach:** `processTransfer()` validates inputs, then applies both balance changes and writes the ledger entries in one synchronous step before notifying subscribers. Because this runs in a single browser thread, there are no concurrent writers; a real multi-user system would need database transactions.

### 2. Navigation history across role-based views
**Problem:** the browser back button did not match the in-app view state in a multi-role dashboard.
**Approach:** a custom `viewHistory` stack with breadcrumbs and an `Alt + ←` shortcut for stepping back.

### 3. Rule-based fraud flagging
**Problem:** flag risky transfers without a backend.
**Approach:** a small rule pipeline checks transfer size (above ₹1,00,000) and rapid consecutive transfers, then sets an `isSuspicious` flag that appears in the admin queue.

---

## 11. Future Enhancements

- [ ] Real backend API with a database and server-side authorization
- [ ] Password hashing and token-based authentication
- [ ] Automated tests (unit tests for `bankingStore`, end-to-end tests for transfer and OTP flows)
- [ ] WebAuthn / FIDO2 login
- [ ] Multi-currency wallets
- [ ] Push notifications for credits and security events

---

## 12. Author

**Avuthu Heshma Sree**
- GitHub: [heshmasree2809](https://github.com/heshmasree2809)
- Email: avuthuheshmasree@gmail.com
- Built: August 2026

## License

Add a `LICENSE` file (for example MIT) to the repository and state it here.
