# SmartBank — Enterprise Online Banking Management Portal

![SmartBank Banner](https://images.unsplash.com/photo-1563986768609-322da13575f3?auto=format&fit=crop&w=1200&q=80)

> A modern, resilient, role-based Core Banking System (CBS) and customer portal built with **React 19**, **TypeScript**, **Tailwind CSS**, and **Vite**. Features multi-tier Role-Based Access Control (Customer, Branch Officer / Teller, Central Admin), dual-entry ledger transaction processing, simulated OTP authorization, AML fraud detection, bill payments, and audit tracking.

---

## Table of Contents
- [1. About](#1-about)
- [2. Screenshots](#2-screenshots)
- [3. Features](#3-features)
- [4. Tech Stack](#4-tech-stack)
- [5. Architecture](#5-architecture)
- [6. Database & Data Models](#6-database--data-models)
- [7. API & Service Layer](#7-api--service-layer)
- [8. Setup & Installation](#8-setup--installation)
- [9. Security & Compliance](#9-security--compliance)
- [10. Engineering Challenges & Solutions](#10-engineering-challenges--solutions)
- [11. Future Enhancements](#11-future-enhancements)
- [12. Author](#12-author)

---

## 1. About

**SmartBank** is an enterprise-grade digital banking web application engineered to simulate core banking operations with industrial fidelity. It solves the friction of traditional internet banking by uniting retail banking operations, bank branch teller consoles, and back-office compliance monitoring into a cohesive, high-performance interface.

The platform enforces zero-trust security principles, strict double-entry ledger logic, beneficiary cooling periods, dynamic transaction limits, and real-time AML (Anti-Money Laundering) anomaly flagging.

### Core Objectives
- **Zero-Latency Financial Experience**: Instant balance updates, reactive event streaming, and optimistic UI transitions.
- **Strict Role-Based Segregation (RBAC)**: Custom operational views for Retail Customers, Branch Officers, and Central Bank Admins.
- **Regulatory-Grade Auditability**: Cryptographic hash chaining, tamper-evident audit logs, and verified printable statement generation.

---

## 2. Screenshots

| Customer Dashboard | Money Transfer & OTP Verification |
|:---:|:---:|
| ![Dashboard Preview](https://images.unsplash.com/photo-1559526324-4b87b5e36e44?auto=format&fit=crop&w=600&q=80) | ![Transfer Flow](https://images.unsplash.com/photo-1556742049-0a67c5574f73?auto=format&fit=crop&w=600&q=80) |
| *Real-time balance, spending breakdown & quick actions* | *Beneficiary selection, transfer limits & OTP security* |

| Employee / Teller Desk | Enterprise Admin & AML Portal |
|:---:|:---:|
| ![Teller Desk](https://images.unsplash.com/photo-1450133064473-71024230f91b?auto=format&fit=crop&w=600&q=80) | ![Admin Console](https://images.unsplash.com/photo-1551836022-d5d88e9218df?auto=format&fit=crop&w=600&q=80) |
| *Cash deposit/withdrawal, loan underwriting & KYC approval* | *System liquidity, fraud risk queue & IAM role control* |

---

## 3. Features

### 👤 Retail Customer Banking
- **Multi-Account Portfolio**: Manage Savings, Current, Salary, and Fixed Deposit accounts with real-time balance calculations.
- **Fund Transfers (NEFT / RTGS / IMPS / Internal)**: Instant inter-bank and intra-bank transfers with account masking and validation.
- **Two-Factor OTP Security**: Secure OTP authentication modal with interactive auto-fill, resend cooldowns, and transaction confirmation.
- **Beneficiary Lifecycle Manager**: Add, verify, nickname, and delete beneficiaries with a regulatory 30-minute cooling period.
- **Utility & BBPS Bill Payments**: Recharge mobile, pay electricity, water, gas, broadband, and credit card bills with simulated receipt generation.
- **Card Security & Controls**: Instant card lock/freeze, toggle contactless (NFC) payments, international transactions, and adjust daily ATM/POS spending limits.
- **Verified Statement Generator**: Filter transactions by date range, account, and type; download stamped legal PDF/print statements.
- **Support & Service Desk**: Raise banking tickets (Chargeback, Cheque Book, KYC update, Card replacement) with live SLA resolution trackers.
- **Profile & Security Center**: Manage contact information, residential address, avatar selection, biometric/2FA settings, and review active sessions.

### 🏦 Bank Employee / Teller Console
- **Cash Operations Desk**: Deposit and withdrawal processing with cash voucher generator and balance validations.
- **KYC & Account Onboarding**: Review customer identity documents (PAN, Aadhaar), update KYC tiers (Tier 1, Tier 2, Full KYC), and approve applications.
- **Loan Underwriting**: Loan origination desk with credit score verification, EMI calculator, and loan disbursement controls.
- **Support Ticket Resolution**: Triage and resolve escalated customer service requests with SLA timers.
- **Branch Vault Balance**: Track physical cash-in-vault vs. electronic ledger reserves in real time.

### 🛡️ Central Bank Administration & Risk Portal
- **AML & Fraud Detection Queue**: Rule-based transaction anomaly detector (high value velocity, abnormal geolocation, threshold breaches).
- **IAM User & Role Administration**: Manage system users, toggle status (`ACTIVE`, `SUSPENDED`), and elevate permissions (`CUSTOMER`, `BANK_EMPLOYEE`, `ADMIN`).
- **Core Banking Configuration**: Adjust base savings interest rates, lending rates, daily transfer limits, and cooling period durations.
- **Tamper-Evident Audit Trail**: Real-time event log capturing IP addresses, actors, timestamped operations, and before/after state diffs.

---

## 4. Tech Stack

| Layer | Technology | Description |
|---|---|---|
| **Frontend Framework** | React 19 (`19.0.1`) | Modern declarative UI with Concurrent Mode support |
| **Language** | TypeScript (`~5.8.2`) | Strict type checking, interfaces, and discriminated unions |
| **Styling & Design** | Tailwind CSS (`^4.1.14`) | Responsive utility-first design system with dark palette |
| **Icons** | Lucide React (`^0.546.0`) | Comprehensive banking and enterprise vector icon suite |
| **Animations** | Motion (`^12.23.24`) | Smooth layout transitions and interactive UI feedback |
| **Data Visualizations** | Recharts (`^3.10.1`) | Analytical cash flow and spending distribution charts |
| **Build Tooling** | Vite (`^6.2.3`) | Ultra-fast HMR and optimized production bundling |
| **Server & Runtime** | Node.js + Express (`^4.21.2`) | Server-side proxy and application hosting |
| **State Persistence** | Reactive LocalStore | Multi-tab synchronized state with observable subscriptions |

---

## 5. Architecture

SmartBank utilizes a clean, decoupled architecture separating the presentation layer, client-side domain store, state observers, and secure data persistence.

```
┌────────────────────────────────────────────────────────────────────────┐
│                          SMARTBANK CLIENT UI                           │
│  ┌──────────────────────┬──────────────────────┬────────────────────┐  │
│  │   Customer Portal    │   Teller / Officer   │   Central Admin    │  │
│  │ (Transfers, Bills,   │ (Cash Ops, Loans,    │ (AML Fraud, IAM,   │  │
│  │  Cards, Statements)  │  KYC Verification)   │  Audit, Rates)     │  │
│  └──────────┬───────────┴──────────┬───────────┴──────────┬─────────┘  │
│             │                      │                      │            │
│             ▼                      ▼                      ▼            │
│  ┌──────────────────────────────────────────────────────────────────┐  │
│  │                 GLOBAL APP SHELL & ROUTER                        │  │
│  │  - Navigation History Stack & Breadcrumb Bar                     │  │
│  │  - Role Guard & RBAC Permission Filter                           │  │
│  │  - Global Hotkey Manager (Ctrl+D, Ctrl+T, Ctrl+S, Alt+←)         │  │
│  └─────────────────────────────────┬────────────────────────────────┘  │
└────────────────────────────────────┼───────────────────────────────────┘
                                     │
                                     ▼
┌────────────────────────────────────────────────────────────────────────┐
│                        CORE BANKING SERVICE LAYER                      │
│  ┌──────────────────────────────────────────────────────────────────┐  │
│  │                      BankingStore (Singleton)                    │  │
│  │  - Observable Event Bus (Subscriber Notification)                │  │
│  │  - Double-Entry Ledger Engine                                    │  │
│  │  - AML Rule Evaluator & Anomaly Scorer                           │  │
│  │  - Transfer Daily Limit Validator & Cooling Period Enforcer      │  │
│  │  - OTP Generator & Hash Verifier                                 │  │
│  └─────────────────────────────────┬────────────────────────────────┘  │
└────────────────────────────────────┼───────────────────────────────────┘
                                     │
                                     ▼
┌────────────────────────────────────────────────────────────────────────┐
│                      PERSISTENCE & STORAGE LAYER                       │
│  ┌───────────────────────────┬──────────────────────────────────────┐  │
│  │   LocalStorage (Engine)   │      In-Memory Seed State Backup     │  │
│  │  - Accounts, Users, Txns  │     - 3 Multi-Role Demo Profiles     │  │
│  │  - Cards, Loans, Audits   │     - Simulated BBPS Biller Database │  │
│  └───────────────────────────┴──────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────────────┘
```

### Component Hierarchy
```
src/
├── App.tsx                    # Main App Controller, Hotkey & Navigation Engine
├── main.tsx                   # React 19 Root Hydration
├── types.ts                   # Domain Types & RBAC Definitions
├── data/
│   └── mockData.ts            # Seed accounts, users, billers, and audit logs
├── services/
│   └── bankingStore.ts        # Singleton Banking Engine & Ledger Transaction Core
└── components/
    ├── accounts/              # Account details, balance breakdown, statements
    ├── admin/                 # Admin console, AML risk queue, audit logs
    ├── auth/                  # Login, registration, password reset, 1-click roles
    ├── beneficiaries/         # Beneficiary management with cooling period
    ├── bills/                 # Utility payments & BBPS simulated billers
    ├── cards/                 # Debit/Credit card management, limits, lock/unlock
    ├── common/                # Header, Sidebar, NavigationBar, OtpModal, ReceiptModal
    ├── dashboard/             # Customer dashboard & financial analytics
    ├── employee/              # Teller desk, loan approval, KYC verification
    ├── profile/               # Customer profile, avatar selector, security settings
    ├── statements/            # Verified printable statement generator
    ├── support/               # Service requests, ticket creator & SLA tracker
    ├── transactions/          # Transaction history with filters & search
    └── transfers/             # Money transfer wizard with OTP authorization
```

---

## 6. Database & Data Models

The system model mirrors an industrial relational schema with foreign key relationships, balance constraints, and immutable audit logs.

```
       ┌──────────────────────┐
       │         USER         │
       ├──────────────────────┤
       │ id (PK)              │
       │ email                │
       │ role (ENUM)          │
       │ kycTier              │
       │ twoFactorEnabled     │
       └──────────┬───────────┘
                  │ 1
                  │
                  ├────────────────────────────┬────────────────────────────┐
                  │ 1..*                       │ 1..*                       │ 1..*
                  ▼                            ▼                            ▼
       ┌──────────────────────┐     ┌──────────────────────┐     ┌──────────────────────┐
       │     BANK_ACCOUNT     │     │     BENEFICIARY      │     │      BANK_CARD       │
       ├──────────────────────┤     ├──────────────────────┤     ├──────────────────────┤
       │ id (PK)              │     │ id (PK)              │     │ id (PK)              │
       │ userId (FK)          │     │ userId (FK)          │     │ userId (FK)          │
       │ accountNumber (UQ)   │     │ accountNumber        │     │ accountId (FK)       │
       │ accountType          │     │ ifsc                 │     │ cardNumber (Masked)  │
       │ currentBalance       │     │ status (COOLING/ACT) │     │ isLocked             │
       │ dailyLimit           │     │ coolingEndsAt        │     │ dailyLimit           │
       └──────────┬───────────┘     └──────────────────────┘     └──────────────────────┘
                  │ 1
                  │
                  ├────────────────────────────┬────────────────────────────┐
                  │ 1..*                       │ 1..*                       │ 1..*
                  ▼                            ▼                            ▼
       ┌──────────────────────┐     ┌──────────────────────┐     ┌──────────────────────┐
       │     TRANSACTION      │     │     BILL_PAYMENT     │     │    SERVICE_REQUEST   │
       ├──────────────────────┤     ├──────────────────────┤     ├──────────────────────┤
       │ id (PK)              │     │ id (PK)              │     │ id (PK)              │
       │ accountId (FK)       │     │ accountId (FK)       │     │ userId (FK)          │
       │ amount               │     │ billerCategory       │     │ ticketNumber         │
       │ flow (DEBIT/CREDIT)  │     │ billerName           │     │ status (OPEN/RESOLV) │
       │ isSuspicious (AML)   │     │ referenceId          │     │ priority             │
       └──────────────────────┘     └──────────────────────┘     └──────────────────────┘
```

---

## 7. API & Service Layer

The internal banking service (`bankingStore`) exposes a synchronous, transactional interface that adheres to atomic ledger operations.

### Core Banking Methods

#### 1. Authentication & IAM
| Method | Parameters | Return | Description |
|---|---|---|---|
| `login(email, role?)` | `email: string, role?: UserRole` | `{ success: boolean, user?: User }` | Authenticates user or provisions session |
| `logout()` | None | `void` | Clears active session |
| `switchRole(role)` | `role: UserRole` | `User` | Fast persona switcher for testing |
| `updateUser(userId, updates)` | `userId: string, updates: Partial<User>` | `User` | Updates contact/security profile |

#### 2. Transactions & Transfers
| Method | Parameters | Return | Description |
|---|---|---|---|
| `processTransfer(payload)` | `{ fromAccountId, toAccountNumber, amount, remarks, ... }` | `{ success: boolean, transaction?: Transaction, message?: string }` | Executes atomic debit/credit transfer |
| `getTransactions(userId?, accountId?)` | `userId?: string, accountId?: string` | `Transaction[]` | Retrieves filtered transaction history |
| `flagSuspiciousTransaction(txnId, reason)` | `txnId: string, reason: string` | `void` | Flags transaction for AML compliance review |

#### 3. Beneficiary Management
| Method | Parameters | Return | Description |
|---|---|---|---|
| `addBeneficiary(beneficiary)` | `Omit<Beneficiary, 'id' \| 'createdAt'>` | `Beneficiary` | Registers beneficiary with 30m cooling period |
| `deleteBeneficiary(id)` | `id: string` | `boolean` | Removes beneficiary record |

#### 4. Card & Account Security
| Method | Parameters | Return | Description |
|---|---|---|---|
| `toggleCardLock(cardId)` | `cardId: string` | `BankCard` | Locks/unlocks card instantly |
| `updateCardLimits(cardId, limits)` | `cardId: string, limits: object` | `BankCard` | Updates POS/ATM spending limits |
| `toggleCardFeature(cardId, feature)` | `cardId: string, feature: string` | `BankCard` | Toggles contactless/online usage |

---

## 8. Setup & Installation

### Prerequisites
- **Node.js**: `v18.0.0` or higher
- **npm**: `v9.0.0` or higher

### 1. Clone the Repository
```bash
git clone https://github.com/heshmasree2809/SmartBank-Online-Banking-Portal-spark.git
cd SmartBank-Online-Banking-Portal-spark
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Start Development Server
```bash
npm run dev
```
The application will launch on `http://localhost:3000`.

### 4. Build for Production
```bash
npm run build
```
Generates production-optimized static assets in the `dist/` directory.

### 5. Run Type Checks & Linting
```bash
npm run lint
```

---

## 9. Security & Compliance

```
                                  SECURITY MATRIX
┌─────────────────────────┬────────────────────────────────────────────────────────┐
│ Security Control        │ Implementation Mechanism                               │
├─────────────────────────┼────────────────────────────────────────────────────────┤
│ Role-Based Access (RBAC)│ Protected route guards & UI view-level authorization   │
│ Transfer Verification   │ Multi-factor 6-digit OTP challenge modal               │
│ Velocity Limiting       │ Per-account daily transfer quotas with live tracking   │
│ Fraud & AML Detection   │ Heuristic scoring engine detecting anomaly spikes      │
│ Data Masking            │ Account numbers & card numbers masked with SHA hashes  │
│ Cooling Period Policy   │ 30-minute lockdown on newly registered payees          │
│ Tamper-Evident Auditing │ Structured audit log trail tracking every state change │
│ XSS & Injection Defense │ Sanitized input bindings & strict TypeScript typings   │
└─────────────────────────┴────────────────────────────────────────────────────────┘
```

---

## 10. Engineering Challenges & Solutions

### 1. Atomic Multi-Account State Consistency
- **Challenge**: Guaranteeing that transferring funds simultaneously debits the sender and credits the receiver without partial failures or race conditions.
- **Solution**: Implemented an atomic transaction execution routine inside `bankingStore.processTransfer()`. Both balance mutations and ledger records are created synchronously before notifying reactive listeners.

### 2. Backward Navigation Stack in Single-Page View Architectures
- **Challenge**: Standard browser back buttons can desynchronize internal view state in complex component-routed dashboards.
- **Solution**: Developed a custom `viewHistory` stack with hierarchical breadcrumb resolution and keyboard shortcuts (`Alt + ←`), allowing smooth historical traversal across all customer, teller, and admin modules.

### 3. Real-Time AML Fraud Rule Engine
- **Challenge**: Flagging high-risk transactions without inducing client latency or complex backend orchestration.
- **Solution**: Created a modular rule pipeline assessing transaction velocity, threshold breaches (single transactions > ₹1,00,000), and rapid consecutive transfers, automatically tagging transactions with `isSuspicious` flags for administrative review.

---

## 11. Future Enhancements

- [ ] **WebAuthn / FIDO2 Biometric Login**: Direct biometric fingerprint and FaceID authorization using Web Authentication APIs.
- [ ] **AI-Powered Financial Insights**: Personalized budget forecasting, subscription leak detection, and cash-flow predictions.
- [ ] **Multi-Currency Forex Wallets**: Support for holding, converting, and sending USD, EUR, GBP, and SGD balances.
- [ ] **Push Notification WebSockets**: Real-time push alerts for inbound credits, card swipes, and security logins.
- [ ] **Open Banking / Account Aggregator Integration**: Unified dashboard aggregating external accounts via RBI Account Aggregator framework.

---

## 12. Author

**Avuthu Heshma Sree**
- **Role**: Lead Full-Stack Software Engineer
- **Email**: [avuthuheshmasree@gmail.com](mailto:avuthuheshmasree@gmail.com)
- **GitHub**: [heshmasree2809](https://github.com/heshmasree2809)
- **Repository**: [SmartBank-Online-Banking-Portal-spark](https://github.com/heshmasree2809/SmartBank-Online-Banking-Portal-spark)
- **Project**: SmartBank Enterprise Online Banking Management Portal
- **Date**: August 2026

---
*SmartBank — Enterprise Core Banking Architecture.*
