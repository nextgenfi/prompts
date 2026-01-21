\# AI Copilot Enhancements: Area Code \& Zip Code Search + Property Type Expansion



\*\*Project:\*\* NextGenBank Mortgage Lending Platform  

\*\*Date:\*\* January 21, 2026  

\*\*Developer:\*\* Chichi (Intern at NextGenFi)  

\*\*Mentor:\*\* Jayaraman Venkataramni (CTO)



---



\## Problem Statement



The AI copilot in the loan leads module was unable to answer questions about leads filtered by area codes or zip codes, despite these fields existing in the database. Additionally, the Hot/Warm/Cold lead statistics were inconsistent between the landing page and the leads list page. The brokerage module also needed expansion to support additional property types (LOT and TOWNHOUSE).



---



\## Requirements



\### 1. AI Copilot Area Code Search Enhancement



\*\*User Request:\*\*

> "Our AI chatbot in the loan leads when asked about what are the leads with area code, it answers 'There are no leads in the database with area code 410.' But I think we do have those in the database. The area code is the initial 3 numbers of the telephone. Also, I want to change the area codes - if they are not 510 or 408, change them to those numbers."



\*\*Business Context:\*\*

\- Phone numbers in the Lead table follow format: XXX-XXX-XXXX

\- Area code = first 3 digits before the first dash

\- All leads should use Bay Area codes (510, 408) for consistency

\- AI copilot should be able to filter and report on leads by area code



\*\*Technical Requirements:\*\*

1\. Update AI copilot context to include phone numbers and area codes

2\. Parse area codes from phone number strings (format: XXX-XXX-XXXX)

3\. Provide area code distribution statistics in database summary

4\. Standardize all existing phone numbers to use only 510 or 408 area codes

5\. Alternate between 510 and 408 when updating to maintain distribution balance



\*\*Expected Behavior:\*\*

\- Query: "What are the leads with area code 510?"

\- Response: Should list all leads with phone numbers starting with 510, including names and contact details



\### 2. Zip Code Integration



\*\*User Request:\*\*

> "My sir asked the chatbot how many leads are from zip code 94536 and it said 'I currently do not have access to the specific zip code data.' We do have a zip code column in the database but it does not show up neither in the leads UI nor in the chatbot answers."



\*\*Business Context:\*\*

\- Zip codes are already stored in the Lead table

\- Zip codes are critical for geographic targeting and lead qualification

\- Both UI and AI copilot need zip code visibility



\*\*Technical Requirements:\*\*

1\. Add zip code field to AI copilot context with all lead data

2\. Include zip code distribution in database summary statistics

3\. Display zip codes in the leads list UI (Details Grid section)

4\. Add zip code icon (📮) for visual clarity

5\. Update AI system prompt to handle zip code queries



\*\*Expected Behavior:\*\*

\- Query: "How many leads are from zip code 94536?"

\- Response: Should return accurate count and list of leads in that zip code

\- UI: Should display zip code alongside phone number, email, and location



\### 3. Statistics Synchronization Fix



\*\*User Request:\*\*

> "Why are these two showing different numbers of hot, warm, and cold leads?"



\*\*Business Context:\*\*

\- Landing page showed: Hot: 454, Warm: 7, Cold: 14

\- Leads list page showed: Hot: 57, Warm: 158, Cold: 260

\- Inconsistency caused confusion for management reporting



\*\*Root Cause:\*\*

\- Landing page used raw score comparison (score >= 80)

\- Leads list page used percentage calculation ((score/1325) \* 100 >= 80)

\- Correct formula: percentage-based (score of 1060+ = HOT at 80%)



\*\*Technical Requirements:\*\*

1\. Standardize temperature calculation across all pages

2\. Use percentage-based formula: (leadScore / 1325) \* 100

3\. Temperature thresholds:

&nbsp;  - HOT: ≥80% (score ≥ 1060)

&nbsp;  - WARM: 50-79% (score 663-1059)

&nbsp;  - COLD: <50% (score <663)

4\. Update landing page stats calculation to match leads page



\### 4. Brokerage Property Type Expansion



\*\*User Request:\*\*

> "In the real estate brokerage lead we need to add two more property types with some new leads if possible: LOT (if that's a type) and TOWNHOUSE."



\*\*Business Context:\*\*

\- Current property types: Single Family, Condo, Farmhouse, Multi-Unit, Commercial

\- Need to support land/lot sales and townhouse transactions

\- Sample leads needed for testing and demonstration



\*\*Technical Requirements:\*\*

1\. Update Prisma schema enum to add LOT and TOWNHOUSE

2\. Apply database migration without data loss

3\. Update TypeScript type definitions in frontend

4\. Update property type label mappings

5\. Create 8 sample leads:

&nbsp;  - 3 LOT leads (2 buyers, 1 seller)

&nbsp;  - 5 TOWNHOUSE leads (3 buyers, 1 seller, 1 renter)

6\. Assign realistic agents to new leads



---



\## Implementation Summary



\### Files Modified



\*\*Backend:\*\*

1\. `prisma/schema.prisma` - Added LOT and TOWNHOUSE to PropertyType enum

2\. `app/api/copilot/route.ts` - Enhanced with phone number, area code, and zip code support

3\. `scripts/update-area-codes.ts` - Script to standardize area codes to 510/408

4\. `scripts/add-sample-brokerage-leads.ts` - Generate sample LOT and TOWNHOUSE leads

5\. `scripts/add-agents-to-brokerage-leads.ts` - Assign agents to new leads



\*\*Frontend:\*\*

1\. `app/page.tsx` - Fixed stats calculation using percentage formula

2\. `app/leads/page.tsx` - Added zip code display in Details Grid

3\. `app/brokerage-leads/page.tsx` - Added LOT and TOWNHOUSE support



\### Database Changes



\*\*Area Code Standardization:\*\*

\- Total leads processed: 475

\- Already had 510/408: 335 leads

\- Changed to 510: 70 leads

\- Changed to 408: 70 leads

\- Distribution: 510 (223 leads), 408 (252 leads)



\*\*New Brokerage Leads:\*\*

\- Added 8 new leads with LOT and TOWNHOUSE properties

\- All leads have realistic Bay Area locations (Fremont, San Jose, Oakland, Berkeley, etc.)

\- Budgets range from $350k-$1.25M

\- Assigned to rotating agent pool: Mike Chen, Sarah Johnson, David Lee, Emily Rodriguez



---



\## AI Copilot Context Enhancement



\### Before:

```typescript

// Lead data without phone numbers or zip codes

\- Name, email, status, temperature, loan type, amount, source, city, state

```



\### After:

```typescript

// Complete lead data with geographic identifiers

\- Name, email, phone, area code, zip code, status, temperature, loan type, amount, source, city, state



// Added statistics

\- By Area Code: 510: 223, 408: 252

\- By Zip Code: 94102: 31, 94103: 20, 94107: 33, \[...]



// Enhanced system prompt

"You have FULL access to the leads database INCLUDING phone numbers, area codes, and zip codes.

Phone numbers are in format XXX-XXX-XXXX where first XXX is the area code.

Zip codes are 5-digit codes (e.g., 94536, 94102)."

```



---



\## Testing Validation



\### Area Code Queries:

✅ "What are the different area codes of leads?" → Returns 408 (252), 510 (223)  

✅ "How many leads with area code 510?" → Returns accurate count  

✅ "List all 408 area code leads" → Returns names with phone numbers



\### Zip Code Queries:

✅ "How many leads are from zip code 94536?" → Returns accurate count  

✅ "What are the different zip codes?" → Returns all 18 SF zip codes with distributions  

✅ "List leads in 94110" → Returns names and details



\### Statistics Accuracy:

✅ Landing page matches leads list page  

✅ Hot: 57, Warm: 158, Cold: 260 (consistent across all views)



\### Property Types:

✅ LOT and TOWNHOUSE visible in brokerage UI  

✅ Property type labels display correctly  

✅ Filter and search work with new types



---



\## Key Learnings



1\. \*\*Context is Everything:\*\* AI copilots can only answer questions about data explicitly included in their context

2\. \*\*Standardization Matters:\*\* Using consistent calculation methods across UI prevents confusion

3\. \*\*Safe Migrations:\*\* Use `db push` instead of `migrate dev` when schema drift detected to avoid data loss

4\. \*\*Type Safety:\*\* TypeScript enums must match Prisma schema and frontend type definitions exactly

5\. \*\*User-Friendly Labels:\*\* Internal ENUM values (SINGLE\_FAMILY) should map to readable labels ("Single Family")



---



\## Future Enhancements



1\. Add city-based filtering alongside area code and zip code

2\. Implement geographic heat maps for lead distribution

3\. Add automated area code validation on lead creation

4\. Create zip code-based marketing campaign triggers

5\. Add property type icons for better visual distinction



---





