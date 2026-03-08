These are the prompts used to integrate the mortgage-lending app into the

bank-backend project in a professional, refined format.



--------------------------------------------------------------------------------

PROMPT 1 — Sidebar Layout Integration (Bypass Dashboard Header)

--------------------------------------------------------------------------------



I have two Next.js projects:

\- "bank-backend" — the main banking app with a DashboardHeader on all employee pages

\- "mortgage-lending" — a standalone mortgage app with its own left sidebar navigation



I want to embed the mortgage-lending experience fully inside bank-backend,

preserving the sidebar layout from the mortgage app, but WITHOUT showing

the bank-backend DashboardHeader on those pages.



The mortgage section lives under /employee/real-estate and includes:

&nbsp; - Brokerage Leads  (/employee/real-estate)

&nbsp; - Loan Leads       (/employee/real-estate/loan-leads)



Tasks:

1\. Create a new (mortgage) route group in the Next.js app directory that uses

&nbsp;  a bare layout (auth check only, no DashboardHeader).

2\. Port the BrokerageSidebarLayout and SidebarLayout components from the

&nbsp;  mortgage-lending project into bank-backend under components/mortgage/.

&nbsp;  Adapt all internal links to use bank-backend URLs.

3\. Create the brokerage leads page and loan leads page inside (mortgage),

&nbsp;  wrapping them with the appropriate sidebar layout.

4\. Delete the old (dashboard)/employee/real-estate pages to avoid duplicate

&nbsp;  route conflicts.

5\. Both sidebars should have cross-navigation links so users can switch

&nbsp;  between Brokerage Leads and Loan Leads.



The auth check in the (mortgage)/layout.tsx should mirror the existing

(dashboard)/layout.tsx — redirect to /auth/sign-in if no session.





--------------------------------------------------------------------------------

PROMPT 2 — Enable Customers, Renewals, Campaigns \& Knowledge Base Sections

--------------------------------------------------------------------------------



In the SidebarLayout (used on Loan Leads and related pages), the following

nav items are currently disabled (greyed out) because the pages and API

routes don't exist yet:

&nbsp; - Customers

&nbsp; - Renewals

&nbsp; - Campaigns

&nbsp; - Knowledge Base



Please do the following:



1\. Create the four page files under app/(mortgage)/employee/real-estate/:

&nbsp;  - customers/page.tsx     — searchable customer list with mortgage count

&nbsp;  - renewals/page.tsx      — mortgage renewal queue with 30/60/90/all day filters

&nbsp;  - campaigns/page.tsx     — two tabs: "Run Campaign" and "Campaign History"

&nbsp;  - knowledge/page.tsx     — document upload (drag-and-drop) + AI semantic search stub



&nbsp;  All pages should be wrapped in <SidebarLayout> and call their respective

&nbsp;  API routes. Use the same clean card/table UI style as the existing pages.



2\. Create the corresponding API routes under app/api/mortgage/:



&nbsp;  GET  /api/mortgage/customers

&nbsp;       → mortgagePrisma.customer.findMany({ include: { mortgages: true } })



&nbsp;  GET  /api/mortgage/renewals?days=N

&nbsp;       → mortgagePrisma.mortgage.findMany where maturityDate <= now + N days

&nbsp;       → return: { id, customerId, customerName, customerEmail,

&nbsp;                   amount (loanAmount), renewalDate (maturityDate),

&nbsp;                   status, daysUntilRenewal }



&nbsp;  POST /api/mortgage/campaigns/run

&nbsp;       → Find or create a Campaign record (type: RENEWAL, isActive: true)

&nbsp;       → Find all ACTIVE mortgages with maturityDate within 90 days

&nbsp;       → Create a CampaignLog for each (campaignId is required)

&nbsp;       → Simulate email send (mark SUCCESS if customer.email exists)

&nbsp;       → return: { success, message, emailsSent, emailsFailed, logs\[] }



&nbsp;  GET  /api/mortgage/campaigns/logs

&nbsp;       → mortgagePrisma.campaignLog.findMany with customer + mortgage included

&nbsp;       → return flat array: { id, customerName, customerEmail,

&nbsp;                              mortgageAmount, daysUntilRenewal,

&nbsp;                              emailStatus, createdAt }



&nbsp;  GET  /api/mortgage/knowledge/list

&nbsp;       → mortgagePrisma.document.findMany with \_count chunks

&nbsp;       → return: { files: \[{ name, size, uploadedAt, chunks }] }



&nbsp;  POST /api/mortgage/knowledge/upload  (multipart form)

&nbsp;       → request.formData(), loop files, upsert Document by filename



&nbsp;  POST /api/mortgage/knowledge/query   { query: string }

&nbsp;       → Stub response (vector search not yet configured)



&nbsp;  DELETE /api/mortgage/knowledge/delete  { filename: string }

&nbsp;       → mortgagePrisma.document.delete({ where: { filename } })



3\. Enable all disabled nav items in SidebarLayout (remove disabled: true).

&nbsp;  Also fix the Customers href from /employee/customers

&nbsp;  to /employee/real-estate/customers.



Prisma schema notes (mortgage.prisma):

&nbsp; - Mortgage fields: loanAmount (not amount), maturityDate (not renewalDate)

&nbsp; - CampaignLog.campaignId is required — must find/create Campaign first

&nbsp; - mortgagePrisma is imported from @/lib/mortgage-prisma





--------------------------------------------------------------------------------

PROMPT 3 — Vercel Build Fix: Generate Mortgage Prisma Client

--------------------------------------------------------------------------------



The Vercel build is failing with:

&nbsp; "Module not found: Can't resolve '@/generated/mortgage-prisma'"



This is because the build script only runs `prisma generate` for the default

schema.prisma. The mortgage schema lives at prisma/mortgage.prisma and

generates its client to prisma/generated/mortgage-prisma.



Fix: Update the build script in package.json to generate both clients:



&nbsp; "build": "prisma generate \&\& prisma generate --schema=prisma/mortgage.prisma \&\& next build"





--------------------------------------------------------------------------------

PROMPT 4 — Vercel Build Fix: Lazy PrismaClient Initialization

--------------------------------------------------------------------------------



After fixing the generate step, the build still fails with:

&nbsp; "PrismaClientConstructorValidationError: Invalid value undefined for

&nbsp;  datasource 'db' provided to PrismaClient constructor."



This happens because lib/mortgage-prisma.ts calls `new PrismaClient()` at

module evaluation time. During Vercel's static page data collection phase,

MORTGAGE\_DATABASE\_URL is not available in the build environment, so the

constructor throws immediately.



Fix: Replace the direct instantiation with a lazy Proxy pattern so the

PrismaClient is only created when the first actual database method is called

(not when the module is imported):



&nbsp; export const mortgagePrisma = new Proxy({} as PrismaClient, {

&nbsp;   get(\_target, prop: string | symbol) {

&nbsp;     const client = getClient()   // getClient() checks env var + creates instance

&nbsp;     const val = (client as any)\[prop]

&nbsp;     return typeof val === 'function' ? val.bind(client) : val

&nbsp;   },

&nbsp; })



Also add MORTGAGE\_DATABASE\_URL as an environment variable in Vercel:

&nbsp; Settings → Environment Variables → Add New

&nbsp; Key:   MORTGAGE\_DATABASE\_URL

&nbsp; Value: <postgresql connection string for the mortgage Neon database>

&nbsp; Environments: ✅ Production  ✅ Preview  ✅ Development





--------------------------------------------------------------------------------

PROMPT 5 — Fix Brokerage AI Copilot (Inaccurate / Hallucinated Answers)

--------------------------------------------------------------------------------



The Brokerage AI Assistant chatbot is giving wrong answers:

&nbsp; - When asked to name all renters, it responds with "LEAD 1, LEAD 2..."

&nbsp;   instead of the real customer names.

&nbsp; - When asked about property types, it only mentions Single Family

&nbsp;   even though other types (Condo, Townhouse, etc.) exist.



Root cause:

&nbsp; The API only passes a sample of 15 most-recent leads to the AI, ordered

&nbsp; by createdAt descending. The 4 rent leads are not in that window, so the AI

&nbsp; has no names for them and hallucinates. Property type is also never included

&nbsp; in the stats object sent to the model.



Fix the /api/mortgage/brokerage-copilot route:



1\. Fetch ALL active (non-archived) leads with full field details.



2\. Build and pass the following to the AI system prompt:

&nbsp;  - Overall counts: total, BUY, SELL, RENT, contingent

&nbsp;  - Property type breakdown (all types with counts)

&nbsp;  - Stage breakdown (all stages with counts)

&nbsp;  - Top 10 cities by lead count

&nbsp;  - ALL rent leads with full details (names, city, stage, rent amount)

&nbsp;  - Up to 50 sell leads

&nbsp;  - Top 30 buy leads by date (mention total BUY count separately)



3\. Lower temperature from 0.7 to 0.3 to reduce hallucination.



4\. Add explicit system instruction:

&nbsp;  "Only use the data provided above to answer questions.

&nbsp;   Never fabricate or guess names or numbers not present in the dataset."





================================================================================

&nbsp; END OF PROMPTS

================================================================================



