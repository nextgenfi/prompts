\# Scout Lead Scoring \& Sorting Optimization - Prompt Repository



This document contains prompts used to optimize Scout demographic data integration, including proper lead scoring conversion, intelligent sorting strategies, and build error resolution.



---



\## 1. Converting Scout Propensity to Internal Lead Scores



\*\*Prompt:\*\*

"I notice the Scout leads are showing with high propensity scores (87%, 63%) but they're classified as COLD leads because their leadScore is 0. Can you convert the Scout propensity percentage (0-100%) to our internal leadScore system (0-1325 points) during import? This way Scout leads will be properly classified as HOT/WARM/COLD based on their actual propensity scores."



\*\*Context:\*\* Scout demographic data includes propensity percentages, but our internal system uses a point-based scoring system. Without conversion, high-propensity leads were misclassified as low-priority.



\*\*Technical Solution:\*\*

\- Added calculation: `leadScore = Math.round((propensity / 100) \* 1325)`

\- Applied during CSV import in `lib/scout-lead-importer.ts`

\- Result: 87% propensity → ~1150 points (HOT), 63% propensity → ~834 points (WARM)



---



\## 2. Prioritizing Established Leads Over New Imports



\*\*Prompt:\*\*

"The Scout leads with high scores are appearing at the top of the list, but I'd prefer to see our established leads first since they're existing relationships we've been nurturing. Can you adjust the sorting so that older, original leads appear first, then Scout leads appear after them? Within each group, sort by leadScore from highest to lowest."



\*\*Context:\*\* Business priority is to focus on existing customer relationships before pursuing new demographic leads. The interface should reflect this priority hierarchy.



\*\*Technical Solution:\*\*

```javascript

// Implemented two-tier sorting logic

const sortedLeads = leadsWithTemperature.sort((a, b) => {

&nbsp; const aIsScout = a.source === 'SCOUT\_IMPORT';

&nbsp; const bIsScout = b.source === 'SCOUT\_IMPORT';

&nbsp; 

&nbsp; // Non-Scout (older) leads come first

&nbsp; if (!aIsScout \&\& bIsScout) return -1;

&nbsp; if (aIsScout \&\& !bIsScout) return 1;

&nbsp; 

&nbsp; // Within same group, sort by leadScore (highest first)

&nbsp; return b.leadScore - a.leadScore;

});

```



---



\## 3. Applying Consistent Sorting to Brokerage Module



\*\*Prompt:\*\*

"Can you apply the same prioritization logic to the brokerage leads overview page? I want to see our original brokerage leads (like the manually entered ones) at the top, then Scout-imported leads below them, all sorted by creation date with oldest first."



\*\*Context:\*\* The mortgage leads module had proper sorting, but brokerage leads needed the same treatment for consistency across the platform.



\*\*Technical Solution:\*\*

\- Updated `app/api/brokerage-leads/route.ts` with chronological sorting

\- Oldest leads appear first (established relationships prioritized)

\- Maintained consistency between mortgage and brokerage modules



---



\## 4. Understanding Scout Data Demographics



\*\*Prompt:\*\*

"I'm seeing that all the Scout imported data shows as BUY leads in the brokerage module. Is this intentional? Why don't we have SELL or RENT leads from Scout?"



\*\*Context:\*\* Clarification needed on why Scout demographic data translates entirely to buyer intent.



\*\*Answer:\*\* Scout demographic intelligence is focused on identifying people with high propensity to \*\*purchase homes\*\* (who will need mortgages). The propensity scores measure likelihood of needing a mortgage loan, which by definition means they're buyers, not sellers or renters. This is correct behavior - Scout data identifies potential buyers based on demographic signals like recent moves, life events, property searches, etc.



---



\## 5. Resolving TypeScript Build Errors - Missing Source Field



\*\*Prompt:\*\*

"I'm getting a TypeScript build error saying 'Property source does not exist on type BrokerageLead'. The error is on line 40 where we check `a.source === 'SCOUT\_IMPORT'`. Can you fix this so the build succeeds?"



\*\*Context:\*\* BrokerageLead schema doesn't have a `source` field like the Lead schema does.



\*\*Technical Solution:\*\*

\- Simplified sorting to use only `createdAt` timestamp

\- Removed source-based filtering (not needed for brokerage)

\- Build now succeeds without TypeScript errors

```javascript

// Simplified approach - no source field needed

const sortedLeads = leads.sort((a, b) => {

&nbsp; return new Date(a.createdAt).getTime() - new Date(b.createdAt).getTime();

});

```



---



\## 6. Additional Build Error - POST Handler Source Field



\*\*Prompt:\*\*

"Another build error: 'source does not exist in type BrokerageLeadCreateInput' on line 93 in the POST handler. Can you remove that field from the brokerage lead creation?"



\*\*Context:\*\* The POST handler was trying to set a `source` field that doesn't exist in the BrokerageLead schema.



\*\*Technical Solution:\*\*

\- Removed `source: body.source || 'MANUAL'` from create operation

\- BrokerageLead model doesn't track source (only Lead model does)

\- Build completed successfully



---



\## Key Technical Decisions



\### Why Different Sorting Strategies?



\*\*Mortgage Leads:\*\* Two-tier sorting (source-based, then score-based)

\- Has `source` field to distinguish Scout imports from other sources

\- More complex lead scoring with multiple data points

\- Benefit: Prioritizes established relationships while maintaining score-based ordering



\*\*Brokerage Leads:\*\* Simple chronological sorting

\- No `source` field in schema

\- Simpler data model focused on transaction stages

\- Benefit: Oldest leads (most mature relationships) appear first naturally



\### Why Convert Propensity to Lead Score?



The platform uses a unified 0-1325 point system for lead prioritization:

\- 80%+ (1060+ pts) = HOT (red badge, high priority)

\- 50-79% (662-1059 pts) = WARM (yellow badge, medium priority)  

\- <50% (<662 pts) = COLD (blue badge, low priority)



Scout's percentage-based propensity scores needed conversion to integrate with existing temperature-based workflows, priority queues, and agent assignment algorithms.



\### Business Impact



\*\*Before optimization:\*\*

\- High-propensity Scout leads misclassified as COLD

\- New imports appearing above established relationships

\- Inconsistent sorting between mortgage and brokerage modules



\*\*After optimization:\*\*

\- Accurate temperature classification based on Scout intelligence

\- Established customer relationships prioritized in UI

\- Consistent experience across all lead management modules

\- Sales team can focus on hot established leads first, then pursue high-propensity Scout leads



---



\## File Changes Summary



\*\*Modified Files:\*\*

1\. `lib/scout-lead-importer.ts` - Added propensity-to-leadScore conversion

2\. `app/api/leads/route.ts` - Implemented two-tier sorting logic

3\. `app/api/brokerage-leads/route.ts` - Simplified to chronological sorting

4\. Removed source field references from brokerage POST/PATCH handlers



\*\*Database Impact:\*\*

\- No schema changes required

\- Existing Scout leads need re-import to populate leadScore field

\- Command: Delete existing Scout imports, then re-run CSV import



---



\## Deployment Notes



\*\*Pre-deployment checklist:\*\*

1\. ✅ Delete existing Scout leads with 0 leadScore: `DELETE FROM "Lead" WHERE source = 'SCOUT\_IMPORT' AND leadScore = 0;`

2\. ✅ Re-import Scout CSV with updated importer

3\. ✅ Verify temperature classifications are correct

4\. ✅ Confirm sort order: old leads → Scout leads

5\. ✅ Build succeeds without TypeScript errors



\*\*Post-deployment validation:\*\*

\- Hot Scout leads (>87% propensity) should show ~1150+ points

\- Warm Scout leads (63-79% propensity) should show 834-1046 points

\- Original leads (B Apple, james, etc.) should appear at top of list

\- No COLD leads with >80% scores



---





