Prompt : Add Scout Data Columns



My mentor sent a Scout Data Dictionary CSV with 756 consumer data fields. He wants the Scout Data columns added to the Lead table with randomly populated values. Add columns like:



HHID (Household ID)

PID (Person ID)

AGE, GENDER, DOB

HOMEOWNERCD, DWELLTYPE, MARRIEDCD

MRKTHOMEVAL (Home Value Range)

EDUCATIONCD, LENGTHOFRESIDENCE

CREDITCARD, VETERANCD, HHNBR

CHILDPRESENT, CHILDNBRCD, SGLPARENT

SPANISHSPCD, SOHOCD, CHARITYDNR

WEALTHSCORE







SQL Scripts Used

Create Leads Table

sqlCREATE TABLE IF NOT EXISTS leads (

&nbsp; id TEXT PRIMARY KEY DEFAULT gen\_random\_uuid()::text,

&nbsp; first\_name TEXT NOT NULL,

&nbsp; last\_name TEXT NOT NULL,

&nbsp; phone TEXT NOT NULL,

&nbsp; address TEXT NOT NULL,

&nbsp; state TEXT DEFAULT 'CA',

&nbsp; account\_type TEXT DEFAULT 'Residential',

&nbsp; has\_insurance BOOLEAN DEFAULT false,

&nbsp; earthquake\_zone TEXT DEFAULT 'High',

&nbsp; source TEXT,

&nbsp; created\_at TIMESTAMP DEFAULT NOW(),

&nbsp; updated\_at TIMESTAMP DEFAULT NOW()

);

Merge Tables with Random Propensity Values

sqlINSERT INTO "Lead" (

&nbsp; id, "fullName", "phoneNumber", email, "propertyType", "loanType",

&nbsp; "estimatedLoanAmount", city, state, "zipCode", source, "sourceDetails",

&nbsp; status, "propensityScore", "predictedTemperature", "predictionConfidence",

&nbsp; "leadScore", "contactAttempts", "daysSinceFirstContact", "hasBranchVisit",

&nbsp; "hasCampaignResponse", "hasRecentInquiry", "isExistingCustomer",

&nbsp; "lifeEvent", "lastPredictionAt", "scoreCalculatedAt", "scoringBreakdown",

&nbsp; "createdAt", "updatedAt"

)

SELECT 

&nbsp; gen\_random\_uuid()::text,

&nbsp; first\_name || ' ' || last\_name,

&nbsp; phone,

&nbsp; LOWER(REPLACE(first\_name || '.' || last\_name, ' ', '')) || floor(random()\*100)::text || '@gmail.com',

&nbsp; 'RESIDENTIAL',

&nbsp; (ARRAY\['NEW\_MORTGAGE', 'RENEWAL', 'REFINANCE', 'NEW'])\[floor(random()\*4+1)],

&nbsp; floor(random() \* 900000 + 100000),

&nbsp; 'San Francisco',

&nbsp; 'CA',

&nbsp; (ARRAY\['94102', '94103', '94107', '94110', '94112'])\[floor(random()\*5+1)],

&nbsp; 'Yellow Pages 1907 - Bay Area',

&nbsp; 'Historical Directory Import',

&nbsp; (ARRAY\['NEW', 'CONTACTED', 'QUALIFIED', 'NEGOTIATING'])\[floor(random()\*4+1)],

&nbsp; round((random() \* 100)::numeric, 2),

&nbsp; (ARRAY\['HOT', 'WARM', 'COLD'])\[floor(random()\*3+1)],

&nbsp; round((random() \* 0.5 + 0.5)::numeric, 2),

&nbsp; floor(random() \* 1200),

&nbsp; floor(random() \* 5),

&nbsp; floor(random() \* 30),

&nbsp; random() > 0.7,

&nbsp; random() > 0.6,

&nbsp; random() > 0.5,

&nbsp; random() > 0.85,

&nbsp; (ARRAY\['MARRIAGE', 'RETIREMENT', 'NEW\_JOB', 'RELOCATION', 'GROWING\_FAMILY', NULL])\[floor(random()\*6+1)],

&nbsp; NOW() - (random() \* interval '30 days'),

&nbsp; NOW() - (random() \* interval '7 days'),

&nbsp; '{"loanType":' || floor(random()\*200) || ',"propertyType":' || floor(random()\*150) || '}',

&nbsp; NOW(),

&nbsp; NOW()

FROM leads;

Add Scout Data Columns

sqlALTER TABLE "Lead" 

ADD COLUMN IF NOT EXISTS "hhid" TEXT,

ADD COLUMN IF NOT EXISTS "pid" TEXT,

ADD COLUMN IF NOT EXISTS "age" INTEGER,

ADD COLUMN IF NOT EXISTS "gender" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "dob" VARCHAR(8),

ADD COLUMN IF NOT EXISTS "homeownerCd" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "dwellType" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "marriedCd" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "mrkthomeval" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "educationCd" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "lengthOfResidence" INTEGER,

ADD COLUMN IF NOT EXISTS "creditCard" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "veteranCd" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "hhNbr" INTEGER,

ADD COLUMN IF NOT EXISTS "childPresent" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "childNbrCd" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "sglParent" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "spanishSpCd" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "sohoCd" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "charityDnr" VARCHAR(1),

ADD COLUMN IF NOT EXISTS "wealthScore" VARCHAR(1);

Populate Scout Columns with Random Values

sqlUPDATE "Lead" SET

&nbsp; "hhid" = 'HH' || LPAD(floor(random() \* 999999)::text, 6, '0'),

&nbsp; "pid" = 'PID' || LPAD(floor(random() \* 9999999)::text, 7, '0'),

&nbsp; "age" = floor(random() \* 52 + 25)::integer,

&nbsp; "gender" = (ARRAY\['M', 'F'])\[floor(random() \* 2 + 1)],

&nbsp; "dob" = (1950 + floor(random() \* 50))::text || LPAD(floor(random() \* 12 + 1)::text, 2, '0'),

&nbsp; "homeownerCd" = (ARRAY\['H', 'R', 'O', 'P'])\[floor(random() \* 4 + 1)],

&nbsp; "dwellType" = (ARRAY\['S', 'M'])\[floor(random() \* 2 + 1)],

&nbsp; "marriedCd" = (ARRAY\['M', 'S'])\[floor(random() \* 2 + 1)],

&nbsp; "mrkthomeval" = (ARRAY\['E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R'])\[floor(random() \* 14 + 1)],

&nbsp; "educationCd" = (ARRAY\['A', 'B', 'C', 'D', 'F'])\[floor(random() \* 5 + 1)],

&nbsp; "lengthOfResidence" = floor(random() \* 15)::integer,

&nbsp; "creditCard" = CASE WHEN random() > 0.3 THEN 'Y' ELSE NULL END,

&nbsp; "veteranCd" = CASE WHEN random() > 0.85 THEN 'Y' ELSE NULL END,

&nbsp; "hhNbr" = floor(random() \* 4 + 1)::integer,

&nbsp; "childPresent" = CASE WHEN random() > 0.5 THEN 'Y' ELSE NULL END,

&nbsp; "childNbrCd" = (ARRAY\['A', 'B', 'C', 'D'])\[floor(random() \* 4 + 1)],

&nbsp; "sglParent" = CASE WHEN random() > 0.85 THEN 'Y' ELSE NULL END,

&nbsp; "spanishSpCd" = CASE WHEN random() > 0.8 THEN 'Y' ELSE NULL END,

&nbsp; "sohoCd" = CASE WHEN random() > 0.9 THEN 'Y' ELSE NULL END,

&nbsp; "charityDnr" = CASE WHEN random() > 0.7 THEN 'Y' ELSE NULL END,

&nbsp; "wealthScore" = (ARRAY\['1', '2', '3', '4', '5', '6', '7', '8', '9'])\[floor(random() \* 9 + 1)];



Final Data Summary

Field CategoryFields AddedBasic InfofullName, phoneNumber, email, address, city, state, zipCodePropensity ScoringpropensityScore, predictedTemperature, leadScore, scoringBreakdownLead Statusstatus, lifeEvent, source, sourceDetailsEngagementhasBranchVisit, hasCampaignResponse, hasRecentInquiry, isExistingCustomerScout Demographicshhid, pid, age, gender, dob, marriedCdScout HousinghomeownerCd, dwellType, mrkthomeval, lengthOfResidenceScout LifestyleeducationCd, creditCard, veteranCd, charityDnr, wealthScoreScout FamilyhhNbr, childPresent, childNbrCd, sglParentScout OtherspanishSpCd, sohoCd

Total Records: 504 (3 original + 501 imported)

Distribution by Area Code:



415 (SF/Marin): ~100 entries

650 (Peninsula): ~100 entries

925 (East Bay): ~100 entries

510 (Oakland): ~100 entries

408 (San Jose): ~100 entries

+1 mentor entry (Jayaraman Venkataramni)

