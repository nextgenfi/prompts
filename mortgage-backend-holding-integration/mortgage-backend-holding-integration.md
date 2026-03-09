PROMPT 1 — Restructure Real Estate as a Holding Company Landing Page

--------------------------------------------------------------------------------



Restructure the Real Estate section of the employee portal into a formal

"NextGenBank Real Estate Holding Company" hub with the following changes:



1\. Replace the current /employee/real-estate page with a professional landing

&nbsp;  page that presents three division cards:

&nbsp;  - Brokerage Leads  (blue)   → /employee/real-estate/brokerage-leads

&nbsp;  - Loan Leads       (green)  → /employee/real-estate/loan-leads

&nbsp;  - Campaign         (violet) → /employee/real-estate/campaign



&nbsp;  The page title should read "NextGenBank Real Estate Holding Company".



2\. Move the existing brokerage leads page from /employee/real-estate to

&nbsp;  /employee/real-estate/brokerage-leads. Update all internal href references

&nbsp;  in BrokerageSidebarLayout accordingly (intent filter URLs, isActive checks).



3\. Create /employee/real-estate/campaign/page.tsx that renders the existing

&nbsp;  <RAGSection /> component (Home Insurance Promotions), wrapped in a simple

&nbsp;  page layout with a "Back to Holdings" header.



4\. Remove Home Insurance Promotions from the bank-backend dashboard Campaign

&nbsp;  dropdown. The dashboard Campaign section should retain only:

&nbsp;  - Customer Profitability Based Offers

&nbsp;  - Customer e-Risk Withdrawal

&nbsp;  - Customer e-Statement



5\. Change the "Real Estate" button in the employee dashboard nav to use a

&nbsp;  corporate/institutional building icon and relabel it "Real Estate Holdings".





--------------------------------------------------------------------------------

PROMPT 2 — Fix Back Navigation Across the Real Estate Section

--------------------------------------------------------------------------------



Update the back navigation throughout the Real Estate section to create a

consistent three-level hierarchy:



&nbsp; Dashboard Overview → Real Estate Holdings → Brokerage Leads / Loan Leads



Changes required:



1\. BrokerageSidebarLayout: Change the "Back to Home" link to "Back to Holdings"

&nbsp;  pointing to /employee/real-estate.



2\. SidebarLayout (Loan Leads): Change the "Dashboard" nav item to "RE Holdings"

&nbsp;  pointing to /employee/real-estate. Update isActive to match the new href.



3\. Real Estate Holdings landing page: Add a "← Back to Dashboard Overview"

&nbsp;  button that navigates to /employee.





--------------------------------------------------------------------------------

PROMPT 3 — Remove Cross-Navigation Links Between Sidebars

--------------------------------------------------------------------------------



Remove the cross-navigation links that appear in each sidebar:



1\. In BrokerageSidebarLayout: remove the "Loan Leads" quick-nav link

&nbsp;  (with the Landmark icon) from the section above the main nav.

&nbsp;  Also remove the now-unused Landmark import from lucide-react.



2\. In SidebarLayout (Loan Leads): remove the "Brokerage Leads" cross-nav

&nbsp;  link and its accompanying divider from the top of the main nav section.



Each sidebar should only navigate within its own section. Users return to

the Real Estate Holdings landing page to switch between divisions.





--------------------------------------------------------------------------------

PROMPT 4 — Add Back to Holdings Link in Loan Leads Sidebar

--------------------------------------------------------------------------------



In SidebarLayout (Loan Leads), add a "Back to Holdings" link above the

Main Menu section (similar to the pattern in BrokerageSidebarLayout).



The link should:

&nbsp; - Navigate to /employee/real-estate

&nbsp; - Display a left chevron icon followed by "Back to Holdings" text

&nbsp; - Collapse to just the icon when the sidebar is in collapsed state

&nbsp; - Use the same styling as the equivalent link in BrokerageSidebarLayout



Also remove the "RE Holdings" item from the mainNavItems array in

SidebarLayout, since navigation back to the holding company is now handled

by the dedicated back link above the menu.





--------------------------------------------------------------------------------

PROMPT 5 — Rebrand "RE Holdings" to "Real Estate Holdings"

--------------------------------------------------------------------------------



Rename all instances of the short label "RE Holdings" to the full label

"Real Estate Holdings" across the application:



1\. Employee dashboard nav button: "RE Holdings" → "Real Estate Holdings"

2\. BrokerageSidebarLayout: update the tooltip title attribute on the

&nbsp;  back link to "Back to Real Estate Holdings"

3\. SidebarLayout: update the tooltip title attribute on the back link

&nbsp;  to "Back to Real Estate Holdings"





--------------------------------------------------------------------------------

PROMPT 6 — Professional Bank-Style Redesign of the Holdings Landing Page

--------------------------------------------------------------------------------



Redesign the Real Estate Holdings landing page to look like a formal internal

banking portal. Keep the existing colour coding (blue / emerald / violet) but

elevate the overall visual treatment:



Structure:

&nbsp; - A dark top bar (bg-slate-900) showing "NextGenBank — Employee Portal"

&nbsp;   on the left and "Real Estate Division" on the right.

&nbsp; - A white header section containing:

&nbsp;   - A breadcrumb row ("Employee Portal > Real Estate Holdings") on the left

&nbsp;     and a visible "← Back to Dashboard Overview" pill button on the right.

&nbsp;   - A title row with a dark icon block, "SUBSIDIARY DIVISION" label,

&nbsp;     the full company name, a one-line description, and a "3 Divisions Active"

&nbsp;     status pill.

&nbsp; - A divisions section with a labelled header row (title + divider line +

&nbsp;   item count) followed by a 3-column card grid.

&nbsp; - A footer strip with the company name on the left and a "Back to Employee

&nbsp;   Portal" link on the right.



Card design (per division):

&nbsp; - A 3px coloured top border strip (accent colour)

&nbsp; - Icon block (coloured background square) + category badge on the same row

&nbsp; - Division title and description text

&nbsp; - A bottom row (separated by a border-t) with "Access Division" text in the

&nbsp;   accent colour and a right-pointing arrow that animates on hover



Do not change any of the existing colour assignments between divisions.





