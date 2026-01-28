\# Scout Data Integration - Prompt Repository



This document contains the key prompts and requests used to successfully integrate Scout demographic data into the NextGenBank mortgage lending platform.



---



\## 1. Initial Scout Integration Request



\*\*Prompt:\*\*

"We need to integrate Scout demographic data with our existing mortgage and brokerage leads. The Scout CSV contains pre-calculated propensity scores, demographic information, and intent market scores (IMS) for refinancing, HELOC, and home buying. Please add this data to enhance our lead scoring system by importing the CSV and associating Scout records with existing leads."



\*\*Context:\*\* Starting point for adding third-party demographic enrichment data to improve lead qualification.



---



\## 2. Simplifying the Integration Approach



\*\*Prompt:\*\*

"The complex dashboard approach is too much for our needs. We have 420 existing mortgage leads and want to enhance them with Scout propensity scores via CSV import. Can you pivot to a simpler approach: just add an import button that reads the CSV, matches records to existing leads by email/phone, and displays Scout scores as badges on lead cards?"



\*\*Context:\*\* Reduced complexity from full Scout dashboards to a lightweight enhancement of existing lead views.



---



\## 3. Fixing CSV Column Name Mapping



\*\*Prompt:\*\*

"The CSV import is showing 'Unknown' for all names. Can you check the actual CSV column headers and fix the field mapping? The Scout CSV uses 'fname' and 'lname' instead of 'first\_name' and 'last\_name'. Also update the other field mappings like 'segment\_refined', 'ims\_mtg\_refi', 'ims\_mtg\_heloc', and 'ims\_mtg\_newhome'."



\*\*Context:\*\* Real-world CSV files often have different naming conventions than expected.



---



\## 4. Adding All Scout CSV Data as New Leads



\*\*Prompt:\*\*

"Please import ALL entries from the Scout CSV as new leads in the database. Don't filter or skip any rows - we want the complete dataset imported so we can see the full demographic intelligence Scout provides."



\*\*Context:\*\* Customer wanted comprehensive data import rather than selective matching.



---



\## 5. Fixing AI Copilot Token Limit Errors



\*\*Prompt:\*\*

"The brokerage AI copilot is throwing a 'context\_length\_exceeded' error - it's sending 156,478 tokens but the limit is 128,000. Can you optimize it to send only aggregate statistics and a sample of leads instead of the full JSON for all 400+ leads? Also limit the conversation history to the last 3-5 messages."



\*\*Context:\*\* Scalability issue with sending large datasets to OpenAI's API.



---



\## 6. Adjusting Lead Display Order



\*\*Prompt:\*\*

"Currently Scout leads are showing first, but we want to see our original leads at the top since they're more established. Can you change the sorting so that older leads appear first (by creation date) and newly imported Scout leads appear at the bottom?"



\*\*Context:\*\* Prioritizing existing customer relationships over new demographic data.



---



\## 7. Removing Scout Propensity from Brokerage Pages



\*\*Prompt:\*\*

"The Scout propensity scores are designed for mortgage lending (likelihood of needing a loan), not real estate brokerage transactions. Can you remove the Scout propensity badges from all brokerage lead views since they're not relevant in that context?"



\*\*Context:\*\* Ensuring data relevance - propensity scores only make sense for mortgage products.



---



\## 8. Creating Dedicated Pipeline Pages



\*\*Prompt:\*\*

"Can you create separate dedicated pages for the Buy Pipeline, Sell Pipeline, and Rent Pipeline? Each should show only leads of that intent type, with their respective stage columns. Update the sidebar navigation to link to these new pages instead of using query parameters."



\*\*Context:\*\* Improving user experience with focused views for each transaction type.



---



\## 9. Fixing Buy Pipeline Stage Coverage



\*\*Prompt:\*\*

"The Buy Pipeline overview shows 432 leads, but the pipeline view only shows 6 leads. Can you check what stages exist in the database and add all of them to the BUY\_STAGES array? The issue is that most Scout leads have stage = 'New Lead' which wasn't in the original stage list."



\*\*Context:\*\* Ensuring all imported leads are visible in the pipeline view.



---



\## 10. Greying Out Incomplete Sidebar Items



\*\*Prompt:\*\*

"Please grey out the following incomplete sidebar menu items in the brokerage section: All Properties, Clients, Calendar, Reports, and Documents. Make them non-clickable with reduced opacity and a 'cursor-not-allowed' style to indicate they're not yet implemented."



\*\*Context:\*\* Professional UI/UX - showing users which features are coming soon.



---



\## 11. Removing Scout Import Button for Demo



\*\*Prompt:\*\*

"For the demo presentation, can you remove the Scout Import button from the brokerage leads overview page? We want a cleaner interface without the data management controls visible to stakeholders."



\*\*Context:\*\* Simplifying the UI for executive presentations.



---



\## Key Technical Solutions Implemented



\### Database Schema Changes

\- Added `scoutPropensityScore` (Decimal field)

\- Added `scoutRoutingBucket` (String - hot/warm/cold)

\- Added `scoutSegment` (String - demographic segment)



\### Import Logic

\- CSV parsing with PapaParse

\- Email/phone matching for existing leads

\- Bulk insert for new Scout leads

\- Proper handling of Decimal types from Prisma



\### UI Enhancements

\- Purple Scout badges showing propensity percentages

\- Collapsible import sections

\- Simple drag-and-drop file upload

\- Real-time statistics display after import



\### Performance Optimizations

\- Limited copilot context to 50 sample leads instead of all records

\- Aggregate statistics calculated once

\- Conversation history trimmed to last 3-5 messages

\- Reduced token usage from 156k to ~15k



---



\## Lessons Learned



1\. \*\*Start Simple:\*\* The complex dashboard approach was abandoned in favor of simple badges

2\. \*\*Check Real Data:\*\* CSV column names rarely match initial assumptions

3\. \*\*Token Limits Matter:\*\* Always consider API limits when sending data to LLMs

4\. \*\*Context is Key:\*\* Propensity scores for mortgages don't translate to brokerage

5\. \*\*User Priorities:\*\* Existing customers matter more than new demographic data in the UI

6\. \*\*Stage Management:\*\* Always verify database state before defining UI stage lists



---



\## File Structure Created

```

components/

&nbsp; scout/

&nbsp;   SimpleScoutImporter.tsx    # Main import component

&nbsp;   

lib/

&nbsp; scout-lead-importer.ts        # Import logic and CSV parsing

&nbsp; 

app/

&nbsp; api/

&nbsp;   scout/

&nbsp;     import/route.ts           # Upload endpoint

&nbsp; leads/page.tsx                # Shows Scout badges on mortgage leads

&nbsp; brokerage-leads/

&nbsp;   page.tsx                    # Overview without Scout scores

&nbsp;   buy/page.tsx                # Buy pipeline with all stages

&nbsp;   sell/page.tsx               # Sell pipeline

&nbsp;   rent/page.tsx               # Rent pipeline

```



---



\*This prompt repository documents the iterative development process of integrating Scout demographic data into NextGenBank's mortgage lending platform, including all pivots, fixes, and optimizations made along the way.\*

