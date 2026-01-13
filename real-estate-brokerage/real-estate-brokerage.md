\# NextGenBank Platform - Development Requirements Documentation



\*\*Project:\*\* Mortgage Lending \& Real Estate Brokerage Lead Management System  

\*\*Tech Stack:\*\* Next.js 14, TypeScript, PostgreSQL (Neon), Prisma ORM, OpenAI API  

\*\*Date:\*\* January 2026  

\*\*Developer:\*\* Chichi (Intern Developer)  

\*\*Supervisor:\*\* Jayaraman Venkataramni (CTO)



---



\## 1. Brokerage Leads Module - Initial Implementation



\### Context

Building upon the existing Loan Leads module architecture, we are developing a comprehensive Real Estate Brokerage Leads management system. This module should maintain consistency with established patterns while introducing brokerage-specific functionality.



\### Requirements



\#### 1.1 UI Layout \& Information Architecture

\*\*Objective:\*\* Create a compact, information-dense interface optimized for professional mortgage and real estate workflows.



\*\*Specifications:\*\*

\- Implement responsive, single-screen layout without horizontal scrolling

\- Optimize search and filter components for minimal vertical space consumption

\- Design for standard desktop resolutions (1920x1080 minimum)

\- Ensure all critical information is visible without layout overflow

\- Allow only vertical scrolling for content navigation



\*\*Rationale:\*\* Professional users require efficient information access without constant navigation or scrolling. Desktop-first design aligns with industry-standard banking applications.



\#### 1.2 AI Chatbot Integration

\*\*Objective:\*\* Extend AI copilot functionality to brokerage operations with domain-specific intelligence.



\*\*Implementation Requirements:\*\*

\- Position chatbot at bottom-right corner (consistent with Loan Leads module)

\- Implement brokerage-specific conversation flows:

&nbsp; - Property transaction inquiries (Buy/Sell/Rent)

&nbsp; - Open house coordination and follow-ups

&nbsp; - Buyer qualification and feedback management

&nbsp; - Seller outreach and contract progression tracking

&nbsp; 

\*\*Technical Architecture:\*\*

\- Create dedicated `BrokerageLead` database table with appropriate schema

\- Establish optional relationship with `Lead` table via shared identifier

\- Maintain separation of concerns while enabling future cross-domain analytics

\- Implement RAG (Retrieval-Augmented Generation) for database-aware responses



\#### 1.3 Visual Design System

\*\*Objective:\*\* Establish a professional, enterprise-grade color palette suitable for financial institutions.



\*\*Design Principles:\*\*

\- Minimal color usage (neutral backgrounds + single primary accent)

\- Subtle, meaningful status indicators only where functionally necessary

\- Consistent with banking/financial industry UI standards

\- Professional appearance suitable for client-facing demonstrations



\*\*Anti-patterns to Avoid:\*\*

\- Excessive decorative colors

\- Consumer-app aesthetics

\- Unnecessary visual embellishments



\#### 1.4 Property Type Classification

\*\*Objective:\*\* Replace generic property categories with brokerage-appropriate classifications.



\*\*Updated Property Types:\*\*

\- Single Family Homes

\- Condominiums

\- Multi-Unit Properties

\- Farmhouses

\- Commercial Properties



\*\*Implementation Scope:\*\*

\- Database schema updates (enum types)

\- Form field options

\- Filter components

\- Display formatting across all views

\- Chatbot contextual awareness



\### Development Constraints

\- Maintain existing architectural patterns and component structure

\- Enhance existing functionality without breaking unrelated features

\- Follow established coding conventions and file organization

\- Treat as production-grade feature implementation



---



\## 2. Lead Archive \& Disposition Management



\### Context

Professional mortgage and brokerage operations require systematic tracking of lead outcomes beyond active pipeline stages. This feature enables proper lead lifecycle management and reporting.



\### Requirements



\#### 2.1 Archive Status Classification

\*\*Objective:\*\* Implement comprehensive lead disposition tracking with industry-standard categories.



\*\*Status Categories:\*\*

\- \*\*Sold/Closed\*\* - Transaction successfully completed

\- \*\*Abandoned\*\* - Client disengaged or lost interest

\- \*\*Lost to Competitor\*\* - Client chose alternative provider

\- \*\*On Hold\*\* - Temporarily paused or deferred



\#### 2.2 User Interface Components



\*\*Archive Modal:\*\*

\- Status dropdown with predefined categories

\- Optional notes/reason field for archival context

\- Confirmation workflow to prevent accidental archiving

\- Clear visual distinction from active lead actions



\*\*UI Indicators:\*\*

\- Archive status badges on lead cards

\- Toggle control to show/hide archived leads

\- Dedicated statistics card displaying archived lead count

\- Visual differentiation (grayed/muted styling) for archived records



\*\*Archive Actions:\*\*

\- Archive button in lead detail view (bottom-left, red styling)

\- Unarchive capability for archived leads

\- Audit trail with timestamp and user information



\#### 2.3 Technical Implementation



\*\*Database Schema:\*\*

```

isArchived: Boolean (default: false)

archiveStatus: String (SOLD | ABANDONED | LOST\_TO\_COMPETITOR | ON\_HOLD)

archiveReason: Text (optional notes)

archivedAt: DateTime

archivedBy: String (user identifier)

```



\*\*API Endpoints:\*\*

\- `POST /api/brokerage-leads/archive` - Archive lead with status

\- `DELETE /api/brokerage-leads/archive?leadId={id}` - Unarchive lead

\- `GET /api/brokerage-leads?showArchived={boolean}` - Filter by archive status



\*\*Data Management:\*\*

\- Fetch all leads on initial load

\- Filter archived/active leads in client-side logic

\- Maintain accurate statistics across both states

\- Preserve Scout demographic data during schema migrations



---



\## 3. Color-Coded Lead Temperature System



\### Context

Establish consistent visual language for lead prioritization across both Loan Leads and landing page interfaces, utilizing industry-standard traffic light metaphor.



\### Requirements



\#### 3.1 Temperature Classification

\*\*Objective:\*\* Apply unified color coding system based on propensity scores.



\*\*Color Scheme:\*\*

\- 🔴 \*\*HOT\*\* (Score ≥ 80%) - Red (#EF4444)

&nbsp; - High priority, immediate attention required

&nbsp; - Active background: `bg-red-600`

&nbsp; - Subtle background: `bg-red-50`

&nbsp; - Border: `border-red-300`



\- 🟡 \*\*WARM\*\* (Score 50-79%) - Yellow/Amber (#F59E0B)

&nbsp; - Medium priority, regular follow-up needed

&nbsp; - Active background: `bg-yellow-600`

&nbsp; - Subtle background: `bg-yellow-50`

&nbsp; - Border: `border-yellow-300`



\- 🔵 \*\*COLD\*\* (Score < 50%) - Blue (#3B82F6)

&nbsp; - Lower priority, periodic nurturing

&nbsp; - Active background: `bg-blue-600`

&nbsp; - Subtle background: `bg-blue-50`

&nbsp; - Border: `border-blue-300`



\#### 3.2 Implementation Scope



\*\*UI Components:\*\*

\- Filter button active states

\- Lead card borders and hover states

\- Temperature badge backgrounds

\- Statistics cards in sidebar

\- Landing page dashboard cards



\*\*Visual Hierarchy:\*\*

\- Emoji indicators (🔴🟡🔵) for quick recognition

\- Consistent border-weight and spacing

\- Hover state enhancements

\- Active filter highlighting



---



\## 4. Landing Page Enhancement



\### Context

Transform landing page from placeholder state to functional dashboard displaying real-time lead statistics and providing navigation to both modules.



\### Requirements



\#### 4.1 Module Cards

\*\*Objective:\*\* Create interactive dashboard cards for both Loan Leads and Brokerage Leads modules.



\*\*Loan Leads Card:\*\*

\- Display real-time statistics:

&nbsp; - Hot leads count

&nbsp; - Warm leads count

&nbsp; - Cold leads count

&nbsp; - Total active leads

\- Blue/Purple gradient theme

\- Clickable navigation to `/leads`



\*\*Brokerage Leads Card:\*\*

\- Display real-time statistics:

&nbsp; - Buy leads count

&nbsp; - Sell leads count

&nbsp; - Rent leads count

&nbsp; - Total active leads

\- Green/Emerald gradient theme

\- Clickable navigation to `/brokerage-leads`



\#### 4.2 Data Integration

\- Fetch live data from both API endpoints on page load

\- Display loading states during data retrieval

\- Update counts dynamically without page refresh

\- Exclude archived leads from statistics



\#### 4.3 Visual Design

\- Glassmorphism effects with backdrop blur

\- Gradient backgrounds and glow effects

\- Smooth hover animations (scale, shadow)

\- Professional dark theme with gradient overlay



---



\## 5. UI Refinements \& Polish



\### Context

Series of minor adjustments to optimize user experience and visual consistency across the platform.



\### Requirements



\#### 5.1 Spatial Adjustments

\*\*Objective:\*\* Fine-tune spacing and layout for optimal information density.



\*\*Changes Implemented:\*\*

\- Reduced gap between main content and sidebar (`pr-4` → `pr-2`)

\- Optimized search bar width for better space utilization

\- Balanced filter component sizing

\- Ensured consistent padding and margins



\#### 5.2 Component Enhancements

\*\*Objective:\*\* Improve functionality and user feedback mechanisms.



\*\*Features Added:\*\*

\- Location dropdown filter with dynamic city population

\- Property type column in list view

\- Enhanced stat cards with color coding

\- Improved modal layouts and button positioning



\#### 5.3 Data Seeding

\*\*Objective:\*\* Populate database with realistic test data for demonstration and development.



\*\*Implementation:\*\*

\- Created seed script with 12 diverse brokerage leads

\- Included various property types, locations, and transaction stages

\- Added realistic financial amounts and agent assignments

\- Preserved existing Scout demographic data during migrations



---



\## 6. Version Control \& Deployment



\### Context

Prepare codebase for GitHub repository and production deployment.



\### Requirements



\#### 6.1 Git Workflow

\*\*Commands:\*\*

```bash

git status

git add .

git commit -m "Add brokerage leads module with archive feature and color-coded loan leads"

git push origin main

```



\#### 6.2 Deployment Checklist

\- Verify environment variables are configured

\- Ensure database migrations are applied

\- Test all API endpoints in production environment

\- Validate UI responsiveness across devices

\- Confirm chatbot functionality with production API keys



\#### 6.3 Documentation

\- Update README with new module information

\- Document API endpoints and request/response schemas

\- Create user guide for archive functionality

\- Maintain changelog of feature additions



---



\## Technical Notes



\### Database Considerations

\- Always use `npx prisma db push` for development (not migrate)

\- Back up schema before major changes

\- Preserve Scout demographic fields (475 leads depend on these)

\- Use `fetchAll=true` parameter to retrieve both active and archived leads



\### Code Quality Standards

\- Follow existing component patterns and file structure

\- Maintain TypeScript type safety

\- Implement proper error handling

\- Include loading states for async operations

\- Add appropriate console logging for debugging



\### Performance Optimization

\- Implement client-side filtering for better UX

\- Batch database queries where possible

\- Use proper React hooks and memoization

\- Optimize re-renders with proper dependency arrays



---



\## Success Metrics



\### Functional Requirements Met

✅ Brokerage Leads module fully operational  

✅ Archive system with status tracking  

✅ Color-coded temperature indicators  

✅ Active landing page with live statistics  

✅ AI chatbot integrated for both modules  

✅ No horizontal scrolling on any screen  

✅ Professional, minimal color palette  

✅ Updated property type classifications  



\### Future Enhancements

\- Cross-module lead analytics and reporting

\- Advanced filtering and search capabilities

\- Bulk operations for lead management

\- Email integration for automated outreach

\- Calendar integration for open houses and appointments

\- Mobile-responsive design optimization



---





