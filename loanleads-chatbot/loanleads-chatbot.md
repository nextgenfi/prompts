\# AI Copilot for Loan Leads - Complete Prompt Engineering Documentation



\## Project Overview



\*\*Feature:\*\* AI-Powered Loan Officer Copilot  

\*\*Platform:\*\* NextGenBank Mortgage Lending Application  

\*\*Technology Stack:\*\* Next.js 14, OpenAI GPT-4o-mini, Prisma ORM, PostgreSQL with pgvector  

\*\*Date:\*\* January 2026



---



\## Development Prompts



The following prompts were used sequentially to build the AI Copilot feature from UI design to full implementation.



---



\### Prompt 1: Initial UI Design



```

You already have context about the mortgage/lending platform and the Loan Leads screen.



For this task, focus only on designing the chatbot UI, not backend logic or AI behavior.



Add an AI Copilot / Chatbot interface to the Loan Leads screen with the following requirements:



\- The chatbot should be fixed to the bottom-right corner of the screen

\- It should be collapsible and expandable

\- Use a clean, modern fintech-style design consistent with the rest of the UI



Include:

\- A chat header (e.g. "AI Copilot")

\- Message bubbles for user and assistant

\- A text input field with a send button

\- Optional quick-action suggestions (e.g. "Why is this lead pending?", "Summarize lead")



This chatbot is intended to assist loan officers by explaining lead status and suggesting 

next steps, but no backend logic, AI processing, or data integration should be implemented yet.

```



\*\*Purpose:\*\* Establish the visual foundation and UI components for the chatbot interface.



---



\### Prompt 2: Lead Selection Context



```

The AI Copilot should be able to answer questions about a selected lead.



Requirements:

\- When a user clicks on a lead card, that lead becomes the "selected lead" for AI context

\- Show a visual indicator when a lead is selected for AI analysis

\- The chatbot should know which lead the user is asking about

\- Add a "Clear Selection" option to deselect the lead



The AI should be able to answer questions like:

\- "Summarize this lead"

\- "Why is this pending?"

\- "What should I do next?"

```



\*\*Purpose:\*\* Enable contextual conversations about specific leads.



---



\### Prompt 3: Backend API Integration



```

Create the backend API for the AI Copilot at /api/copilot.



Requirements:

\- Accept POST requests with message, leadId, and conversation history

\- Fetch lead data from database using Prisma

\- Generate contextual information about the lead (status, score, temperature, etc.)

\- Use OpenAI GPT-4o-mini to generate responses

\- Include system prompt that defines the AI's role and capabilities



The AI should understand:

\- Lead scoring system (HOT/WARM/COLD based on 1325 point scale)

\- Lead statuses (NEW, CONTACTED, QUALIFIED, DOCUMENT\_REQUIRED, PENDING, APPROVED, CONVERTED)

\- Best practices for loan officers

```



\*\*Purpose:\*\* Implement the core AI processing logic and database integration.



---



\### Prompt 4: Action Confirmation Workflow



```

Add action confirmation capability to the AI Copilot.



When the AI suggests an action like:

\- Send document reminder

\- Schedule follow-up call

\- Escalate to manager



It should:

\- Ask for user confirmation before executing

\- Track pending actions in the conversation state

\- Confirm or cancel based on user response

\- Log executed actions with timestamps

```



\*\*Purpose:\*\* Enable safe, user-confirmed actions through the chatbot.



---



\### Prompt 5: RAG Knowledge Base Integration



```

Connect the AI Copilot to the existing RAG knowledge base.



The copilot should be able to answer questions about:

\- Company policies and procedures

\- Loan products and guidelines

\- Compliance requirements

\- Any documents uploaded to the knowledge base



Implementation:

\- Use the existing embedding generation function

\- Search the Chunk table using pgvector similarity

\- Include relevant knowledge chunks in the GPT context

\- Cite sources when using knowledge base information

```



\*\*Purpose:\*\* Extend the AI's knowledge beyond lead data to include organizational documents.



---



\### Prompt 6: Full Database Access



```

The AI Copilot should be able to answer ANY question about the leads database.



Current limitation: It only sees the selected lead.



Required capability:

\- "How many hot leads do we have?"

\- "List all leads with PENDING status"

\- "Which lead has the highest loan amount?"

\- "What's the total pipeline value?"

\- "Compare hot vs cold leads"

\- "Show me leads from referral source"



Solution: Fetch ALL leads from database and include as JSON context in every API call.

Include summary statistics for efficient querying.

```



\*\*Purpose:\*\* Enable comprehensive database querying and analytics through natural language.



---



\## Final System Prompt



The following system prompt defines the AI Copilot's complete behavior:



```

You are an AI Copilot for loan officers at NextGenBank. You have FULL ACCESS to the leads database.



YOU CAN ANSWER:

\- "How many hot leads?" → Count from database

\- "List all pending leads" → Filter and list

\- "Which lead has highest loan amount?" → Find max

\- "Show me leads from referrals" → Filter by source

\- "What's the total pipeline value?" → Sum amounts

\- "Compare hot vs cold leads" → Analyze

\- ANY question about leads, stats, comparisons, filters, etc.



ALSO CAN:

\- Analyze specific selected lead

\- Search knowledge base for policies/procedures

\- Suggest actions (follow-up, remind, escalate)



SCORING:

\- HOT 🔴: ≥80% (≥1060 pts) - Contact within 24hrs

\- WARM 🟡: 50-79% (663-1059 pts) - 48-72hrs  

\- COLD 🔵: <50% (<663 pts) - Nurture



STATUSES: NEW → CONTACTED → QUALIFIED → DOCUMENT\_REQUIRED → PENDING → APPROVED → CONVERTED



RULES:

\- Use the ALL LEADS DATABASE to answer aggregate questions

\- Use SELECTED LEAD for specific lead questions

\- Use KNOWLEDGE BASE for policy questions

\- Be concise, use numbers and bullet points

\- Always be accurate - count from the actual data provided

```



---



\## Architecture Overview



```

User Query

&nbsp;   │

&nbsp;   ▼

┌─────────────────────────────────────┐

│         API: /api/copilot           │

├─────────────────────────────────────┤

│  1. Fetch ALL leads from database   │

│  2. RAG search knowledge base       │

│  3. Get selected lead context       │

│  4. Build GPT prompt with all data  │

│  5. Return AI response              │

└─────────────────────────────────────┘

&nbsp;   │

&nbsp;   ▼

GPT-4o-mini Response

```



---



\## Context Injection Strategy



\### Database Context



All leads are fetched and converted to structured JSON:



```typescript

const leadsWithTemp = leads.map(lead => ({

&nbsp; id: lead.id,

&nbsp; name: lead.fullName,

&nbsp; email: lead.email,

&nbsp; phone: lead.phoneNumber,

&nbsp; status: lead.status,

&nbsp; temperature: getTemperature(lead.leadScore),

&nbsp; score: lead.leadScore,

&nbsp; percentage: Math.round((lead.leadScore / 1325) \* 100),

&nbsp; loanType: lead.loanType,

&nbsp; propertyType: lead.propertyType,

&nbsp; loanAmount: lead.estimatedLoanAmount,

&nbsp; source: lead.source,

&nbsp; city: lead.city,

&nbsp; state: lead.state,

&nbsp; lifeEvent: lead.lifeEvent,

&nbsp; daysSinceContact: lead.daysSinceFirstContact,

&nbsp; isExistingCustomer: lead.isExistingCustomer,

&nbsp; hasRecentInquiry: lead.hasRecentInquiry,

&nbsp; hasCampaignResponse: lead.hasCampaignResponse,

&nbsp; hasBranchVisit: lead.hasBranchVisit,

&nbsp; campaign: lead.campaign?.name,

&nbsp; createdAt: lead.createdAt,

}));

```



\### Summary Statistics



Pre-calculated statistics included in context:



```

SUMMARY:

\- Total: {count}

\- HOT 🔴: {hotCount}

\- WARM 🟡: {warmCount}

\- COLD 🔵: {coldCount}

\- Pipeline Value: ${totalValue}



BY STATUS: {statusBreakdown}

BY SOURCE: {sourceBreakdown}

BY LOAN TYPE: {loanTypeBreakdown}

```



\### Selected Lead Context



When user selects a specific lead:



```

SELECTED LEAD (user clicked on this one):

• Name: {fullName}

• Email: {email} | Phone: {phone}

• Status: {status}

• Temperature: {temperature} ({percentage}%, {score}/1325 pts)

• Loan: {loanType} | Property: {propertyType}

• Amount: ${amount}

• Source: {source}

• Location: {city}, {state}

• Days Since Contact: {days}

• Life Event: {lifeEvent}

• Engagement: {engagementIndicators}

```



---



\## RAG Integration



\### Embedding Generation



```typescript

async function generateEmbedding(text: string): Promise<number\[]> {

&nbsp; const response = await openai.embeddings.create({

&nbsp;   model: 'text-embedding-3-small',

&nbsp;   input: text,

&nbsp;   dimensions: 384

&nbsp; });

&nbsp; return response.data\[0].embedding;

}

```



\### Vector Search Query



```sql

SELECT

&nbsp; c.text,

&nbsp; c."sourceDocument",

&nbsp; 1 - (c.embedding <=> ${embedding}::vector) as similarity

FROM "Chunk" c

ORDER BY c.embedding <=> ${embedding}::vector

LIMIT 5

```



---



\## Lead Scoring Reference



| Category | Max Points | Breakdown |

|----------|------------|-----------|

| Loan Type | 200 | Renewal=200, Refinance=150, New=100 |

| Lead Source | 200 | Referral=200, Walk-in=150, Campaign=100, External=50 |

| Existing Customer | 200 | Yes=200, No=0 |

| Property Type | 150 | Commercial=150, Multi-family=125, Residential=100, Land=75 |

| Loan Amount | 150 | >$500K=150, $300-500K=100, $100-300K=75, <$100K=50 |

| Life Event | 150 | Relocation=150, Marriage=120, Child=100, Retirement=80 |

| Engagement | 175 | Branch Visit=125, Campaign Response=25, Recent Inquiry=25 |

| Recency | 100 | <7 days=100, 7-14=75, 14-30=50, >30=0 |



\*\*Total Maximum Score: 1325 points\*\*



\### Temperature Classification



\- \*\*HOT 🔴\*\*: Score ≥80% (≥1060 points) - High priority, contact within 24 hours

\- \*\*WARM 🟡\*\*: Score 50-79% (663-1059 points) - Medium priority, 48-72 hours

\- \*\*COLD 🔵\*\*: Score <50% (<663 points) - Low priority, nurture over time



---



\## Action Confirmation Workflow



\### Supported Actions



| Action Type | Trigger Keywords | Description |

|-------------|------------------|-------------|

| SEND\_REMINDER | "send reminder", "remind" | Send document reminder |

| FOLLOW\_UP | "follow up", "schedule call" | Schedule follow-up |

| ESCALATE | "escalate" | Escalate to manager |



\### Confirmation Flow



```

AI: "I recommend sending a document reminder. Would you like me to proceed?"

User: "Yes"

AI: "✅ Done! 'Send reminder' logged."

```



---



\## Sample Queries



| User Query | Data Source | Response Type |

|------------|-------------|---------------|

| "How many hot leads?" | Database | Count |

| "List pending leads" | Database | Filtered list |

| "Summarize this lead" | Selected Lead | Analysis |

| "What is FHA loan limit?" | Knowledge Base | Policy info |

| "What should I do next?" | Selected Lead | Recommendation |

| "Total pipeline value?" | Database | Calculation |



---



\## Message Context Structure



```typescript

const messages = \[

&nbsp; { role: 'system', content: SYSTEM\_PROMPT },

&nbsp; { role: 'system', content: allLeadsContext },      // Full database

&nbsp; { role: 'system', content: knowledgeContext },     // RAG results

&nbsp; { role: 'system', content: selectedLeadContext },  // If selected

&nbsp; ...conversationHistory,                            // Last 10 messages

&nbsp; { role: 'user', content: userMessage }

];

```



---



\## API Configuration



```typescript

const completion = await openai.chat.completions.create({

&nbsp; model: 'gpt-4o-mini',

&nbsp; messages,

&nbsp; max\_tokens: 800,

&nbsp; temperature: 0.7,

});

```



---



\## File Structure



```

app/

├── api/

│   └── copilot/

│       └── route.ts          # Main copilot API

├── leads/

│   └── page.tsx              # Leads page with copilot UI

lib/

├── prisma.ts                 # Database client

```



---



\## Dependencies



```json

{

&nbsp; "openai": "^4.x",

&nbsp; "prisma": "@prisma/client",

&nbsp; "next": "14.x"

}

```



---



\## Environment Variables



```

OPENAI\_API\_KEY=your\_openai\_api\_key

DATABASE\_URL=your\_postgresql\_connection\_string

```



\-

