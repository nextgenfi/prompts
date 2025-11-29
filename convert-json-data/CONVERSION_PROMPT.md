You are helping me take UI mock data and convert it into a real backend JSON API response format that connects to the actual database.

Your tasks:
1️⃣ Convert any displayed UI data into a structured JSON API response format.
2️⃣ Do NOT use hardcoded static values — fetch data dynamically from PostgreSQL through Prisma.
3️⃣ Include relevant fields such as:
   - user -> account -> budget -> transactions -> bills summary
   - payment method details: type, description, and masked card/wallet info
4️⃣ Ensure the JSON follows consistent key naming and formatting:
   - camelCase keys
   - Include nested fields like paymentMethodDescription, merchantCategory, timestamps, etc.
5️⃣ When needed, modify the Prisma queries and add relationships if missing.
6️⃣ Validate that the JSON matches real DB columns and relations — no fake fields.
7️⃣ If UI shows masked data (e.g. ****7262), derive mask using the real account number.

Output Format:
- Clean JSON output
- No comments inside the JSON
- Then below JSON include explanation of DB queries used
- If schema changes needed, generate migration code

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
typescriptconst mockTransactions = [
  { name: "Starbucks", amount: "$6.75" }
]
Converted API Response:
json{
  "success": true,
  "transactions": [
    {
      "id": "txn_001",
      "description": "Starbucks",
      "amount": -6.75,
      "date": "2025-11-26T19:22:33Z",
      "paymentMethod": "mobile_payment",
      "paymentMethodDescription": "Paid using Mobile Payment (Apple Pay)",
      "paymentDetails": "Apple Pay - Visa •••• 7262"
    }
  ]
}

2️⃣ Use Dynamic Database Queries (NO Hardcoded Values)
❌ Don't Do This:
typescriptconst balance = 12847.65 // Hardcoded
✅ Do This:
typescriptconst account = await prisma.bankAccount.findFirst({
  where: { userId }
})
const balance = account.balance // From database

3️⃣ Include Comprehensive Field Structure
Every API response must include relevant nested data:
json{
  "user": {
    "id": "user_123",
    "name": "John Doe",
    "email": "john@example.com"
  },
  "accounts": [
    {
      "id": "acc_001",
      "accountNumber": "****4532",
      "accountType": "checking",
      "balance": 12847.65,
      "currency": "USD"
    }
  ],
  "transactions": [
    {
      "id": "txn_001",
      "date": "2025-11-28T14:32:15Z",
      "description": "Whole Foods Market",
      "amount": -87.43,
      "partyFrom": "John Doe - Checking ****4532",
      "partyTo": "Whole Foods Market #234",
      "paymentMethod": "debit_card",
      "paymentMethodDescription": "Paid using Debit Card",
      "paymentDetails": "Visa Debit •••• 4532",
      "merchantCategory": "Grocery Stores",
      "status": "completed"
    }
  ],
  "bills": [
    {
      "id": "bill_001",
      "billName": "Electric Bill",
      "company": "City Power & Light",
      "dueDate": "2025-12-05",
      "amount": 156.42,
      "autopay": true
    }
  ],
  "summary": {
    "totalIncome": 5500.00,
    "totalExpenses": 2166.80,
    "netCashFlow": 3333.20
  }
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
  "paymentMethod": "debit_card",
  "paymentMethodDescription": "Paid using Debit Card",
  "paymentDetails": "Visa Debit •••• 4532",
  "merchantCategory": "Grocery Stores",
  "createdAt": "2025-11-28T14:32:15Z",
  "updatedAt": "2025-11-28T14:32:15Z"
}

5️⃣ Prisma Relationships & Schema Updates
Check Existing Relationships:
prismamodel User {
  id           String        @id
  bankAccounts BankAccount[]
  transactions Transaction[]
  bills        Bill[]
}

model BankAccount {
  id           String        @id
  userId       String
  user         User          @relation(fields: [userId], references: [id])
  transactions Transaction[]
}

model Transaction {
  id                       String      @id
  userId                   String
  accountId                String
  paymentMethodDescription String      // Ensure this exists
  user                     User        @relation(fields: [userId], references: [id])
  account                  BankAccount @relation(fields: [accountId], references: [id])
}
If Missing, Add Migration:
bashnpx prisma migrate dev --name add_payment_method_description

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
Database Value: "****7262"
API Response: "****7262"
Dynamic Masking:
typescriptconst accountNumber = account.accountNumber // "****7262"
const lastFour = accountNumber.slice(-4)    // "7262"
const paymentDetails = `Visa Debit •••• ${lastFour}`

📤 Output Format
1. Clean JSON Response
json{
  "success": true,
  "data": {
    "accountBalance": 12847.65,
    "transactions": []
  }
}
2. Database Query Explanation
typescript// Prisma Query Used:
const account = await prisma.bankAccount.findFirst({
  where: { 
    userId: session.user.id,
    accountType: 'checking'
  }
})

const transactions = await prisma.transaction.findMany({
  where: { userId: session.user.id },
  orderBy: { date: 'desc' },
  take: 30,
  include: {
    account: true
  }
})
3. Schema Changes (If Needed)
prisma// Add to schema.prisma:
model Transaction {
  // ... existing fields
  paymentMethodDescription String  // NEW FIELD
}
Generate Migration:
bashnpx prisma migrate dev --name add_payment_method_description

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
  const session = await auth.api.getSession()
  
  const accounts = await prisma.bankAccount.findMany({
    where: { userId: session.user.id }
  })
  
  const transactions = await prisma.transaction.findMany({
    where: { userId: session.user.id },
    orderBy: { date: 'desc' },
    take: 30
  })
  
  return NextResponse.json({
    accountBalance: {
      checking: accounts.find(a => a.accountType === 'checking'),
      savings: accounts.find(a => a.accountType === 'savings'),
      totalBalance: accounts.reduce((sum, a) => sum + a.balance, 0)
    },
    recentTransactions: transactions.map(txn => ({
      id: txn.id,
      date: txn.date.toISOString(),
      description: txn.description,
      amount: txn.amount,
      paymentMethodDescription: txn.paymentMethodDescription,
      // ... all fields
    }))
  })
}
Step 4: Update Frontend Fetch Code
typescript// components/customer-dashboard.tsx
useEffect(() => {
  const fetchData = async () => {
    const response = await fetch('/api/customer/financial-overview')
    const data = await response.json()
    setFinancialData(data)
  }
  fetchData()
}, [])
```

### **Step 5: Test & Validate**
- Test API endpoint returns correct data
- Verify UI displays data properly
- Check all fields are populated

---

## 🏗️ **Project Structure**
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

## 🧪 **Testing Checklist**

Before marking conversion complete:

- [ ] API returns valid JSON
- [ ] All data fetched from database (no hardcoded values)
- [ ] Timestamps in ISO 8601 format
- [ ] Nested relationships included
- [ ] Payment method descriptions present
- [ ] Currency values are numbers, not strings
- [ ] Account numbers properly masked
- [ ] Frontend successfully fetches and displays data
- [ ] No console errors
- [ ] Works in both development and production

---

## 📚 **Examples**

### **Example 1: Transaction List**

**UI Shows:**
```
Starbucks - $6.75
Whole Foods - $87.43
API Returns:
json{
  "transactions": [
    {
      "id": "txn_001",
      "date": "2025-11-26T19:22:33Z",
      "description": "Starbucks",
      "amount": -6.75,
      "paymentMethod": "mobile_payment",
      "paymentMethodDescription": "Paid using Mobile Payment (Apple Pay)",
      "status": "completed"
    },
    {
      "id": "txn_002",
      "date": "2025-11-28T14:32:15Z",
      "description": "Whole Foods Market",
      "amount": -87.43,
      "paymentMethod": "debit_card",
      "paymentMethodDescription": "Paid using Debit Card",
      "status": "completed"
    }
  ]
}
```

### **Example 2: Account Balance**

**UI Shows:**
```
Checking: $12,847.65
Savings: $25,430.20
API Returns:
json{
  "accountBalance": {
    "checking": {
      "accountNumber": "****7262",
      "balance": 12847.65,
      "availableBalance": 12347.65,
      "currency": "USD"
    },
    "savings": {
      "accountNumber": "****7263",
      "balance": 25430.20,
      "availableBalance": 25430.20,
      "currency": "USD",
      "interestRate": 2.5
    },
    "totalBalance": 38277.85
  }
}

🚀 Deployment
After conversion:
bash# Commit changes
git add .
git commit -m "Convert UI mock data to real database API"
git push origin master

# Seed production database
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


