Prompt 1: Initial Data Extraction Request



I have a historical 1907 San Francisco telephone directory PDF. I need to extract contact information where:



First and Last names should be real (from the directory)

Phone numbers can be synthetic - convert old 4-digit numbers to modern 10-digit format using Bay Area codes (415, 650, 925, 510, 408)

Addresses can be synthetic California addresses if originals are incomplete



Please extract 100 entries from the middle of this PDF to start.





Prompt 2: Expanded Requirements (100 per Area Code)



My mentor has updated the requirements:



Extract 500 total entries (100 entries per area code)

Names must be REAL from the historical directory

Addresses must be REAL from the historical directory

Phone numbers can be synthetic with proper Bay Area area codes:



415 (San Francisco/Marin)

650 (Peninsula)

925 (East Bay)

510 (Oakland/Berkeley)

408 (San Jose/South Bay)







Please also add my mentor as the first entry:



Name: Jayaraman Venkataramni

Phone: 415-555-0001

Address: 201 Harrison St Apt 201, San Francisco, CA 94105







Prompt 3: Database Import Script



Create a TypeScript import script for Prisma that:



Connects to the Customer table

Optionally clears existing records (--clear flag)

Imports all 501 entries with proper field mapping

Logs progress and final count

Sets source as "Yellow Pages 1907 - Bay Area"







Prompt 4: Schema Cleanup



Remove unused columns from the Customer table:



street (redundant - address field contains full address)

apt (redundant)

city (redundant)



Keep the address field which stores the complete address string.





Prompt 5: Database Deployment



Push the updated schema and import data to the production Neon database, then verify the data is accessible.

