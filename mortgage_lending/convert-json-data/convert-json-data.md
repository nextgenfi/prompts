You are helping me take UI mock data and convert it into a real backend JSON API response format that connects to the actual database.



Your tasks:

1️⃣ Convert any displayed UI data into a structured JSON API response format.

2️⃣ Do NOT use hardcoded static values — fetch data dynamically from PostgreSQL through Prisma.

3️⃣ Include relevant fields such as:

&nbsp;  - user -> account -> budget -> transactions -> bills summary

&nbsp;  - payment method details: type, description, and masked card/wallet info

4️⃣ Ensure the JSON follows consistent key naming and formatting:

&nbsp;  - camelCase keys

&nbsp;  - Include nested fields like paymentMethodDescription, merchantCategory, timestamps, etc.

5️⃣ When needed, modify the Prisma queries and add relationships if missing.

6️⃣ Validate that the JSON matches real DB columns and relations — no fake fields.

7️⃣ If UI shows masked data (e.g. \*\*\*\*7262), derive mask using the real account number.



Output Format:

\- Clean JSON output

\- No comments inside the JSON

\- Then below JSON include explanation of DB queries used

\- If schema changes needed, generate migration code



Whenever I give sample UI data screenshots or text:

▶️ Convert it into the dynamic API JSON format  

▶️ Implement server-side changes for `/api/...` endpoints  

▶️ Update frontend fetch code if needed







📋 Overview

This prompt helps convert UI mock/hardcoded data into a real backend JSON API response format that connects to an actual PostgreSQL database via Prisma ORM.



🎯 Objectives

Convert any displayed UI data into a structured, database-driven API response that:



Fetches data dynamically from PostgreSQL

Follows consistent JSON formatting standards

Includes complete relational data

Maintains data integrity and type safety



📝 Conversion Tasks

1️⃣ Convert UI Data to Structured JSON API Format

Transform any mock or hardcoded UI data into a proper API response structure.

Example:

UI Mock Data:

typescriptconst mockTransactions = \[

&nbsp; { name: "Starbucks", amount: "$6.75" }

]

Converted API Response:

json{

&nbsp; "success": true,

&nbsp; "transactions": \[

&nbsp;   {

&nbsp;     "id": "txn\_001",

&nbsp;     "description": "Starbucks",

&nbsp;     "amount": -6.75,

&nbsp;     "date": "2025-11-26T19:22:33Z",

&nbsp;     "paymentMethod": "mobile\_payment",

&nbsp;     "paymentMethodDescription": "Paid using Mobile Payment (Apple Pay)",

&nbsp;     "paymentDetails": "Apple Pay - Visa •••• 7262"

&nbsp;   }

&nbsp; ]

}



2️⃣ Use Dynamic Database Queries (NO Hardcoded Values)

❌ Don't Do This:

typescriptconst balance = 12847.65 // Hardcoded

✅ Do This:

typescriptconst account = await prisma.bankAccount.findFirst({

&nbsp; where: { userId }

})

const balance = account.balance // From database



3️⃣ Include Comprehensive Field Structure

Every API response must include relevant nested data:

json{

&nbsp; "user": {

&nbsp;   "id": "user\_123",

&nbsp;   "name": "John Doe",

&nbsp;   "email": "john@example.com"

&nbsp; },

&nbsp; "accounts": \[

&nbsp;   {

&nbsp;     "id": "acc\_001",

&nbsp;     "accountNumber": "\*\*\*\*4532",

&nbsp;     "accountType": "checking",

&nbsp;     "balance": 12847.65,

&nbsp;     "currency": "USD"

&nbsp;   }

&nbsp; ],

&nbsp; "transactions": \[

&nbsp;   {

&nbsp;     "id": "txn\_001",

&nbsp;     "date": "2025-11-28T14:32:15Z",

&nbsp;     "description": "Whole Foods Market",

&nbsp;     "amount": -87.43,

&nbsp;     "partyFrom": "John Doe - Checking \*\*\*\*4532",

&nbsp;     "partyTo": "Whole Foods Market #234",

&nbsp;     "paymentMethod": "debit\_card",

&nbsp;     "paymentMethodDescription": "Paid using Debit Card",

&nbsp;     "paymentDetails": "Visa Debit •••• 4532",

&nbsp;     "merchantCategory": "Grocery Stores",

&nbsp;     "status": "completed"

&nbsp;   }

&nbsp; ],

&nbsp; "bills": \[

&nbsp;   {

&nbsp;     "id": "bill\_001",

&nbsp;     "billName": "Electric Bill",

&nbsp;     "company": "City Power \& Light",

&nbsp;     "dueDate": "2025-12-05",

&nbsp;     "amount": 156.42,

&nbsp;     "autopay": true

&nbsp;   }

&nbsp; ],

&nbsp; "summary": {

&nbsp;   "totalIncome": 5500.00,

&nbsp;   "totalExpenses": 2166.80,

&nbsp;   "netCashFlow": 3333.20

&nbsp; }

}



4️⃣ JSON Formatting Standards

Required Standards:



✅ Use camelCase for all keys

✅ Include timestamps in ISO 8601 format

✅ Use nested objects for related data

✅ Include payment method descriptions for transactions

✅ Add metadata fields (status, category, location)

✅ Use consistent currency formatting (numbers, not strings)



Key Naming Convention:

json{

&nbsp; "paymentMethod": "debit\_card",

&nbsp; "paymentMethodDescription": "Paid using Debit Card",

&nbsp; "paymentDetails": "Visa Debit •••• 4532",

&nbsp; "merchantCategory": "Grocery Stores",

&nbsp; "createdAt": "2025-11-28T14:32:15Z",

&nbsp; "updatedAt": "2025-11-28T14:32:15Z"

}



5️⃣ Prisma Relationships \& Schema Updates

Check Existing Relationships:

prismamodel User {

&nbsp; id           String        @id

&nbsp; bankAccounts BankAccount\[]

&nbsp; transactions Transaction\[]

&nbsp; bills        Bill\[]

}



model BankAccount {

&nbsp; id           String        @id

&nbsp; userId       String

&nbsp; user         User          @relation(fields: \[userId], references: \[id])

&nbsp; transactions Transaction\[]

}



model Transaction {

&nbsp; id                       String      @id

&nbsp; userId                   String

&nbsp; accountId                String

&nbsp; paymentMethodDescription String      // Ensure this exists

&nbsp; user                     User        @relation(fields: \[userId], references: \[id])

&nbsp; account                  BankAccount @relation(fields: \[accountId], references: \[id])

}

If Missing, Add Migration:

bashnpx prisma migrate dev --name add\_payment\_method\_description



6️⃣ Validate Against Real Database Schema

Before Creating API Response:



Check Prisma schema for field names

Verify relationships exist

Ensure data types match

Test queries return expected data



Validation Checklist:



✅ All fields exist in database

✅ Relationships are properly defined

✅ Data types are correct (String, Int, Float, DateTime)

✅ No fake/placeholder fields





7️⃣ Implement Data Masking

For sensitive data like account numbers:

Database Value: "\*\*\*\*7262"

API Response: "\*\*\*\*7262"

Dynamic Masking:

typescriptconst accountNumber = account.accountNumber // "\*\*\*\*7262"

const lastFour = accountNumber.slice(-4)    // "7262"

const paymentDetails = `Visa Debit •••• ${lastFour}`



📤 Output Format

1\. Clean JSON Response

json{

&nbsp; "success": true,

&nbsp; "data": {

&nbsp;   "accountBalance": 12847.65,

&nbsp;   "transactions": \[]

&nbsp; }

}

2\. Database Query Explanation

typescript// Prisma Query Used:

const account = await prisma.bankAccount.findFirst({

&nbsp; where: { 

&nbsp;   userId: session.user.id,

&nbsp;   accountType: 'checking'

&nbsp; }

})



const transactions = await prisma.transaction.findMany({

&nbsp; where: { userId: session.user.id },

&nbsp; orderBy: { date: 'desc' },

&nbsp; take: 30,

&nbsp; include: {

&nbsp;   account: true

&nbsp; }

})

3\. Schema Changes (If Needed)

prisma// Add to schema.prisma:

model Transaction {

&nbsp; // ... existing fields

&nbsp; paymentMethodDescription String  // NEW FIELD

}

Generate Migration:

bashnpx prisma migrate dev --name add\_payment\_method\_description



🔄 Conversion Workflow

When providing UI data (screenshots or text), the assistant will:

Step 1: Analyze UI Data



Identify all data fields shown

Determine data structure

List relationships needed



Step 2: Design JSON API Response



Create structured JSON format

Add all required fields

Include nested relationships



Step 3: Implement Server-Side Changes

Create/Update API Endpoint:

typescript// app/api/customer/financial-overview/route.ts

export async function GET() {

&nbsp; const session = await auth.api.getSession()

&nbsp; 

&nbsp; const accounts = await prisma.bankAccount.findMany({

&nbsp;   where: { userId: session.user.id }

&nbsp; })

&nbsp; 

&nbsp; const transactions = await prisma.transaction.findMany({

&nbsp;   where: { userId: session.user.id },

&nbsp;   orderBy: { date: 'desc' },

&nbsp;   take: 30

&nbsp; })

&nbsp; 

&nbsp; return NextResponse.json({

&nbsp;   accountBalance: {

&nbsp;     checking: accounts.find(a => a.accountType === 'checking'),

&nbsp;     savings: accounts.find(a => a.accountType === 'savings'),

&nbsp;     totalBalance: accounts.reduce((sum, a) => sum + a.balance, 0)

&nbsp;   },

&nbsp;   recentTransactions: transactions.map(txn => ({

&nbsp;     id: txn.id,

&nbsp;     date: txn.date.toISOString(),

&nbsp;     description: txn.description,

&nbsp;     amount: txn.amount,

&nbsp;     paymentMethodDescription: txn.paymentMethodDescription,

&nbsp;     // ... all fields

&nbsp;   }))

&nbsp; })

}

Step 4: Update Frontend Fetch Code

typescript// components/customer-dashboard.tsx

useEffect(() => {

&nbsp; const fetchData = async () => {

&nbsp;   const response = await fetch('/api/customer/financial-overview')

&nbsp;   const data = await response.json()

&nbsp;   setFinancialData(data)

&nbsp; }

&nbsp; fetchData()

}, \[])

```



\### \*\*Step 5: Test \& Validate\*\*

\- Test API endpoint returns correct data

\- Verify UI displays data properly

\- Check all fields are populated



---



\## 🏗️ \*\*Project Structure\*\*

```

bank-backend/

├── app/

│   └── api/

│       ├── customer/

│       │   └── financial-overview/

│       │       └── route.ts          # Customer API

│       └── employee/

│           └── customers/

│               └── route.ts          # Employee API

├── components/

│   ├── customer-dashboard.tsx        # Customer UI

│   └── employee-dashboard.tsx        # Employee UI

├── lib/

│   ├── prisma.ts                     # Prisma client

│   └── data-access/

│       └── customer-data-layer.ts    # Data access layer

├── prisma/

│   ├── schema.prisma                 # Database schema

│   └── seed.ts                       # Seed script

└── .env                              # Database URL

```



---



\## 🧪 \*\*Testing Checklist\*\*



Before marking conversion complete:



\- \[ ] API returns valid JSON

\- \[ ] All data fetched from database (no hardcoded values)

\- \[ ] Timestamps in ISO 8601 format

\- \[ ] Nested relationships included

\- \[ ] Payment method descriptions present

\- \[ ] Currency values are numbers, not strings

\- \[ ] Account numbers properly masked

\- \[ ] Frontend successfully fetches and displays data

\- \[ ] No console errors

\- \[ ] Works in both development and production



---



\## 📚 \*\*Examples\*\*



\### \*\*Example 1: Transaction List\*\*



\*\*UI Shows:\*\*

```

Starbucks - $6.75

Whole Foods - $87.43

API Returns:

json{

&nbsp; "transactions": \[

&nbsp;   {

&nbsp;     "id": "txn\_001",

&nbsp;     "date": "2025-11-26T19:22:33Z",

&nbsp;     "description": "Starbucks",

&nbsp;     "amount": -6.75,

&nbsp;     "paymentMethod": "mobile\_payment",

&nbsp;     "paymentMethodDescription": "Paid using Mobile Payment (Apple Pay)",

&nbsp;     "status": "completed"

&nbsp;   },

&nbsp;   {

&nbsp;     "id": "txn\_002",

&nbsp;     "date": "2025-11-28T14:32:15Z",

&nbsp;     "description": "Whole Foods Market",

&nbsp;     "amount": -87.43,

&nbsp;     "paymentMethod": "debit\_card",

&nbsp;     "paymentMethodDescription": "Paid using Debit Card",

&nbsp;     "status": "completed"

&nbsp;   }

&nbsp; ]

}

```



\### \*\*Example 2: Account Balance\*\*



\*\*UI Shows:\*\*

```

Checking: $12,847.65

Savings: $25,430.20

API Returns:

json{

&nbsp; "accountBalance": {

&nbsp;   "checking": {

&nbsp;     "accountNumber": "\*\*\*\*7262",

&nbsp;     "balance": 12847.65,

&nbsp;     "availableBalance": 12347.65,

&nbsp;     "currency": "USD"

&nbsp;   },

&nbsp;   "savings": {

&nbsp;     "accountNumber": "\*\*\*\*7263",

&nbsp;     "balance": 25430.20,

&nbsp;     "availableBalance": 25430.20,

&nbsp;     "currency": "USD",

&nbsp;     "interestRate": 2.5

&nbsp;   },

&nbsp;   "totalBalance": 38277.85

&nbsp; }

}



🚀 Deployment

After conversion:

bash# Commit changes

git add .

git commit -m "Convert UI mock data to real database API"

git push origin master



\# Seed production database

npx prisma db seed



📖 Key Principles



Always fetch from database - Never hardcode data

Follow JSON standards - camelCase, ISO dates, proper nesting

Include payment details - Method, description, card info

Validate schema - Ensure all fields exist in database

Test thoroughly - API and UI integration

Document queries - Explain Prisma queries used

Security first - Mask sensitive data, validate auth





✅ Success Criteria

Conversion is complete when:

✅ API fetches all data from PostgreSQL via Prisma

✅ No hardcoded/mock values remain

✅ JSON follows consistent formatting

✅ All relationships properly included

✅ Payment method descriptions present

✅ Frontend successfully displays data

✅ Works in production environment

✅ Mentor can verify dynamic data



📞 Support

If conversion encounters issues:



Check Prisma schema for missing fields

Verify database migrations applied

Test API endpoint directly (Postman/browser)

Check console for errors

Validate authentication/authorization





Version: 1.0

Last Updated: November 28, 2025

Project: NextGenFi Banking Platform







