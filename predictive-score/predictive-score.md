\*\*prompt one\*\*





You are already familiar with our mortgage lending web application, including its existing UI, database schema, user roles, and lead-related work done so far.



We already implemented a Lead Input \& Lead Management system with manual Hot / Medium / Cold tagging.

Now I want to refine and upgrade this logic.



Change Request: Rule-Based Lead Scoring (No Manual Selection)

1\. Remove Manual Lead Temperature Selection



Lead Temperature (Hot / Medium / Cold) should NOT be manually selected by employees anymore.



Bank employees should not see a dropdown for Hot / Medium / Cold.



2\. Introduce Lead Scoring (Points-Based System)



Instead of manual tagging, introduce a Lead Score (numeric) that is calculated automatically using predefined business rules.



Examples of scoring signals (illustrative, not exhaustive):



Life events (e.g., birth of a child, marriage, relocation)



Existing customer vs new customer



Loan type (Renewal > Refinance > New)



Property type (Residential vs Commercial)



Estimated loan amount (higher amount = higher score)



Engagement indicators (recent inquiry, campaign response, branch visit, etc.)



Any other reasonable customer attributes already available or easy to add



These rules should be implemented as deterministic logic, not ML.



3\. Lead Temperature Derived from Score



Automatically map score ranges to temperature:



Hot → High score



Medium → Mid score



Cold → Low score



Lead Temperature should be computed, not stored as user input.



Example (flexible, can be adjusted):



Score ≥ X → Hot



Score between Y–X → Medium



Score < Y → Cold



4\. Data Model Changes



Add fields such as:



leadScore (number)



scoringBreakdown (optional JSON explaining how points were assigned)



Keep the schema future-ready for ML, but:



Current implementation must be rule-based only



Add clear TODO comments indicating where predictive / propensity models will replace or enhance this logic later



5\. UI / UX Updates



In the Lead Management table:



Show Lead Temperature (derived)



Show Lead Score (optional but preferred)



For new leads, temperature should appear automatically upon creation.



Ensure the UI remains consistent with the existing professional mortgage dashboard.



Do not break or modify any existing unrelated features.



6\. Access Control



Only bank employees can:



View lead scores and temperature logic



View detailed lead attributes used for scoring



No manual override of temperature for now.



7\. Explanation After Implementation



After completing this change, briefly explain:



What logic was added or modified



How lead temperature is now derived



Where predictive / propensity models will integrate later



How this improves the original “lead input progress” requirement



Important Constraints



Do NOT implement ML or external models



Do NOT change existing core flows



Architecture must be prediction-ready



Keep the system scalable and clean



If you want, next we can:



Design the exact scoring formula



Add audit visibility (why a lead is Hot)



Plug this into campaign performance later







\*\*prompt two\*\*





I need you to build a complete lead propensity scoring system for my NextGen mortgage lending platform. The system should calculate lead scores based on multiple factors and display them with color-coded temperature indicators (HOT/WARM/COLD).



\## TECH STACK:

\- Next.js 14+ (App Router)

\- TypeScript

\- Prisma ORM

\- PostgreSQL

\- Tailwind CSS



\## EXISTING PRISMA SCHEMA:

My Lead model in schema.prisma already has these fields:

\- id, fullName, phoneNumber, email

\- propertyType, loanType, estimatedLoanAmount

\- city, state, zipCode

\- source, sourceDetails, status

\- leadScore (Int), scoringBreakdown (Text), scoreCalculatedAt

\- isExistingCustomer (Boolean)

\- lifeEvent (String)

\- hasRecentInquiry, hasCampaignResponse, hasBranchVisit (Booleans)

\- daysSinceFirstContact (Int)

\- campaignId (relation to Campaign model)

\- createdAt, updatedAt



\## SCORING CRITERIA (from Product Manager):



Factor | Options | Points

-------|---------|-------

Loan Type | New Mortgage: 100 | Refinance: 150 | Renewal: 200

Property Type | Residential: 100 | Commercial: 150

Loan Amount | <$150K: 50 | $150K-$300K: 75 | $300K-$500K: 100 | >$500K: 150

Lead Source | External Data: 50 | Campaign: 100 | Walk-in: 150 | Referral: 200

Existing Customer | No: 0 | Yes: 200

Life Event | None: 0 | Retirement: 80 | Child Birth: 100 | Marriage: 120 | Relocation: 150

Engagement | Recent Inquiry: +100 | Campaign Response: +75 | Branch Visit: +125

Contact Info | None: 0 | Phone OR Email: 25 | Phone AND Email: 50

Recency | >30 days: 0 | 14-30 days: 50 | 7-14 days: 75 | <7 days: 100



\## TEMPERATURE RULES:

\- HOT (≥80% score): Red background/badge

\- WARM (50-79% score): Yellow background/badge

\- COLD (<50% score): Blue background/badge



\## UI REQUIREMENTS:

1\. Single screen with form and list (not separate tabs)

2\. Color-coded lead cards with temperature badges

3\. Sort leads: HOT → WARM → COLD, then by score

4\. Pagination (10 leads per page)

5\. Display scoring breakdown when lead is created/updated

6\. Show engagement indicators as colored badges

7\. Clean, modern design with emojis for visual clarity



\## FILES I NEED (complete, copy-paste ready):



1\. \*\*lib/propensityScoreCalculator.ts\*\*

&nbsp;  - Export PROPENSITY\_POINTS constant with all scoring rules

&nbsp;  - calculatePropensityScore() function that returns { score, maxScore, percentage, temperature, breakdown }

&nbsp;  - getTemperatureColor() and getTemperatureBadgeColor() helper functions

&nbsp;  - Auto-calculate contact info quality from email/phone presence



2\. \*\*app/api/leads/route.ts\*\*

&nbsp;  - POST: Create lead with auto-calculated score

&nbsp;  - GET: Fetch leads with pagination, sorted by temperature then score

&nbsp;  - Return leads with temperature and percentage calculated



3\. \*\*app/api/leads/\[id]/route.ts\*\*

&nbsp;  - PUT: Update lead and recalculate score

&nbsp;  - DELETE: Delete lead

&nbsp;  - Calculate daysSinceFirstContact from createdAt timestamp



4\. \*\*components/LeadForm.tsx\*\*

&nbsp;  - Client component with all form fields

&nbsp;  - Dropdowns for: loanType, propertyType, source, lifeEvent, status

&nbsp;  - Checkboxes for: isExistingCustomer, hasRecentInquiry, hasCampaignResponse, hasBranchVisit

&nbsp;  - Number input for estimatedLoanAmount

&nbsp;  - Show point values in checkbox labels (e.g., "Existing Customer (+200 pts)")

&nbsp;  - Alert showing score breakdown after submission

&nbsp;  - Can be used for both create and edit (accepts lead prop)



5\. \*\*app/leads/page.tsx\*\*

&nbsp;  - Client component displaying all leads

&nbsp;  - Color-coded lead cards using temperature

&nbsp;  - Show: name, temperature badge, score, percentage, status

&nbsp;  - Display all lead details in organized grid

&nbsp;  - Engagement indicators as small colored badges

&nbsp;  - Edit and Delete buttons

&nbsp;  - Pagination controls at bottom

&nbsp;  - Toggle between list view and form view

&nbsp;  - Legend showing temperature criteria



\## ADDITIONAL REQUIREMENTS:

\- Store scoring breakdown as JSON in scoringBreakdown field

\- Calculate daysSinceFirstContact automatically from createdAt

\- Use leadScore field (0-1325 range), not a 0-100 scale

\- Temperature is DERIVED from percentage, not stored separately

\- Include proper TypeScript types

\- Handle null/undefined values gracefully

\- Show loading states

\- Add proper error handling



\## DESIGN NOTES:

\- Use Tailwind CSS utility classes

\- Red/Yellow/Blue color scheme for temperatures

\- Clean card-based layout with proper spacing

\- Icons/emojis for better visual hierarchy

\- Responsive design (mobile-friendly)







