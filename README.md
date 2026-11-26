# networkwalks-wk4-project
Mediroza General Hospital – Web Application Penetration Testing

"Cybersecurity" (https://img.shields.io/badge/Domain-Cybersecurity-blue)
"Assessment" (https://img.shields.io/badge/Assessment-Week%204-orange)
"Status" (https://img.shields.io/badge/Status-Completed-success)
"Platform" (https://img.shields.io/badge/Platform-Kali%20Linux-purple)


Project Overview

This project documents an authorized black-box web application penetration testing assessment conducted as part of the Networkwalks cybersecurity internship programme.

The assessment focused on identifying security weaknesses within a hospital web application, examining authentication controls, retrieving protected documents within the authorized lab exercise, evaluating PDF password protection, and investigating exposed server resources.

The exercise followed four milestones (M1–M4), progressing from reconnaissance and initial access to data extraction, sensitive information exposure analysis, and final security reporting.

prepared by : Sakinah Abubakar
Assessment Type: Black-Box Web Application Penetration Testing
Environment: Kali Linux
Target: https://medirozahospital.com
Assessment Period: November 2026
Status: Completed

«Disclaimer: This project was conducted under the authorization provided for the educational assessment. The findings are documented for cybersecurity learning and defensive security improvement. Sensitive information has been excluded or redacted.»

---

Table of Contents

1. "Objectives" (#1-project-objectives)
2. "Tools and Technologies" (#2-tools-and-technologies)
3. "Methodology" (#3-methodology)
4. "M1 – Reconnaissance and Initial Access" (#4-m1--reconnaissance-and-initial-access)
5. "M2 – PDF Hash Extraction and Password Recovery" (#5-m2--pdf-hash-extraction-and-password-recovery)
6. "M3 – Critical Data Exposure" (#6-m3--critical-data-exposure)
7. "M4 – Penetration Testing Report" (#7-m4--penetration-testing-report)
8. "Risk Assessment" (#8-risk-assessment)
9. "Recommendations" (#9-recommendations)
10. "Lessons Learned" (#10-lessons-learned)
11. "Conclusion" (#11-conclusion)

---

1. Project Objectives

The assessment aimed to:

- Perform reconnaissance and identify publicly exposed website information.
- Identify accessible application directories and resources.
- Evaluate the patient portal's authentication security.
- Investigate document protection and password strength.
- Examine publicly accessible server resources.
- Identify exposure of sensitive organizational information.
- Document vulnerabilities, assess risks and recommend remediation.

2. Tools and Technologies

Tool| Purpose
Kali Linux| Penetration testing environment
WHOIS| Domain registration information
nslookup| DNS information gathering
DNSRecon| DNS enumeration
WhatWeb| Website technology identification
curl| HTTP requests and response-header inspection
Web Browser| Directory discovery and application interaction
Nmap| Network and service discovery
Gobuster| Directory and resource enumeration
ExifTool| Metadata inspection
Online Hash Calculator (Netsvorks.com)| PDF hash extraction
Online Password Cracker (Network.com)| PDF password recovery

3. Methodology

The assessment was divided into four milestones:

Milestone| Focus
M1| Reconnaissance and Initial Access
M2| PDF Hash Extraction and Password Recovery
M3| Critical Data Exposure
M4| Final Penetration Testing Report

The methodology involved information gathering, resource discovery, vulnerability identification, controlled testing, evidence collection, risk assessment and remediation planning.

---

4. M1 – Reconnaissance and Initial Access

Objective

Identify publicly exposed information, discover application resources and assess the security of the patient portal.

4.1 Website Reconnaissance

WhatWeb was used to identify the website's technology stack and publicly exposed metadata.

The scan identified:

Information| Observation
HTTP Status| 200 OK
Web Server| LiteSpeed
CMS| Mediroza CMS 1.4.2
Page Author| Mediroza IT Department
Server IP| Recorded during assessment
Public Contact Email| Identified during assessment

An HTTP header inspection was also performed using curl.

Security Observation: The website disclosed server and application metadata that could assist technology fingerprinting.

4.2 robots.txt and Sitemap Inspection

The website's robots.txt file was inspected to identify additional application paths.

The following directories were listed:

- "/patient/"
- "/staff/"
- "/old/"

The sitemap also revealed the publicly indexed website pages.

Security Observation: robots.txt does not enforce access restrictions. However, it can reveal directory paths that may require further investigation.

4.3 Patient Directory Discovery

The patient directory was accessed through a web browser.

The directory index exposed the following resources:

Resource| Description
"reports/"| Patient reports directory
"download.php"| Download endpoint
"error"| Error resource
"login.php"| Login endpoint
"logout.php"| Logout endpoint
"portal.php"| Patient portal

The directory listing revealed the structure of the patient application and exposed resources associated with authentication and document access.

4.4 SQL Injection and Authentication Bypass

A controlled SQL injection test was performed against the patient login functionality.

The test successfully bypassed authentication and provided access to the patient portal without valid login credentials.

Finding: SQL Injection – Authentication Bypass
Severity: Critical
CWE: CWE-89

Potential Impact:

- Unauthorized access to restricted application functionality.
- Exposure of confidential patient information.
- Compromise of application authentication controls.

4.5 Patient Report Retrieval

Following successful portal access, three password-protected pathology reports were retrieved.

The documents contained confidential patient information.

Finding: Unauthorized Access to Patient Documents
Severity: High

Potential Impact: Exposure of sensitive medical and personally identifiable information.

M1 Outcome

Reconnaissance identified the website's technology stack and exposed application resources. Authentication testing demonstrated a SQL injection vulnerability, and three protected patient reports were retrieved.

Status: COMPLETED

---

5. M2 – PDF Hash Extraction and Password Recovery

Objective

Examine the password protection applied to the three retrieved PDF reports and determine whether their passwords could be recovered.

5.1 Methodology

Two online tools were used:

1. Netsvorks.com Hash Calculator: Used to obtain the PDF password hashes.
2. Network.com Password Cracker: Used to perform password recovery.

The recovered passwords were subsequently used to open the protected documents and verify access.

5.2 Finding: Weak PDF Password Protection

All three PDF passwords were successfully recovered.

Severity: High

Potential Impact: Individuals who obtain the protected documents may be able to recover their passwords and access confidential information.

M2 Outcome

All three PDF documents were successfully recovered and opened for verification.


Status: COMPLETED – 3/3 DOCUMENTS RECOVERED

---

6. M3 – Critical Data Exposure

Objective

Investigate exposed server resources and identify sensitive organizational information accessible through the target website.

6.1 Discovery of the Old Directory

The "/old/" directory previously identified during reconnaissance was accessed directly through the web browser.

The server displayed an automatically generated directory listing titled:

"Index of /old/"

The listing exposed the following database backup:

"mediroza_db_backup_2019.sql"

Observed file information:

Attribute| Result
File| mediroza_db_backup_2019.sql
File Size| 6,346 bytes
Displayed Size| Approximately 7 KB
Last Modified| 04 September 2026
Directory| "/old/"

Finding: Public Directory Listing Enabled
Severity: Medium

6.2 Database Backup Inspection

The exposed SQL backup was inspected.

Two significant categories of information were identified.

Staff Table

- 30 employee records.
- Employee names and identification details.
- Job titles and departments.
- Contact information.
- National identification numbers.
- Monthly salary information.

Shareholders Table

- 10 shareholder records.
- Shareholder names.
- Share percentages.
- Number of shares held.
- Share classes.

6.3 Finding: Public Database Backup Exposure

Severity: Critical
CWE: CWE-552 – Files or Directories Accessible to External Parties

Potential Impact:

The publicly accessible database backup exposed sensitive employee and shareholder information without adequate access restrictions.

This could result in:

- Exposure of personally identifiable information.
- Unauthorized disclosure of employee compensation.
- Exposure of confidential organizational information.
- Increased risk of identity misuse and targeted fraud.

M3 Outcome

A publicly accessible database backup containing sensitive employee and shareholder information was successfully identified and inspected.

Status: COMPLETED

---

7. M4 – Penetration Testing Report

7.1 Executive Summary

The authorized assessment identified multiple security weaknesses affecting the hospital web application.

The most significant findings included SQL injection authentication bypass, unauthorized retrieval of patient reports, weak PDF password protection and exposure of a database backup containing sensitive organizational information.

The findings demonstrate weaknesses in application authentication, document protection and server access controls.

Overall Risk Assessment: CRITICAL

7.2 Scope and Methodology

Testing focused on the authorized website, patient application and publicly accessible resources.

The assessment followed the four milestones described in this repository.

Testing excluded social engineering and denial-of-service attacks.

7.3 Consolidated Findings

No.| Finding| Severity| Milestone
1| SQL injection authentication bypass| Critical| M1
2| Unauthorized retrieval of patient reports| High| M1
3| Weak PDF password protection| High| M2
4| Public database backup exposure| Critical| M3
5| Public directory listing| Medium| M1/M3
6| Technology and metadata disclosure| Low| M1

7.4 Risk Assessment

The critical findings represent significant confidentiality risks because they could allow unauthorized access to restricted application resources and sensitive database information.

High-risk findings involve exposure of confidential medical documents and inadequate document password protection.

Medium- and low-risk findings concern server configuration and information disclosure that could assist further reconnaissance.

7.5 Recommendations and Remediation

Finding| Recommendation
SQL Injection| Implement prepared statements and parameterized queries. Validate user input and apply least-privilege database permissions.
Patient Report Exposure| Enforce authentication and server-side authorization for every document request.
Weak PDF Passwords| Use strong, unique passwords and modern encryption. Do not rely on PDF passwords as the only access control.
Database Backup Exposure| Remove backups from public directories and store them in encrypted, access-restricted locations.
Directory Listing| Disable directory indexing and restrict access to sensitive directories.
Technology Disclosure| Remove unnecessary application metadata and review exposed server headers. Keep software updated.

Additional recommendations include regular vulnerability assessments, access monitoring, secure backup management and periodic penetration testing.

---

8. Risk Assessment Summary

Severity| Number of Findings
Critical| 2
High| 2
Medium| 1
Low| 1

Overall Assessment: CRITICAL

The ratings are qualitative assessments based on the observed impact and accessibility of the identified weaknesses, not formal CVSS scores.

---

9. Recommendations

The following security improvements should be prioritized:

1. Remediate the SQL injection vulnerability using parameterized database queries.
2. Strengthen authentication and authorization controls.
3. Restrict access to patient documents and sensitive application resources.
4. Remove exposed database backups from public directories.
5. Disable public directory indexing.
6. Strengthen document encryption and password policies.
7. Review server configuration and publicly exposed metadata.
8. Implement continuous monitoring and regular security assessments.

A retest should be conducted after remediation to confirm that the vulnerabilities have been resolved.

---

10. Lessons Learned

This assessment provided practical experience in:

- Conducting website reconnaissance and technology fingerprinting.
- Identifying exposed application directories and resources.
- Understanding SQL injection and authentication weaknesses.
- Examining password-protected documents.
- Performing PDF hash extraction and password recovery.
- Identifying publicly accessible database backups.
- Recognizing the risks of directory listing and information disclosure.
- Documenting security findings and recommending remediation.

The exercise also demonstrated how seemingly separate weaknesses can combine to expose sensitive information.

---

11. Conclusion

The Mediroza General Hospital assessment demonstrated the importance of secure application authentication, appropriate document protection and proper server configuration.

The successful identification of SQL injection, retrieval of protected patient reports, recovery of PDF passwords and discovery of an exposed database backup highlighted significant security risks.

The assessment reinforced the importance of defense in depth, secure file storage, access control and continuous vulnerability management.

M1: Reconnaissance and Initial Access – Completed
M2: PDF Hash Extraction and Password Recovery – Completed
M3: Critical Data Exposure – Completed
M4: Penetration Testing Report – Completed

---

Evidence and Screenshots

Screenshots supporting each milestone are organised into separate folders within the directory
.m1/ - Reconnaissance and initial access evidence.
.m2/ - PDF hash extraction and password recovery evidence.
.m3/ - Database exposure and sensitive information discovery evidence.

Only sanitized screenshots should be committed to this public repository. Do not upload patient records, recovered passwords, raw password hashes, employee personal information, salary details or unredacted database backups.

---

Project completed as part of the Networkwalks Cybersecurity Internship Programme.

Author: Sakinah Abubakar
