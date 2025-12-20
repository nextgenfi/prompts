===============================================================================

INITIAL REQUEST - EXPANDING FILE TYPE SUPPORT

===============================================================================



Prompt:

"My mentor asked me to make the document library support all types of 

documents, not just TXT. I want it to work with PDF, XLS, and DOC files. 

Modify the code accordingly."



Implementation Requirements:

\- Support PDF files (.pdf)

\- Support Word documents (.doc, .docx)

\- Support Excel spreadsheets (.xls, .xlsx)

\- Maintain existing TXT file support

\- Extract text from all formats for embedding generation



===============================================================================

STEP 1: INSTALLING REQUIRED DEPENDENCIES

===============================================================================



Terminal Commands:

```powershell

npm install mammoth xlsx unpdf

```



Package Purposes:

\- \*\*mammoth\*\* - Extracts text from Word documents (.doc, .docx)

\- \*\*xlsx\*\* - Processes Excel spreadsheets (.xls, .xlsx)

\- \*\*unpdf\*\* - Extracts text from PDF files (serverless-compatible)



Note: Initially tried pdf-parse, then pdfjs-dist, but settled on unpdf due to

compatibility with Next.js serverless environment and lack of native dependencies.



===============================================================================

STEP 2: UPDATED UPLOAD ROUTE CODE

===============================================================================



File: `app/api/knowledge/upload/route.ts`



```typescript

import { NextResponse } from 'next/server'

import { put } from '@vercel/blob'

import { prisma } from '@/lib/prisma'

import OpenAI from 'openai'

import { extractText } from 'unpdf'

import mammoth from 'mammoth'

import \* as XLSX from 'xlsx'



const openai = new OpenAI({

&nbsp; apiKey: process.env.OPENAI\_API\_KEY

})



// Supported file extensions

const SUPPORTED\_EXTENSIONS = \['.txt', '.pdf', '.doc', '.docx', '.xls', '.xlsx']



function getFileExtension(filename: string): string {

&nbsp; return filename.toLowerCase().substring(filename.lastIndexOf('.'))

}



// Extract text based on file type

async function extractTextFromFile(

&nbsp; buffer: ArrayBuffer, 

&nbsp; extension: string

): Promise<string> {

&nbsp; switch (extension) {

&nbsp;   case '.txt':

&nbsp;     return new TextDecoder().decode(buffer)

&nbsp;   

&nbsp;   case '.pdf':

&nbsp;     const { text } = await extractText(buffer)

&nbsp;     return text

&nbsp;   

&nbsp;   case '.doc':

&nbsp;   case '.docx':

&nbsp;     const result = await mammoth.extractRawText({ buffer })

&nbsp;     return result.value

&nbsp;   

&nbsp;   case '.xls':

&nbsp;   case '.xlsx':

&nbsp;     const workbook = XLSX.read(buffer, { type: 'array' })

&nbsp;     let excelText = ''

&nbsp;     workbook.SheetNames.forEach(sheetName => {

&nbsp;       const sheet = workbook.Sheets\[sheetName]

&nbsp;       const csvText = XLSX.utils.sheet\_to\_csv(sheet)

&nbsp;       excelText += `\\n\\n=== Sheet: ${sheetName} ===\\n${csvText}`

&nbsp;     })

&nbsp;     return excelText

&nbsp;   

&nbsp;   default:

&nbsp;     throw new Error(`Unsupported file type: ${extension}`)

&nbsp; }

}



// Clean document text - remove headers/titles

function cleanDocumentText(text: string): string {

&nbsp; const lines = text.split('\\n')

&nbsp; let startIndex = 0

&nbsp; 

&nbsp; for (let i = 0; i < Math.min(lines.length, 10); i++) {

&nbsp;   const line = lines\[i].trim().toUpperCase()

&nbsp;   if (line.includes('POLICY DOCUMENT') || 

&nbsp;       line.includes('COVERAGE GUIDE') ||

&nbsp;       line.includes('EDITION') ||

&nbsp;       line === '' ||

&nbsp;       (line.length < 50 \&\& !line.includes('SECTION'))) {

&nbsp;     startIndex = i + 1

&nbsp;   } else {

&nbsp;     break

&nbsp;   }

&nbsp; }

&nbsp; 

&nbsp; return lines.slice(startIndex).join('\\n').trim()

}



// Chunk text into smaller pieces with overlap

function chunkText(text: string, chunkSize: number = 1000, overlap: number = 200): string\[] {

&nbsp; const chunks: string\[] = \[]

&nbsp; let start = 0

&nbsp; const cleanedText = cleanDocumentText(text)



&nbsp; while (start < cleanedText.length) {

&nbsp;   const end = Math.min(start + chunkSize, cleanedText.length)

&nbsp;   const chunk = cleanedText.slice(start, end).trim()



&nbsp;   if (chunk.length > 50) {

&nbsp;     chunks.push(chunk)

&nbsp;   }



&nbsp;   start += chunkSize - overlap

&nbsp; }



&nbsp; return chunks

}



// Generate embeddings using OpenAI

async function generateEmbedding(text: string): Promise<number\[]> {

&nbsp; try {

&nbsp;   const response = await openai.embeddings.create({

&nbsp;     model: 'text-embedding-3-small',

&nbsp;     input: text,

&nbsp;     dimensions: 384

&nbsp;   })

&nbsp;   return response.data\[0].embedding

&nbsp; } catch (error) {

&nbsp;   console.error('Embedding error:', error)

&nbsp;   throw error

&nbsp; }

}



export async function POST(request: Request) {

&nbsp; try {

&nbsp;   const formData = await request.formData()

&nbsp;   const files = formData.getAll('files') as File\[]



&nbsp;   if (!files || files.length === 0) {

&nbsp;     return NextResponse.json(

&nbsp;       { error: 'No files provided' },

&nbsp;       { status: 400 }

&nbsp;     )

&nbsp;   }



&nbsp;   interface FileResult {

&nbsp;     filename: string

&nbsp;     success: boolean

&nbsp;     chunks?: number

&nbsp;     error?: string

&nbsp;   }



&nbsp;   const results: FileResult\[] = \[]

&nbsp;   let successful = 0

&nbsp;   let failed = 0



&nbsp;   for (const file of files) {

&nbsp;     try {

&nbsp;       const extension = getFileExtension(file.name)

&nbsp;       

&nbsp;       // Validate file type

&nbsp;       if (!SUPPORTED\_EXTENSIONS.includes(extension)) {

&nbsp;         results.push({

&nbsp;           filename: file.name,

&nbsp;           success: false,

&nbsp;           error: `Unsupported file type: ${extension}`

&nbsp;         })

&nbsp;         failed++

&nbsp;         continue

&nbsp;       }



&nbsp;       // Read file as ArrayBuffer

&nbsp;       const arrayBuffer = await file.arrayBuffer()

&nbsp;       

&nbsp;       // Extract text based on file type

&nbsp;       const text = await extractTextFromFile(arrayBuffer, extension)



&nbsp;       if (!text || text.trim().length === 0) {

&nbsp;         results.push({

&nbsp;           filename: file.name,

&nbsp;           success: false,

&nbsp;           error: 'No text content extracted'

&nbsp;         })

&nbsp;         failed++

&nbsp;         continue

&nbsp;       }



&nbsp;       // Upload file to Vercel Blob

&nbsp;       const blob = await put(file.name, file, {

&nbsp;         access: 'public',

&nbsp;       })



&nbsp;       // Create document record

&nbsp;       const document = await prisma.document.create({

&nbsp;         data: {

&nbsp;           filename: file.name,

&nbsp;           url: blob.url,

&nbsp;         }

&nbsp;       })



&nbsp;       // Chunk the text

&nbsp;       const chunks = chunkText(text)



&nbsp;       // Generate embeddings and store chunks

&nbsp;       let chunkCount = 0

&nbsp;       for (const chunk of chunks) {

&nbsp;         const embedding = await generateEmbedding(chunk)

&nbsp;         

&nbsp;         await prisma.chunk.create({

&nbsp;           data: {

&nbsp;             sourceDocument: file.name,

&nbsp;             content: chunk,

&nbsp;             embedding: `\[${embedding.join(',')}]`,

&nbsp;           }

&nbsp;         })

&nbsp;         chunkCount++

&nbsp;       }



&nbsp;       results.push({

&nbsp;         filename: file.name,

&nbsp;         success: true,

&nbsp;         chunks: chunkCount

&nbsp;       })

&nbsp;       successful++



&nbsp;     } catch (error: any) {

&nbsp;       console.error(`Error processing ${file.name}:`, error)

&nbsp;       results.push({

&nbsp;         filename: file.name,

&nbsp;         success: false,

&nbsp;         error: error.message

&nbsp;       })

&nbsp;       failed++

&nbsp;     }

&nbsp;   }



&nbsp;   return NextResponse.json({

&nbsp;     message: `Uploaded ${successful} file(s)${failed > 0 ? `, ${failed} failed` : ''}`,

&nbsp;     uploaded: successful,

&nbsp;     failed,

&nbsp;     results,

&nbsp;     supportedTypes: SUPPORTED\_EXTENSIONS

&nbsp;   })



&nbsp; } catch (error: any) {

&nbsp;   console.error('Upload error:', error)

&nbsp;   return NextResponse.json(

&nbsp;     { error: error.message || 'Failed to upload and process files' },

&nbsp;     { status: 500 }

&nbsp;   )

&nbsp; }

}

```



===============================================================================

STEP 3: UPDATING FRONTEND TO ACCEPT NEW FILE TYPES

===============================================================================



Prompt:

"When I click on 'drop your files here' it is only showing .pdf and .txt 

files and no docx or xlsx"



Solution:

Update the file input accept attribute in `app/knowledge/page.tsx`



Change From:

```tsx

<input

&nbsp; type="file"

&nbsp; id="file-upload"

&nbsp; multiple

&nbsp; accept=".pdf,.txt"

&nbsp; onChange={handleFileInput}

&nbsp; disabled={uploading}

&nbsp; className="absolute inset-0 w-full h-full opacity-0 cursor-pointer disabled:cursor-not-allowed"

/>

```



Change To:

```tsx

<input

&nbsp; type="file"

&nbsp; id="file-upload"

&nbsp; multiple

&nbsp; accept=".txt,.pdf,.doc,.docx,.xls,.xlsx"

&nbsp; onChange={handleFileInput}

&nbsp; disabled={uploading}

&nbsp; className="absolute inset-0 w-full h-full opacity-0 cursor-pointer disabled:cursor-not-allowed"

/>

```



===============================================================================

STEP 4: PDF PROCESSING CHALLENGES \& SOLUTIONS

===============================================================================



Challenge 1: Module Not Found Error

```

Error: Module not found: Can't resolve 'fs'

```



Root Cause:

\- pdf-parse uses Node.js filesystem modules

\- Not compatible with Next.js edge runtime

\- Vercel serverless functions have native module limitations



Solution Attempted 1: Switch to pdfjs-dist

```powershell

npm install pdfjs-dist

```



Code:

```typescript

import \* as pdfjsLib from 'pdfjs-dist/build/pdf.mjs'



async function extractPDFText(buffer: ArrayBuffer): Promise<string> {

&nbsp; const pdf = await pdfjsLib.getDocument({ data: buffer }).promise

&nbsp; let fullText = ''

&nbsp; 

&nbsp; for (let i = 1; i <= pdf.numPages; i++) {

&nbsp;   const page = await pdf.getPage(i)

&nbsp;   const textContent = await page.getTextContent()

&nbsp;   const pageText = textContent.items.map((item: any) => item.str).join(' ')

&nbsp;   fullText += pageText + '\\n'

&nbsp; }

&nbsp; 

&nbsp; return fullText

}

```



Challenge 2: Import Error with pdfjs-dist

```

Error: Cannot find module 'pdfjs-dist/build/pdf.mjs'

```



Challenge 3: TypeScript Type Errors

```

Error: Type issues with PDF.js response structure

```



Final Solution: Use unpdf library

```powershell

npm install unpdf

```



Why unpdf:

✓ Specifically designed for serverless environments

✓ No native dependencies

✓ Clean async/await API

✓ Compatible with Vercel deployment

✓ Smaller bundle size



Final Working Code:

```typescript

import { extractText } from 'unpdf'



async function extractPDFText(buffer: ArrayBuffer): Promise<string> {

&nbsp; const { text } = await extractText(buffer)

&nbsp; return text

}

```



===============================================================================

STEP 5: TESTING WITH SAMPLE DOCUMENTS

===============================================================================



Prompt:

"Can you create sample documents for testing? I need a flood insurance PDF, 

earthquake insurance DOCX, and mortgage rates XLSX file."



Created Test Files:



1\. \*\*flood\_insurance\_policy.pdf\*\*

&nbsp;  - Multi-page PDF with policy details

&nbsp;  - Coverage limits and exclusions

&nbsp;  - Claims process documentation

&nbsp;  - Premium calculation factors



2\. \*\*earthquake\_insurance\_policy.docx\*\*

&nbsp;  - Word document with formatted tables

&nbsp;  - Coverage limits table

&nbsp;  - Step-by-step filing procedures

&nbsp;  - Risk assessment criteria

&nbsp;  - Policy exclusions



3\. \*\*mortgage\_rates\_guide.xlsx\*\*

&nbsp;  - Three-sheet workbook:

&nbsp;    Sheet 1: Mortgage Rates (Conventional, FHA, VA, Jumbo)

&nbsp;    Sheet 2: Loan Requirements (Credit scores, down payments)

&nbsp;    Sheet 3: Closing Costs (Fees breakdown)



Testing Process:

1\. Upload all three files through the UI

2\. Verify text extraction for each format

3\. Check chunk generation and embedding creation

4\. Test search functionality across all document types

5\. Confirm source attribution in search results



Results:

✅ All file types upload successfully

✅ Text extraction works for PDF, DOCX, XLSX

✅ Embeddings generated correctly for all chunks

✅ Search retrieves relevant information

✅ Multi-document queries work properly



===============================================================================

STEP 6: DATABASE SCHEMA IMPROVEMENTS

===============================================================================



Prompt:

"I want to add a sourceDocument column to the Chunk table so each chunk 

clearly shows which file it came from, rather than having filenames in the 

content."



Updated Prisma Schema:

```prisma

model Document {

&nbsp; id           String   @id @default(uuid())

&nbsp; filename     String   @unique

&nbsp; url          String?

&nbsp; uploadedAt   DateTime @default(now())

&nbsp; chunks       Chunk\[]

}



model Chunk {

&nbsp; id             String   @id @default(uuid())

&nbsp; sourceDocument String

&nbsp; content        String

&nbsp; embedding      Unsupported("vector(384)")

&nbsp; document       Document @relation(fields: \[sourceDocument], references: \[filename])

&nbsp; createdAt      DateTime @default(now())

&nbsp; 

&nbsp; @@index(\[sourceDocument])

}

```



Migration Steps:

```powershell

\# Generate migration

npx prisma migrate dev --name add-source-document



\# If pgvector extension is lost:

\# Connect to PostgreSQL and run:

CREATE EXTENSION IF NOT EXISTS vector;



\# Then apply migration again

npx prisma migrate deploy

```



Updated Upload Code:

```typescript

// Create chunk with sourceDocument reference

await prisma.chunk.create({

&nbsp; data: {

&nbsp;   sourceDocument: file.name,  // Clear attribution

&nbsp;   content: chunk,

&nbsp;   embedding: `\[${embedding.join(',')}]`,

&nbsp; }

})

```



Benefits:

✓ Clear source attribution for each chunk

✓ Easier to filter/delete chunks by document

✓ Better query performance with indexed field

✓ Cleaner separation of content and metadata



===============================================================================

STEP 7: FIXING SEARCH FUNCTIONALITY

===============================================================================



Prompt:

"The search isn't finding my auto insurance documents even though they exist 

in the database."



Investigation:

1\. Verified documents in database:

```sql

SELECT \* FROM "Document" WHERE filename LIKE '%auto%';

-- Result: Documents found



SELECT COUNT(\*) FROM "Chunk" WHERE "sourceDocument" LIKE '%auto%';

-- Result: 47 chunks found

```



2\. Identified Problem:

&nbsp;  - Upload route: Using OpenAI embeddings (text-embedding-3-small)

&nbsp;  - Query route: Using local TF-IDF embeddings

&nbsp;  - Different embedding spaces = zero similarity scores



3\. Solution:

&nbsp;  Update query route to use OpenAI embeddings consistently



Updated Query Route Code:

```typescript

import OpenAI from 'openai'



const openai = new OpenAI({

&nbsp; apiKey: process.env.OPENAI\_API\_KEY

})



async function generateEmbedding(text: string): Promise<number\[]> {

&nbsp; const response = await openai.embeddings.create({

&nbsp;   model: 'text-embedding-3-small',  // SAME MODEL as upload

&nbsp;   input: text,

&nbsp;   dimensions: 384                    // SAME DIMENSIONS as upload

&nbsp; })

&nbsp; return response.data\[0].embedding

}



export async function POST(request: Request) {

&nbsp; const { query } = await request.json()

&nbsp; 

&nbsp; // Generate embedding for search query

&nbsp; const queryEmbedding = await generateEmbedding(query)

&nbsp; const embeddingString = `\[${queryEmbedding.join(',')}]`

&nbsp; 

&nbsp; // Search using pgvector cosine similarity

&nbsp; const chunks = await prisma.$queryRaw`

&nbsp;   SELECT 

&nbsp;     content, 

&nbsp;     "sourceDocument",

&nbsp;     1 - (embedding <=> ${embeddingString}::vector) as similarity

&nbsp;   FROM "Chunk"

&nbsp;   WHERE 1 - (embedding <=> ${embeddingString}::vector) > 0.3

&nbsp;   ORDER BY similarity DESC

&nbsp;   LIMIT 5

&nbsp; `

&nbsp; 

&nbsp; // Generate answer using retrieved context

&nbsp; const context = chunks.map((c: any) => 

&nbsp;   `\[Source: ${c.sourceDocument}]\\n${c.content}`

&nbsp; ).join('\\n\\n')

&nbsp; 

&nbsp; const completion = await openai.chat.completions.create({

&nbsp;   model: 'gpt-4',

&nbsp;   messages: \[

&nbsp;     {

&nbsp;       role: 'system',

&nbsp;       content: 'Answer based on the provided context. Cite sources.'

&nbsp;     },

&nbsp;     {

&nbsp;       role: 'user',

&nbsp;       content: `Context:\\n${context}\\n\\nQuestion: ${query}`

&nbsp;     }

&nbsp;   ]

&nbsp; })

&nbsp; 

&nbsp; return NextResponse.json({

&nbsp;   answer: completion.choices\[0].message.content,

&nbsp;   sources: chunks.map((c: any) => ({

&nbsp;     filename: c.sourceDocument,

&nbsp;     similarity: c.similarity

&nbsp;   }))

&nbsp; })

}

```



Key Fix:

✅ Use identical embedding model for storage and retrieval

✅ Same dimensions (384)

✅ Proper vector similarity calculation with pgvector



===============================================================================

STEP 8: UI CACHING ISSUES

===============================================================================



Prompt:

"Deleted documents keep reappearing in the UI after deletion."



Root Causes:

1\. Next.js aggressive caching of GET requests

2\. No cache invalidation after mutations

3\. Frontend state not updated optimistically



Solution 1 - Backend Cache Headers:

```typescript

// Add to list route

export const dynamic = 'force-dynamic'

export const revalidate = 0



export async function GET() {

&nbsp; const files = await prisma.document.findMany()

&nbsp; 

&nbsp; return NextResponse.json(

&nbsp;   { files },

&nbsp;   {

&nbsp;     headers: {

&nbsp;       'Cache-Control': 'no-store, no-cache, must-revalidate',

&nbsp;     }

&nbsp;   }

&nbsp; )

}

```



Solution 2 - Frontend Cache Busting:

```typescript

const loadFiles = async () => {

&nbsp; try {

&nbsp;   // Add timestamp to prevent caching

&nbsp;   const res = await fetch(`/api/knowledge/list?t=${Date.now()}`, {

&nbsp;     cache: 'no-store',

&nbsp;     headers: {

&nbsp;       'Cache-Control': 'no-cache'

&nbsp;     }

&nbsp;   })

&nbsp;   const data = await res.json()

&nbsp;   setFiles(data.files || \[])

&nbsp; } catch (error) {

&nbsp;   console.error('Failed to load files:', error)

&nbsp;   setFiles(\[])

&nbsp; }

}

```



Solution 3 - Optimistic Updates:

```typescript

const handleDelete = async (filename: string) => {

&nbsp; setDeleting(filename)

&nbsp; 

&nbsp; // Update UI immediately

&nbsp; setFiles(prev => prev.filter(f => f.name !== filename))

&nbsp; 

&nbsp; try {

&nbsp;   const res = await fetch(

&nbsp;     `/api/knowledge/delete?filename=${encodeURIComponent(filename)}`,

&nbsp;     { method: 'DELETE' }

&nbsp;   )

&nbsp;   

&nbsp;   if (!res.ok) throw new Error('Delete failed')

&nbsp;   

&nbsp;   setNotification({ 

&nbsp;     message: 'Document deleted successfully', 

&nbsp;     type: 'success' 

&nbsp;   })

&nbsp; } catch (error) {

&nbsp;   // Rollback on error

&nbsp;   await loadFiles()

&nbsp;   setNotification({ 

&nbsp;     message: 'Failed to delete document', 

&nbsp;     type: 'error' 

&nbsp;   })

&nbsp; } finally {

&nbsp;   setDeleting(null)

&nbsp; }

}

```



===============================================================================

DEPLOYMENT WORKFLOW

===============================================================================



Standard Git Commands:

```powershell

\# Navigate to project directory

cd "C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending"



\# Stage all changes

git add .



\# Commit with descriptive message

git commit -m "Add multi-format document library with PDF, DOCX, XLSX support"



\# Push to GitHub (branch is 'main' not 'master')

git push origin main

```



Common Issues:

\- Error: "src refspec master does not match any"

&nbsp; Fix: Use `git push origin main` instead



\- Warning: "LF will be replaced by CRLF"

&nbsp; Note: Safe to ignore, just line ending differences



Vercel Deployment:

\- Auto-deploys on push to main branch

\- Or manually deploy: `vercel --prod`



Environment Variables Needed:

```

OPENAI\_API\_KEY=sk-...

DATABASE\_URL=postgresql://...?sslmode=require

```



===============================================================================

KEY LESSONS LEARNED

===============================================================================



1\. \*\*Embedding Model Consistency is Critical\*\*

&nbsp;  - ALWAYS use the same model for storage and retrieval

&nbsp;  - Different models = different vector spaces = no matches

&nbsp;  - Document the model version in code comments



2\. \*\*Serverless Environment Limitations\*\*

&nbsp;  - Native modules don't work in Vercel functions

&nbsp;  - Choose libraries designed for serverless (unpdf vs pdf-parse)

&nbsp;  - Test locally AND in production environment



3\. \*\*Cache Management in Next.js\*\*

&nbsp;  - Default caching can break CRUD operations

&nbsp;  - Add force-dynamic and no-cache headers

&nbsp;  - Implement optimistic UI updates for better UX



4\. \*\*Database Extensions Require Care\*\*

&nbsp;  - pgvector can be lost during migrations/resets

&nbsp;  - Always re-enable: CREATE EXTENSION vector;

&nbsp;  - Document extension requirements in README



5\. \*\*File Processing Error Handling\*\*

&nbsp;  - Validate file types on both client and server

&nbsp;  - Handle corrupted/malformed files gracefully

&nbsp;  - Provide specific error messages to users



===============================================================================

FINAL SYSTEM CAPABILITIES

===============================================================================



✅ Multi-Format Document Upload:

&nbsp;  - PDF files (.pdf)

&nbsp;  - Word documents (.doc, .docx)

&nbsp;  - Excel spreadsheets (.xls, .xlsx)

&nbsp;  - Text files (.txt)



✅ Intelligent Text Extraction:

&nbsp;  - PDF: Full text from all pages

&nbsp;  - DOCX: Clean text (formatting stripped)

&nbsp;  - XLSX: Data from all sheets

&nbsp;  - TXT: Direct UTF-8 read



✅ Advanced Processing:

&nbsp;  - Automatic text chunking (1000 chars, 200 overlap)

&nbsp;  - OpenAI embeddings (384 dimensions)

&nbsp;  - Vector similarity search with pgvector

&nbsp;  - Source attribution for all results



✅ Production-Ready Features:

&nbsp;  - Error handling and validation

&nbsp;  - Optimistic UI updates

&nbsp;  - Cache management

&nbsp;  - Batch file uploads

&nbsp;  - Delete functionality with cleanup





