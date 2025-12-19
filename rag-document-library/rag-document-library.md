\# RAG Document Library - Session Prompts

\## Date: December 17-18, 2025



---



\## Prompt 1: Query Debugging - Auto Insurance Not Found

```

filenamechunk\_count1home\_insurance\_policy.txt142auto\_insurance\_policy.txt17

```

\*(Shared database query results showing both documents exist)\*



---



\## Prompt 2: Testing Auto Insurance Query

```

what are the steps to file an auto insurance claim 

The provided context does not contain specific steps for filing an auto insurance claim. Therefore, I cannot provide an answer based on the given information.

```

\*(Testing query - auto insurance not being found despite existing in database)\*



---



\## Prompt 3: Shared Neon Query Results

```

filenametotal\_chunkschunks\_with\_embedding1auto\_insurance\_policy.txt17172home\_insurance\_policy.txt1414

```

\*(Confirming both documents have embeddings)\*



---



\## Prompt 4: Confirmation Query Working

```

its wokring now most probaby give a question that combines both

```

\*(After fixing the query route embedding mismatch)\*



---



\## Prompt 5: Database Schema Normalization Request

```

We currently store uploaded document chunks in the `Chunk` table, where the file name appears inside the chunk text itself (e.g., "HOME INSURANCE POLICY", "AUTO INSURANCE POLICY" showing as a chunk row).

I want to normalize this properly.

Task:

1\. Add a new column to the `Chunk` table named `sourceDocument` (text/varchar).

2\. This column should store the original uploaded file name (e.g., `HOME\_INSURANCE\_POLICY.pdf`, `AUTO\_INSURANCE\_POLICY.pdf`).

3\. During the upload + chunking pipeline (MCP → PDF → chunks → embeddings):

&nbsp;  \* Extract the file name once from the uploaded document metadata.

&nbsp;  \* Populate `sourceDocument` for every chunk generated from that file.

4\. Remove the file name from being inserted as a chunk itself — chunks should contain only actual content, not the document title.

5\. Ensure existing logic for embeddings, RAG retrieval, and querying remains unchanged.

6\. Update any relevant insert/query code so `sourceDocument` is selectable and filterable later.

Keep this change minimal, clean, and backward-safe. Use the existing schema, ORM, and MCP structure already present in this project

```



---



\## Prompt 6: Prisma Schema Push Error

```

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending> code prisma\\schema.prisma

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending> npx prisma db push

Environment variables loaded from .env

Prisma schema loaded from prisma\\schema.prisma

Datasource "db": PostgreSQL database "neondb", schema "public" at "ep-super-rain-aduwfozx.c-2.us-east-1.aws.neon.tech"

⚠️ We found changes that cannot be executed:

&nbsp; • Added the required column `sourceDocument` to the `Chunk` table without a default value. There are 31 rows in this table, it is not possible to execute this step.

? To apply this change we need to reset the database, do you want to continue? All data will be lost. » (y/N)

```



---



\## Prompt 7: pgvector Extension Error

```

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending> npx prisma db push

Environment variables loaded from .env

Prisma schema loaded from prisma\\schema.prisma

Datasource "db": PostgreSQL database "neondb", schema "public" at "ep-super-rain-aduwfozx.c-2.us-east-1.aws.neon.tech"

⚠️ We found changes that cannot be executed:

&nbsp; • Added the required column `sourceDocument` to the `Chunk` table without a default value. There are 31 rows in this table, it is not possible to execute this step.

√ To apply this change we need to reset the database, do you want to continue? All data will be lost. ... yes

The PostgreSQL database "neondb" from "ep-super-rain-aduwfozx.c-2.us-east-1.aws.neon.tech" was successfully reset.

Error: ERROR: type "vector" does not exist

&nbsp;  0: sql\_schema\_connector::apply\_migration::migration\_step

&nbsp;          with step=CreateTable { table\_id: TableId(5) }

&nbsp;            at schema-engine\\connectors\\sql-schema-connector\\src\\apply\_migration.rs:21

&nbsp;  1: sql\_schema\_connector::apply\_migration::apply\_migration

&nbsp;            at schema-engine\\connectors\\sql-schema-connector\\src\\apply\_migration.rs:10

&nbsp;  2: schema\_core::state::SchemaPush

&nbsp;            at schema-engine\\core\\src\\state.rs:504

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending>

```



---



\## Prompt 8: Vercel Deployment Question

```

hey its deployed on vercel i dont think i will be able to check it

```



---



\## Prompt 9: Schema Push Success

```

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending> npx prisma db push

Environment variables loaded from .env

Prisma schema loaded from prisma\\schema.prisma

Datasource "db": PostgreSQL database "neondb", schema "public" at "ep-super-rain-aduwfozx.c-2.us-east-1.aws.neon.tech"

⚠️ We found changes that cannot be executed:

&nbsp; • Added the required column `sourceDocument` to the `Chunk` table without a default value. There are 31 rows in this table, it is not possible to execute this step.

√ To apply this change we need to reset the database, do you want to continue? All data will be lost. ... yes

The PostgreSQL database "neondb" from "ep-super-rain-aduwfozx.c-2.us-east-1.aws.neon.tech" was successfully reset.

Error: ERROR: type "vector" does not exist

&nbsp;  ...

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending> npx prisma db push

...

Your database is now in sync with your Prisma schema. Done in 20.89s

✔ Generated Prisma Client (v5.22.0) to .\\node\_modules\\@prisma\\client in 165ms

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending>  it worked ig now

```



---



\## Prompt 10: SQL Query Result Empty

```

SELECT "sourceDocument", LEFT(text, 80) as chunk\_preview 

FROM "Chunk" 

ORDER BY "sourceDocument"

LIMIT 10; 



i ran this statement it said statement executed successfully but its not showing anythin btw i haven deployed it yet i just finised step 3

```



---



\## Prompt 11: Chunk Order Question

```

why are chunks like divided first is home second auto then again two homes is this accurate

```



---



\## Prompt 12: Delete Not Working

```

okay i deleted it it said document removed but they are still present like i dont even upload these two but they are always present please go through all the chats and find whats wrong

```



---



\## Prompt 13: Build Error - createdAt Field

```

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending> vercel --prod

Vercel CLI 48.10.3

🔍  Inspect: https://vercel.com/nextgenfi/mortgage-lending/FWswyPmXwJSw1vvUxGBqqW84uTmd \[3s]

✅  Production: https://mortgage-lending-3434ob1t8-nextgenfi.vercel.app \[3s]

Error: Command "npm run build" exited with 1

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending> npm run build     

...

Failed to compile.

./app/api/knowledge/list/route.ts:10:18

Type error: Object literal may only specify known properties, and 'createdAt' does not exist in type 'DocumentOrderByWithRelationInput | DocumentOrderByWithRelationInput\[]'.

&nbsp;  8 |     // Get documents from database (source of truth)

&nbsp;  9 |     const documents = await prisma.document.findMany({

> 10 |       orderBy: { createdAt: 'desc' }

&nbsp;    |                  ^

&nbsp; 11 |     })

&nbsp; 12 |

&nbsp; 13 |     // Get chunk counts for each document

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending>  why this error now

```



---



\## Prompt 14: Document List Inconsistency

```

home insurance was already present while when i added the auto it got added cuz when i asked a question including both it did answer me also the i can see both in source document on neon but the thing is it is not showing up in the document section as you can see and home insurance is already present when i try to delete that a deleted pop up came but it was there as it is

```



---



\## Prompt 15: Both Documents Exist But Only One Shows

```

okay so both exist in the document but but when i open the deployed site only home insurance is present and when i try deploying the it does not load but the ai is answering questions related to both

```



---



\## Prompt 16: API Response - Only One Document

```

{"files":\[{"name":"home\_insurance\_policy.txt","size":0,"uploadedAt":null,"chunks":14}]}

```



---



\## Prompt 17: Database Shows Both Documents

```

idfilenameuploadedAt1cmj9x1z430000czfdtrs54vmrhome\_insurance\_policy.txt2025-12-17 11:16:29.5712cmjayspn50000g8kqmoz83wywauto\_insurance\_policy.txt2025-12-18 04:53:02.801 when i run  SELECT \* FROM "Document";

```



---



\## Prompt 18: Shared List Route Code

```

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending> cat app\\api\\knowledge\\list\\route.ts

import { NextResponse } from 'next/server'

import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

export async function GET() {

&nbsp; try {

&nbsp;   // Get documents from database (source of truth)

&nbsp;   const documents = await prisma.document.findMany()

&nbsp;   // Get chunk counts for each document

&nbsp;   const files = await Promise.all(

&nbsp;     documents.map(async (doc) => {

&nbsp;       const chunkCount = await prisma.chunk.count({

&nbsp;         where: { documentId: doc.id }

&nbsp;       })

&nbsp;       return {

&nbsp;         name: doc.filename,

&nbsp;         size: 0,

&nbsp;         uploadedAt: null,

&nbsp;         chunks: chunkCount

&nbsp;       }

&nbsp;     })

&nbsp;   )

&nbsp;   return NextResponse.json({ files })

&nbsp; } catch (error: any) {

&nbsp;   console.error('List error:', error)

&nbsp;   return NextResponse.json({ error: error.message, files: \[] }, { status: 500 })

&nbsp; }

}

```



---



\## Prompt 19: Local Testing Success

```

PS C:\\Users\\samar\\OneDrive\\Desktop\\mortgage-lending> npm run dev

> mortgage-lender-mvp@1.0.0 dev

> next dev

...

✓ Ready in 5.4s

...

okay when it started it had both docs already then i deleted home insurance and asked about it it did not answer so it waas deleted and then i aadded it back and it came files0name"auto\_insurance\_policy.txt"size0uploadedAtnullchunks171name"home\_insurance\_policy.txt"size0uploadedAtnullchunks14

```



---



\## Prompt 20: UI Caching Issue

```

okay so delete and ecerything is working fine on the deployed site just one thing that is the document section keeps showing even when the docs are deleted from the database

```



---



\## Prompt 21: Document Still Shows After Delete (December 18)

```

okay the delete feature works fine now but but when i add one of the two documents back the one added is indexed but it is automatically showing the other document as well in the document section tho it is not getting processed or anything

```



---



\## Prompt 22: Force Dynamic Route Request

```

import { NextResponse } from 'next/server'

import { PrismaClient } from '@prisma/client'



const prisma = new PrismaClient()



export async function GET() {

&nbsp; try {

&nbsp;   // Get documents from database (source of truth)

&nbsp;   const documents = await prisma.document.findMany()



&nbsp;   // Get chunk counts for each document

&nbsp;   const files = await Promise.all(

&nbsp;     documents.map(async (doc) => {

&nbsp;       const chunkCount = await prisma.chunk.count({

&nbsp;         where: { documentId: doc.id }

&nbsp;       })

&nbsp;       

&nbsp;       return {

&nbsp;         name: doc.filename,

&nbsp;         size: 0,

&nbsp;         uploadedAt: null,

&nbsp;         chunks: chunkCount

&nbsp;       }

&nbsp;     })

&nbsp;   )



&nbsp;   return NextResponse.json({ files })

&nbsp; } catch (error: any) {

&nbsp;   console.error('List error:', error)

&nbsp;   return NextResponse.json({ error: error.message, files: \[] }, { status: 500 })

&nbsp; }

}

```



---



\## Prompt 23: Prompts Documentation Request

```

okay now create a file of all the prompts i gave you today like exact prompts you can filter them on your own but the message should be similar

```



---



\# Summary of Issues Resolved



1\. \*\*RAG Query Not Finding Documents\*\* - Fixed embedding mismatch (TF-IDF vs OpenAI)

2\. \*\*Database Schema Normalization\*\* - Added sourceDocument column

3\. \*\*pgvector Extension Error\*\* - Re-enabled vector extension after database reset

4\. \*\*Document List Route\*\* - Changed from Vercel Blob to database as source of truth

5\. \*\*Delete Route Typo\*\* - Fixed `$executeRa` to `$executeRaw`

6\. \*\*Build Error\*\* - Removed non-existent createdAt field from orderBy

7\. \*\*UI Caching Issues\*\* - Added force-dynamic, cache-busting, and optimistic updates



---



\# Files Modified

\- `app/api/knowledge/query/route.ts` - OpenAI embeddings for queries

\- `app/api/knowledge/upload/route.ts` - sourceDocument column support

\- `app/api/knowledge/list/route.ts` - Database source of truth, force-dynamic

\- `app/api/knowledge/delete/route.ts` - Fixed typo, database-first deletion

\- `app/knowledge/page.tsx` - Cache-busting, optimistic UI updates

\- `prisma/schema.prisma` - Added sourceDocument column to Chunk model

