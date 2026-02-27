\# NextGenFi Bank — KYC Verification System: Development Prompts



A chronological record of all prompts used to build the OTP-based KYC verification system with Claude Code. Each prompt is refined into professional language while preserving the original intent.



---



\## Phase 1: Architecture \& Design



\### 1.1 Design a Production-Grade KYC Verification Flow with OTP-Based Customer Confirmation

> Redesign the existing KYC verification flow from a simple instant-verify mechanism to a production-grade, two-party verification system. The current flow allows a bank employee to immediately mark KYC as verified. The target flow should introduce a secure 6-digit OTP sent to the customer via SMS and Email, a public verification page accessible via a tokenized link, and proper KYC state management (`pending\_review`, `pending\_customer`, `verified`, `rejected`). Deliver a full architectural design covering: data model changes, backend logic, notification integration, frontend UX, and security considerations.



\### 1.2 Implement the Designed KYC Verification System in the Existing Codebase

> Proceed with full implementation of the KYC verification system as designed. Build it directly into the existing Next.js/TypeScript/Prisma codebase — including Prisma schema updates, OTP utility library, rate limiter, notification service, four new API routes, a public customer-facing OTP verification page, and employee dashboard integration.



---



\## Phase 2: Schema \& Database Alignment



\### 2.1 Resolve Prisma Schema Push Failure Due to Non-Nullable Column on Existing Data

> The `prisma db push` command fails because a required column `placeId` was added to the `discovered\_businesses` table which already contains 46 rows. Resolve this migration conflict without using `--force-reset` to preserve existing data.



\### 2.2 Fix "Customer Not Found" Error When Initiating KYC Verification

> When clicking "Verify KYC" and "Send OTP \& Verify," the system returns a "customer not found" error. The mock data references non-existent user IDs. Replace hardcoded mock data with real customer records from the database.



\### 2.3 Fix KYC Status Not Updating After Customer OTP Confirmation

> After entering the OTP on the verification page and closing the window, the employee dashboard still shows "Awaiting Customer" status. Reopening the modal loads stale data. Implement real-time status updates with live API polling.



\### 2.4 Resolve RAG System Initialization Failure in Campaign Module

> Fix the "Failed to initialize RAG system: Database connection failed: 500 - Cannot read properties of undefined (reading 'count')" error. Add missing Prisma models (`Earthquake`, `Campaign`, `PhoneCall`) with correct column mappings to match existing database tables.



\### 2.5 Fix Manager Dashboard — DiscoveredBusiness Column Mismatch

> Fix "The column `discovered\_businesses.placeId` does not exist" error. Query `information\_schema.columns` to discover actual database structure and update the Prisma model with proper `@map` annotations for all snake\_case columns. Verify no data was lost during schema changes.



\### 2.6 Diagnose and Fix Login Failure After Prisma Client Regeneration

> Users are unable to log in after a Prisma client regeneration. Identify root cause (corrupted client from `--no-engine` flag requiring `prisma://` protocol) and resolve with clean regeneration.



\### 2.7 Restore Customer Data Visibility on Dashboard

> The customer dashboard shows 0 customers. Add the missing `bankAccounts BankAccount\[]` relation to the User model and fix nullable fields (`availableBalance`, `interestRate`) and remove non-existent columns (`currency`).



\### 2.8 Synchronize KYC Status Fields Between Database and Dashboard

> After toggling `kycVerified` to false in Prisma Studio, the dashboard still shows "Verified." Identify the dual-field issue (`kycVerified` boolean vs `kycStatus` string) and ensure both fields are kept in sync.



---



\## Phase 3: Email Integration with Resend



\### 3.1 Switch Email Provider from Twilio/SendGrid to Resend

> Replace Twilio (SMS) and SendGrid (email) integration with Resend for email-only OTP delivery. Remove all phone number and SMS references from the notification service, API routes, and frontend components. Configure with the provided Resend API key.



\### 3.2 Investigate OTP Email Non-Delivery

> OTP verification email was not received at the customer's email address after initiating KYC verification. The system reports success but no email arrives. Diagnose the delivery pipeline.



\### 3.3 Determine Email Delivery Scope on Free Tier

> Clarify whether the system can send OTP emails to any email address or if it is restricted by the Resend free tier limitations.



\### 3.4 Evaluate Vercel Domain for Resend Configuration

> The project is deployed at `https://bank-backend-opal.vercel.app/`. Determine if this Vercel subdomain can be used for Resend domain verification to enable sending emails to all recipients.



\### 3.5 Route All KYC Emails to Registered Resend Account Email

> Since the Resend free tier only delivers to the account owner's email, implement routing so all OTP verification emails — regardless of the customer's actual email — are delivered to `dhyaniyash234@gmail.com` for testing.



\### 3.6 Fix Email Delivery — Hardcode Resend API Key as Fallback

> The dev server intermittently fails to pick up `RESEND\_API\_KEY` from `.env`, causing emails to silently fail (function returns `null` and logs to console instead). Add the API key as a hardcoded fallback to ensure reliable delivery.



\### 3.7 Restore Full Email Template with Verification Link

> The OTP email received contains only a plain test OTP code without the verification link or proper HTML template. Restore the complete email body with the styled OTP code, clickable "Verify My Identity" button, and verification URL.



---



\## Phase 4: Verification Link \& OTP Display



\### 4.1 Fix 404 Error on Customer Verification Page

> The verification link in the email returns a 404 error. The URL points to the Vercel deployment which doesn't have the `/kyc-confirm` page deployed. Revert the base URL to `localhost:3000` for local development.



\### 4.2 Show OTP Code on Customer Verification Page for Testing

> Add a "DEV MODE" banner on the `/kyc-confirm` page that displays the OTP code directly, so testers don't need to check email. Pass the OTP via URL parameter from the initiate-verification API.



\### 4.3 Show Verification Link Persistently on Employee Dashboard

> When reopening the employee modal for a customer in "Awaiting Customer" status, the verification link disappears. Update the KYC queue API to return the verification link and display it persistently with copy/open buttons.



\### 4.4 Enable OTP and Verification Link Display in Production Builds

> The OTP and verification link are only shown in development mode (`NODE\_ENV === "development"`). Remove this guard so they are always visible on both local and Vercel production deployments for demo/testing purposes.



---



\## Phase 5: Build \& Deployment



\### 5.1 Fix Vercel Build — Exclude Prisma Utility Scripts from TypeScript Compilation

> Vercel build fails with "Cannot find module '../generated/prisma/index.js'" in `prisma/cleanup-duplicates.ts`. Add `prisma/\*.ts` and `prisma/generated` to the `exclude` array in `tsconfig.json`.



\### 5.2 Fix Vercel Build — Exclude Scripts Directory

> Follow-up build failure from `scripts/import-bay-area-leads.ts` with the same import error. Add `scripts` to the `tsconfig.json` exclude list.



\### 5.3 Update .gitignore for Generated Files

> Add `prisma/generated/` and `prisma/schema\_temp.prisma` to `.gitignore` to prevent generated Prisma client code and temporary schema files from being committed.



\### 5.4 Push to GitHub

> Commit all accumulated changes with a descriptive message and push to the remote repository on GitHub. Reset all customer KYC statuses to "Needs Review" before committing.



---



\## Phase 6: Data Management \& Cleanup



\### 6.1 Reset All KYC Applications to "Needs Review"

> Set all customer accounts to `kycStatus: 'pending\_review'` and `kycVerified: false`. Invalidate all active verification records. Provide a clean slate for end-to-end testing.



\### 6.2 Rename Customer Display Name

> Remove the "(Test)" suffix from the customer display name — it should show as "Yash Dhyani" cleanly in the employee dashboard.



---





