You are already familiar with our mortgage lending web application, its existing UI, database schema, and user flows.



I want to add a new feature called “Lead Input \& Lead Intelligence” to the system.



1\. Lead Input Module



Add a Lead Input section accessible from the main dashboard (for bank employees only).



This should be a clean form that allows manual entry of leads with fields such as:



Full Name



Phone Number



Email



Property Type (Residential / Commercial)



Loan Type (New / Refinance / Renewal)



Estimated Loan Amount



Location (City, State, ZIP)



Source of Lead (Campaign / Walk-in / Referral / External Data)



On submission, store the lead in the existing database with proper validation and timestamps.



2\. Lead Listing \& Status



Create a Lead Management view where all leads are listed in a table.



Each lead should have:



Lead ID



Date created



Current status (New / Contacted / In Progress / Closed)



A placeholder column called Lead Temperature



3\. Lead Temperature (Preparation for Predictive Scoring)



For now, the Lead Temperature should be manually assigned as:



🔴 Hot



🟡 Medium



🔵 Cold



Clearly structure the data model so that this field can later be auto-calculated using predictive / propensity models.



Add comments or TODOs in code indicating where future predictive logic will plug in.



4\. UI \& UX Requirements



Keep the UI consistent with the current professional mortgage dashboard design.



Do NOT break or change any existing features.



Lead Input and Lead Management should feel like a natural extension of the current system.



5\. Future-Ready Design (Important)



Design the backend and frontend in a way that supports:



Predictive lead scoring



Propensity-based classification (Hot / Medium / Cold)



Integration with campaign performance data later



Do not implement ML now — just make the architecture prediction-ready.



6\. Access Control



Only bank employees should be able to:



Add leads



Edit lead temperature



View full lead details



After implementing this, briefly explain:



What was added



Where predictive logic will be integrated later



How this answers the “lead input progress” requirement

