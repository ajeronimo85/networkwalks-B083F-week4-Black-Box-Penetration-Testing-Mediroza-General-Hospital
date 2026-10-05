# 🎯 Week 4 - Capstone Penetration Testing Project: Mediroza General Hospital
Target Type Batch Status

NetworkWalks Academy | Cybersecurity & Ethical Hacking Internship (Batch B082) Capstone Engagement: Black-Box Penetration Testing Target : Mediroza General Hospital Instructor: Waqas Karim (CCIE) Intern: Mithun Kumar Rajak

📋 Executive Overview
In Week 4, the capstone assignment required conducting an end-to-end Black-Box Penetration Test against the web infrastructure of Mediroza General Hospital.

The objective was to evaluate the hospital's security posture as an unauthenticated external attacker, uncover security weaknesses, evaluate real-world business risks (such as patient health data and corporate asset exposure), and deliver an actionable remediation roadmap.

📑 Project Parameters
Parameter	Specification
Client Target	Mediroza General Hospital
Testing Type	Full Black-Box (Zero prior knowledge or insider access)
Duration	3 Days
Rules of Engagement	Strict target boundaries; no Denial of Service (DoS); no social engineering or phishing
Authorization	Authorized educational engagement by NetworkWalks Academy & client


# 🏥 Milestone 1: Initial Access & Report Retrieval
**What was required:**Gain unauthorized access to the hospital's patient portal and retrieve three confidential patient laboratory reports.
How we did it:
During reconnaissance, discovered public and restricted portal routes.
Identified an authentication bypass flaw on the patient login page, allowing entry without valid credentials.
Once inside, identified an access control weakness on the report download feature that allowed downloading records belonging to other patients.
Outcome: Successfully retrieved all 3 target patient laboratory PDF reports.

# 🔓 Milestone 2: Document Decryption & Data Extraction
**What was required:**Crack the password encryption protecting the three retrieved patient PDF documents.
How we did it:
Analyzed the document security properties, identifying standard 128-bit encryption.
Conducted an offline dictionary analysis against the security handler using common password lists.
Identified that predictable, low-complexity passwords had been assigned to the documents.
Outcome: Successfully decrypted all 3 patient PDF files, uncovering the underlying medical diagnostic information.

# 💼 Milestone 3: Sensitive Asset Discovery
**What was required:**Locate confidential internal hospital organizational assets, specifically staff salary registers and shareholder ownership details.
How we did it:
Identified an open directory listing misconfiguration on a legacy server directory.
Discovered an exposed internal database backup archive left in the web root.
Inspected the archive contents to extract staff remuneration data and corporate shareholder distributions.
Outcome: Successfully identified records for 30 staff members across multiple hospital departments and 10 corporate equity holders.

# 📑 Milestone 4: Penetration Testing Report Deliverable
**What was required:**Author a professional, industry-standard penetration testing report documenting all identified vulnerabilities, their risk ratings, and strategic remediation steps.
How we did it:
Documented all 8 technical findings and assigned CVSS v3.1 severity scores.
Applied healthcare compliance standards (HIPAA § 164.312, POPIA, GDPR) to assess regulatory exposure.
Sanitized all sensitive personal and financial data to ensure safe public/portfolio disclosure.
Formulated a prioritized remediation roadmap covering immediate, short-term, and long-term fixes.
Outcome: Produced complete executive and technical pentest deliverables ready for leadership and technical teams.

# 🛡️ Core Defensive Recommendations
Use Parameterized Queries: Always separate SQL application code from user input to eliminate injection vectors.
Enforce Server-Side Ownership Checks: Ensure user session identities match the requested data before serving documents.
Disable Directory Browsing: Turn off directory indexing on web servers and never store database backups in web-accessible paths.
Upgrade Encryption Standards: Adopt modern AES-256 encryption with high-entropy, randomly generated passphrases for sensitive documents.
Implement Defense-in-Depth: Deploy Web Application Firewalls (WAF), enforce multi-factor authentication (MFA), and suppress detailed system error messages.

# Step 1. Recon
![](Screenshot1.png)

# Step 2. Find the login page
![](Screenshot2.png)

# Step 3. Username enumeration
![](Screenshot3.png)
![](Screenshot4.png)

Username not found
![](Screenshot5.png)
try admin
![](Screenshot6.png)

Incorrect password
![](Screenshot7.png)

# Step 4. Test for SQL injection
SQL injection is a vulnerability where the application places user input directly inside a database query. A single quote breaks the query syntax and causes the database to throw an error, which tells us the field is inject
![](Screenshot9.png)

# Step 5. Bypass the login
The payload admin' -- works by breaking out of the query string with a quote, then using -- to comment out everything after it, including the password check. The database then matches admin with no password condition.
![](Screenshot11.1.png)

Logged in. Portal page loads with 3 reports listed.
![](Screenshot12.1.png)

# Step 6. Download the 3 reports
Download all 3 files from the portal.
![](Screenshot13.png)

# Milestone 2 — Crack the Encryption

# Step 1. Get the hash of each PDF
ℹ PDF files use password-based encryption. To crack the password we first extract a hash, which is a mathematical fingerprint of the encrypted file. The cracking tool then tries passwords from a wordlist and checks each one against the hash.
![](Screenshot14.png)

# Step 2. Crack reports 1 and 2
ℹ A wordlist is a text file containing common passwords. The cracker tries each word until one matches. The built-in list covers the most common 100 passwords in the world.
RESULTS 
patient_report_1.pdf - 123456
patient_report_2.pdf - password
![](Screenshot15.png)
![](Screenshot16.png)
![](Screenshot17.png)
![](Screenshot18.png)
![](Screenshot19.png)
![](Screenshot20.png)
![](Screenshot21.png)

# Step 3. Crack report 3
ℹ When a small wordlist fails it means the password is not among the most common ones. The solution is to try a larger wordlist that covers more possibilities.
Run report 3 with the built-in list first.
RESULT Exhausted wordlist. No match. ACCESS DENIED.
![](Screenshot22.png)

![](Screenshot23.png)

![](Screenshot24.png)

![](Screenshot25.png)

![](Screenshot26.png)

# Step 4. Keep an unlocked copy of report 3
ℹ qpdf is a command line tool that can decrypt a password protected PDF and save a clean copy. You need an unlocked copy so that metadata analysis tools can read all the file properties in the next milestone.
![](Screenshot27.png)

# Milestone 3 — Deep Reconnaissance

# Step 1. Confirm from robots.txt
ℹ You already found /old in the robots file during recon in Step 1. This is why thorough recon matters. A clue found later in the assessment can connect back to something you spotted earlier.
Both the metadata clue and the robots file point to /old. Either path leads to the same place.

# Step 2. Open the old folder
ℹ Directory listing is a misconfiguration where a web server shows the contents of a folder like a file browser when no index page exists. This exposes files that should not be publicly visible. 
https://medirozahospital.com/old/
![](Screenshot28.png)


Directory listing is enabled. The backup file is visible. mediroza_db_backup_2019.sql
Download it.
![](Screenshot29.png)

# Step 4. Read the data with ChatGPT
ℹ A .sql file is a database backup in raw text format. It contains SQL commands used to recreate tables and insert data. The format is readable but dense. AI tools like ChatGPT can convert it into a clean human readable table instantly.
Open the file in a text editor. Copy the INSERT INTO `staff` rows and send to ChatGPT with this prompt. Present this SQL data as a readable table showing name, job title, department and monthly salary.
![](Screenshot30.png)

Then copy the INSERT INTO `shareholders` rows and send with this prompt. Present this SQL data as a readable table showing shareholder name, share percentage and share class.
![](Screenshot31.png)
![](9-Screenshothashcalculator.png)

