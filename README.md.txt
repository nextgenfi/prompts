# Interface Creation Prompts

This repository contains prompts used for creating account interfaces and other UI components.

===============================================================================
PROJECT: BANK ACCOUNT OPENING SYSTEM
===============================================================================

## PROMPT 1: Initial Bank Account System Request
-------------------------------------------------------------------------------

Date: November 2025
Status: Completed

### The Prompt:

I'm a complete beginner, and I want you to help me build from scratch a simple bank account opening system that works on both web and mobile (responsive).

The idea is:
* A customer can open a bank account by filling a form that asks for first name, last name, email address, phone number, and physical address.
* There should be an option to verify the email (for example, send a verification code or mark it verified).
* If the customer visits the bank, an employee can open the account using the same interface on their system.
* If the customer is at home, they can open it themselves using a mobile-friendly version or a mobile app interface.

I want you to:
1. Explain what folder structure to create (like `frontend`, `backend`, etc.).
2. Tell me exactly what technologies to install and why (for example: React, Node.js, Express, etc.).
3. Generate all the code files step-by-step — from initializing the project, to frontend form creation, backend API setup, database connection (you can use something simple like MongoDB or even a temporary local JSON), and running it locally.
4. Make the design clean and modern — you can use something like Tailwind CSS for styling.
5. Include email verification functionality (even if it's just simulated for now).
6. Make the frontend fully responsive so it looks good on both desktop (for bank employees) and mobile (for customers).
7. Explain how to run and test the app locally.

Treat me like a beginner — explain each step clearly (like why we're writing certain commands, what each file does, and where to paste code).

Start by outlining the complete plan (technologies, folder structure, and overview), then proceed step by step to build everything.


## TECHNOLOGIES USED
-------------------------------------------------------------------------------

### Frontend (Client-Side):
- HTML5 - Structure
- CSS3 - Styling (with Flexbox & Grid)
- Vanilla JavaScript - Logic
- Google Fonts (Inter) - Typography
- Fetch API - HTTP requests

### Backend (Server-Side):
- Node.js - JavaScript runtime
- Express.js - Web framework for API routes
- Multer - File upload handling
- Tesseract.js - OCR (Optical Character Recognition)
- dotenv - Environment variable management
- CORS - Cross-Origin Resource Sharing

### Third-Party Services:
- SendGrid - Email delivery
- Twilio Verify - SMS OTP


## PROMPT 2: SendGrid Email Integration
-------------------------------------------------------------------------------

Date: November 2025
Status: Completed

### The Question:

Okay it's working but what if I wanna connect verify email to SendGrid which will send an email to the user? Can it be done?

### Context:
- System was working with basic email verification
- Wanted to integrate real email sending via SendGrid

### Outcome:
Successfully integrated SendGrid API for real email delivery with verification codes.


## PROMPT 3: Identity Verification with Document Upload
-------------------------------------------------------------------------------

Date: November 2025
Status: Completed

### The Prompt:

okay the task has now been modified now i also want an identity like a drivers license and adhaar and it will be used to verify the first name last name address to verify the identity and if it fails to authenticate it will say transaction failed

### Context:
This prompt came after the email and SMS verification was already implemented. The goal was to add document-based identity verification using OCR (Optical Character Recognition) to extract text from uploaded documents.

### Features Requested:
- Upload Aadhaar card image
- Upload Driver's License image
- Extract text from documents using OCR
- Match extracted Name and Address with form data
- Display "Identity Verified" if data matches
- Display "Transaction Failed" if data doesn't match

### Technologies Added:
- Tesseract.js for OCR
- Multer for file uploads
- Express.js backend for processing


## PROJECT SUMMARY
-------------------------------------------------------------------------------

### Key Features Built:
1. ✅ Responsive form (mobile + desktop)
2. ✅ Email verification with SendGrid
3. ✅ SMS OTP verification with Twilio
4. ✅ Document upload (Aadhaar, Driver's License)
5. ✅ OCR text extraction
6. ✅ Identity matching and verification
7. ✅ Transaction success/failure handling

### Learning Outcomes:
- Full-stack development (Frontend + Backend)
- API integration (SendGrid, Twilio)
- File handling and OCR
- Form validation and user flows
- Responsive design principles


===============================================================================
END OF DOCUMENT
===============================================================================