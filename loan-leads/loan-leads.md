\*\*\*\*PROMPT 1\*\*\*\*

You already have context about the mortgage / loan lending platform we are building.

We have finalized a terminology update and need to rename “Lead Management” to “Loan Leads” across the product.

Please update:

\* Page titles and headers

\* Sidebar / navigation labels

\* Any visible UI text references to “Lead Management”

This is a naming change only. Do not modify features, workflows, logic, or UI layout.



\*\*\*\*PROMPT 2\*\*\*\*

You already have context about the mortgage / lending platform.

For this task, focus only on the UI. Do not implement backend logic or data handling yet.

Using the attached images as reference, update the UI to match the following:

1\. Keep the existing Loan Leads list UI (cards with HOT / WARM / COLD, lead details, edit/delete actions) exactly as shown in the reference image.

2\. Add a right-side panel similar to the reference pipeline image that contains:

&nbsp;  \* A section titled “Today’s Priorities” at the top

&nbsp;  \* A list-style layout showing recommended actions (static UI placeholders for now)

3\. Add an AI Copilot / Chatbot UI fixed to the bottom-right corner of the screen, visually similar to the reference image:

&nbsp;  \* Collapsible chat window

&nbsp;  \* Message bubbles

&nbsp;  \* Input box with send button

4\. This task is strictly about layout, positioning, spacing, and visual hierarchy.

&nbsp;  \* Use clean, modern fintech-style design

&nbsp;  \* No backend logic

&nbsp;  \* No rules implementation

&nbsp;  \* No AI behavior yet

Match the structure and placement from the reference images, adapting them to the Loan Leads screen. also renaming of lead management is already achieved kindly skip that step





\*\*\*\*prompt 3\*\*\*\*

Now implement the chatbot functionality with the following constraints and goals:



Purpose



The chatbot acts as a Loan Officer Copilot that:



Explains lead status



Answers questions about a selected loan lead



Suggests next actions



Assists, but does not make decisions



Scope \& Rules



The chatbot must not approve, reject, or modify loans autonomously



It must not invent data



It must only respond using data explicitly provided to it as input



Any action (e.g. sending reminders, updating status) must require explicit user confirmation



Input Context (provided by backend)



Assume the chatbot receives structured data in JSON, such as:



Selected lead details (status, stage, days pending, missing documents, score)



Triggered system rules (e.g. DOC\_PENDING\_48H)



User role (Loan Officer)



Expected Capabilities



Implement responses for:



“Why is this lead pending?”



“Summarize this lead”



“What is blocking approval?”



“What should I do next?”



The chatbot should:



Clearly explain reasons in plain language



Reference missing steps or delays



Suggest safe next actions (follow-up, document reminder, review)



Action Handling



When the user requests an action:



Respond with a proposed action summary



Ask for confirmation before execution



Do not execute actions automatically



Output Format



When appropriate, return structured responses such as:



{

&nbsp; "message": "The application is pending due to missing income documents for 3 days.",

&nbsp; "suggested\_action": "Send document reminder",

&nbsp; "requires\_confirmation": true

}



Important



This chatbot assists humans; it does not replace decision-making



All explanations must be deterministic and auditable



No UI changes are required

