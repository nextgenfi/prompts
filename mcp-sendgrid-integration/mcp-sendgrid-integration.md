PROJECT: MCP SERVER FOR EMAIL AUTOMATION WITH SENDGRID



PROMPT 1: Initial MCP Server Setup Request



The Prompt:

You are an expert AI developer and I want you to set up a complete MCP project that integrates with Twilio SendGrid for sending templated emails.

Create detailed step-by-step instructions and all the code files required. Assume I'm starting completely from scratch on Windows.

Here's what I need:



Create a new folder on my Desktop called "mcp-twilio-email".

Inside, set up a Node.js MCP server project (with proper package.json, index.js, and configuration files).

Use @modelcontextprotocol/sdk for defining the MCP tool.

Implement an MCP tool named "send\_email" that:



Uses Twilio SendGrid API.

Takes inputs: to, subject, and templateId.

Optionally takes a dynamicData object for replacing placeholders inside the template.





Add clear setup instructions for:



Installing dependencies.

Setting environment variables (for example, SENDGRID\_API\_KEY).

How to get a Twilio SendGrid account and create an email template.





Add example code for calling the tool (both via Node.js script and using an LLM through MCP).

Include any necessary explanations for debugging or testing the integration.

Use best practices for folder structure, code readability, and security (don't hardcode keys).



Output everything as if you're guiding a beginner — include each file's content with explanations.

Finally, summarize what the project does and how to test it.

Context:

Starting from scratch on Windows, needed to build an MCP server that could send templated emails through SendGrid.



PROMPT 2: Troubleshooting - Testing MCP Tools



The Question:

You tell me what should I ask in new chat to know \[if the MCP server is working]?

Context:

After setting up the MCP server and Claude Desktop config, needed to verify that the integration was working properly.

Test Questions Suggested:



"Do you have a send\_email tool?"

"Can you send emails using SendGrid?"

"List all your available tools"

"What MCP servers are you connected to?"



PROMPT 3: Configuration File Setup



The Issue:

How to create and configure the claude\_desktop\_config.json file properly.

Key Steps Covered:



Finding the config file location: %APPDATA%\\Claude\\

Creating the config file if it doesn't exist

Proper JSON structure for MCP servers

Setting environment variables (SENDGRID\_API\_KEY, FROM\_EMAIL)

Specifying correct file paths



TECHNOLOGIES USED



Core Technologies:



Node.js - JavaScript runtime

@modelcontextprotocol/sdk - MCP server implementation

@sendgrid/mail - SendGrid email API client

dotenv - Environment variable management



Configuration:



Claude Desktop App - Client application

claude\_desktop\_config.json - MCP server configuration

Environment Variables - API key and sender email management



APIs \& Services:



Twilio SendGrid - Email delivery service

SendGrid Dynamic Templates - Email template system

