\# NextGenBank Analytics - Development Prompts



This document contains the refined prompts used to build the NextGenBank Analytics POC - an AI-powered customer segmentation and marketing campaign system for mortgage lending.



---



\## Table of Contents

1\. \[Initial Project Setup](#1-initial-project-setup)

2\. \[ML Implementation](#2-ml-implementation)

3\. \[Real-Time ML Integration](#3-real-time-ml-integration)

4\. \[UI/UX Enhancement](#4-uiux-enhancement)

5\. \[Documentation \& Deployment](#5-documentation--deployment)



---



\## 1. Initial Project Setup



\### Prompt 1.1: Project Requirements \& Architecture



```

Build a proof-of-concept (POC) for a customer segmentation and marketing campaign

system for mortgage lending with the following specifications:



Technology Stack:

\- Frontend: Next.js 14 with App Router

\- Language: TypeScript

\- ORM: Prisma

\- Database: PostgreSQL

\- Styling: Tailwind CSS



Core Features:

1\. Customer data import from CSV

2\. Customer segmentation based on financial attributes

3\. Marketing campaign prioritization

4\. Dashboard with analytics and visualizations

5\. Lead management with filtering and export capabilities



Data Model Requirements:

\- Customer demographics (name, age, contact info, address)

\- Property information (value, equity, mortgage amount)

\- Financial scores (readiness, propensity, intent signals)

\- Segmentation outputs (segment name, NBA scores, priority)

```



---



\## 2. ML Implementation



\### Prompt 2.1: Transition to Predictive Analytics



```

Replace the rule-based segmentation system with predictive machine learning

using Python. The solution should be suitable for running marketing campaigns

with the following requirements:



ML Approach:

\- Implement customer segmentation using unsupervised learning

\- Build propensity models for product recommendations

\- Create a Next-Best-Action (NBA) scoring engine



Products to Model:

\- Refinance

\- HELOC (Home Equity Line of Credit)

\- Reverse Mortgage

\- New Home Purchase



The ML pipeline should:

1\. Process and normalize customer data

2\. Discover natural customer segments

3\. Predict product propensity scores

4\. Generate actionable recommendations with priority levels

```



\### Prompt 2.2: Algorithm Selection



```

Select and implement the most appropriate machine learning algorithms for

mortgage marketing campaigns:



Requirements:

\- Segmentation: Discover natural customer groupings without predefined labels

\- Propensity: Predict likelihood of customer response to each product offer

\- Scoring: Combine ML predictions with business rules for final recommendations



Considerations:

\- Interpretability for bank compliance

\- Performance on tabular financial data

\- Ability to handle imbalanced classes

\- Real-time inference capability

```



---



\## 3. Real-Time ML Integration



\### Prompt 3.1: Real-Time Prediction System



```

Implement real-time ML predictions for bank employees with the following features:



Architecture:

\- Python FastAPI backend serving ML models

\- REST API endpoints for predictions

\- Integration with Next.js frontend via API routes



User Experience:

\- Visual ML status indicator in the navigation bar

\- On-demand scoring for individual customers

\- Batch scoring capability for campaign lists

\- Display of ML-generated insights (segment, propensity, recommendation)



The system should allow bank employees to:

1\. See the current status of the ML engine

2\. Request fresh predictions for any customer

3\. Compare database scores with real-time ML scores

4\. Export ML-enriched lead lists for campaigns

```



\### Prompt 3.2: ML API Development



```

Create a FastAPI application to serve the trained ML models with:



Endpoints:

\- GET /status - Health check and model loading status

\- POST /predict - Single customer prediction

\- POST /predict/batch - Batch predictions for multiple customers



Response Format:

\- Customer segment assignment

\- Product propensity scores (0-1 probability)

\- NBA scores for each product (0-100)

\- Priority classification (hot/warm/nurture/low)

\- Top recommended action



Error Handling:

\- Graceful degradation when models unavailable

\- Input validation and sanitization

\- Meaningful error messages

```



---



\## 4. UI/UX Enhancement



\### Prompt 4.1: Professional Banking Interface



```

Redesign the user interface to match professional banking industry standards

while maintaining all existing functionality:



Design Requirements:

\- Premium, enterprise-grade aesthetic

\- Deep navy blue and gold color scheme (typical of financial institutions)

\- Clean typography with proper visual hierarchy

\- Subtle shadows, gradients, and rounded corners

\- Smooth transitions and hover effects



Components to Update:

1\. Navigation bar with logo, nav links, ML status, and user profile

2\. Dashboard with metric cards, charts, and quick actions

3\. Lead management table with filters and pagination

4\. Customer detail page with comprehensive data display

5\. Campaign manager with ML scoring integration

6\. Segment analysis with comparison visualizations



Accessibility:

\- High contrast for readability

\- Clear visual indicators for interactive elements

\- Responsive design for various screen sizes

```



---





