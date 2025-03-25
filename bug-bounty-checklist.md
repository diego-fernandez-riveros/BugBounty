# Consolidated Bug Bounty Checklist for Web Apps

A comprehensive, up-to-date checklist for bug bounty hunting. It combines modern recon, in-depth domain analysis, API/cloud/native risks, advanced authentication, and automation workflows. Always verify program scopes before testing. Happy hunting!

## Table of Contents
1. [Recon & Enumeration](#1-recon--enumeration)
2. [Single Domain Assessment](#2-single-domain-assessment)  
   2.1 [Scanning & Crawling](#21-scanning--crawling)  
   2.2 [Manual Checks & OSINT](#22-manual-checks--osint)
3. [Information Gathering](#3-information-gathering)
4. [Configuration & Secure Transmission](#4-configuration--secure-transmission)
5. [Authentication & Session Management](#5-authentication--session-management)  
   5.1 [Authentication (including Advanced/2.0)](#51-authentication-including-advanced20)  
   5.2 [Advanced Session Security](#52-advanced-session-security)
6. [Authorization & Data Validation](#6-authorization--data-validation)
7. [API, GraphQL & Modern Input Attacks](#7-api-graphql--modern-input-attacks)
8. [Denial of Service & Business Logic](#8-denial-of-service--business-logic)  
   8.1 [Denial of Service](#81-denial-of-service)  
   8.2 [Business Logic & Post-Exploitation](#82-business-logic--post-exploitation)
9. [Cryptography](#9-cryptography)
10. [Risky Functionality](#10-risky-functionality)  
    10.1 [File Uploads](#101-file-uploads)  
    10.2 [Card Payment & Sensitive Flows](#102-card-payment--sensitive-flows)
11. [Cloud, Infrastructure & CI/CD](#11-cloud-infrastructure--cicd)
12. [Modern Web Techniques](#12-modern-web-techniques)
13. [Mobile & Hybrid Apps](#13-mobile--hybrid-apps)
14. [Automation & Workflows](#14-automation--workflows)
15. [Critical New Focus Areas & Resources](#15-critical-new-focus-areas--resources)

---

## 1. Recon & Enumeration
**Subdomain & DNS Recon:**
- Run Amass, Subfinder, Chaos, Assetfinder
- Use dnsgen, shuffledns, MassDNS
- Test for subdomain takeovers with Subjack or using nuclei-templates

**Live Host Detection & Screenshots:**
- Use httprobe or httpx
- Capture screenshots with Aquatone, Eyewitness, gowitness, or aerial

**Certificate Transparency & Cloud Assets:**
- Query crt.sh, certspotter, tlsx
- Enumerate S3 buckets, Azure blobs, and Google Cloud Storage (e.g., with cloud_enum)

**API & Email Discovery:**
- Identify OpenAPI/Swagger docs, GraphQL endpoints, and websockets
- Perform email enumeration via tools like Hunter.io, phonebook.cz, LinkedIn searches

**Historical & Git Recon:**
- Retrieve historical URLs with waybackurls, gau (with commoncrawl)
- Search code repositories using git-dumper, truffleHog, and github-search

**New Tools:** httpx, nuclei, interactsh, katana, uncover

---

## 2. Single Domain Assessment
### 2.1 Scanning & Crawling
- **Port & Service Scanning:** Use Nmap (with NSE scripts for service/version detection)
- **Crawling:** Run Burp Suite (advanced scope) or katana for deep crawling
- **Fuzzing:** Use ffuf, dirsearch, GoBuster, and paramspider
- **JavaScript Analysis:** Run Linkfinder, JSParser, SecretFinder, JSPrime
- **Vulnerability Scanning:** Use nuclei (with 5000+ templates) and nikto (with updated signatures)
- **Technology Fingerprinting:** Tools such as Wappalyzer, BuiltWith, WhatWeb

### 2.2 Manual Checks & OSINT
- Check for sensitive files: `.env`, `.git/`, `.github/`, CI/CD configs, backups, old versions
- Look for cache poisoning (web cache deception, unkeyed inputs)
- Test edge-case parameters (JSON input, multipart forms, GraphQL queries)
- Assess prototype pollution via manual testing or tools like DOMi
- Evaluate WebSocket security (tools: wsrepl, Burp Collaborator)

---

## 3. Information Gathering
- Manually explore the site to map structure and hidden endpoints
- Crawl/spider for missed or hidden content (using updated tools like Burp's Site Map or gaul)
- Check for configuration files (`robots.txt`, `sitemap.xml`, backups)
- Inspect public caches (Google Cache, Wayback Machine)
- Test content variation with different User Agents (mobile, crawlers)
- Perform fingerprinting to identify frameworks, versions, and client-side code
- Map out API endpoints and third-party hosted content

---

## 4. Configuration & Secure Transmission
**Configuration Management:**
- Identify common application/administrative URLs and old/backed-up files
- Enumerate supported HTTP methods and test for Cross Site Tracing (XST)
- Validate file extension handling and MIME type checks
- Check for security HTTP headers (CSP, X-Frame-Options, HSTS, Referrer-Policy, Permissions-Policy)
- Evaluate client-side policies and debug endpoints

**Secure Transmission:**
- Evaluate SSL/TLS versions, algorithms, and key lengths (tools: SSLyze, testssl.sh)
- Verify certificate validity (duration, signature, CN/SAN)
- Ensure all credentials, login forms, APIs, and session tokens are delivered over HTTPS
- Confirm proper use of HSTS and emerging protocols (HTTP/2, HTTP/3)

---

## 5. Authentication & Session Management
### 5.1 Authentication (including Advanced/2.0)
**Basic Authentication Testing:**
- Test for user enumeration via timing or error responses
- Identify authentication bypass vectors and test for brute-force issues
- Assess password policies, quality rules, and "remember me" functionality
- Review password reset and recovery flows (e.g., token leakage, timing attacks)
- Check for autocomplete on sensitive inputs and default credentials

**Modern/Advanced Techniques:**
- Test OAuth flows (`redirect_uri` bypass, token leakage in logs)
- Evaluate MFA implementations and attempt MFA bypass (e.g., status code manipulation, replay attacks)
- Assess JWT issues (`alg=none`, JWK header injection, `kid` traversal)
- Test for 2FA fatigue (push notification spamming) and credential stuffing (e.g., using X-Forwarded-For)

### 5.2 Advanced Session Security
- Map session management: cookies (with JWT, tokens), URL parameters, etc.
- Verify cookie flags (HttpOnly, Secure, SameSite – Lax vs Strict)
- Check session cookie scope (domain/path) and duration (Expires/Max-Age)
- Test session fixation and the issuance of new session tokens upon login or privilege change
- Assess OAuth session mixing and potential session replay post-password change/logout

---

## 6. Authorization & Data Validation
**Authorization:**
- Test for insecure direct object references (IDOR), path traversal, and privilege escalation (vertical/horizontal)
- Verify that unauthorized access is consistently blocked across endpoints

**Data Validation & Input Attacks:**
- Test for Reflected, Stored, and DOM-based XSS (employ modern payloads, e.g., via XSpear)
- Check for HTML Injection, SQL/NoSQL Injection, LDAP/ORM/XML injections
- Validate against Server-Side Template Injection (SSTI), Expression Language Injection, and command injection
- Test for HTTP parameter pollution, mass assignment, HTTP request smuggling/splitting, and HTTP verb tampering
- Evaluate error handling to avoid leakage of sensitive information

---

## 7. API, GraphQL & Modern Input Attacks
**API Security:**
- Enumerate API endpoints via Postman collections, automated discovery, and introspection
- Test authentication/authorization on APIs, including rate limiting and abuse controls
- Validate input parameters and enforce schema validation (for REST and GraphQL)
- Test for mass assignment vulnerabilities, excessive parameters, and SQLi via APIs

**GraphQL Specifics:**
- Run introspection queries and test for depth/complexity issues (tools: InQL, GraphQL Voyager)
- Evaluate batch operations and potential for GraphQL SQLi

**Modern Attacks:**
- Examine Webhooks for SSRF vulnerabilities (e.g., Slack/MS Teams integrations)
- Assess host header poisoning and request smuggling variants (CL.TE/TE.CL)

---

## 8. Denial of Service & Business Logic
### 8.1 Denial of Service
- Test for anti-automation and CAPTCHA bypass techniques
- Validate account lockout mechanisms under brute force
- Check for HTTP protocol DoS (slow HTTP attacks, resource exhaustion)
- Investigate SQL wildcard abuse and heavy query impact

### 8.2 Business Logic & Post-Exploitation
**Business Logic:**
- Test for feature misuse, lack of non-repudiation, and trust relationship issues
- Verify proper segregation of duties and integrity of critical operations

**Post-Exploitation:**
- Assess privilege escalation opportunities (Admin → Superadmin transitions)
- Explore data exfiltration channels (e.g., blind XXE, DNS exfiltration)
- Test persistence methods (e.g., JWT lifetime manipulation, refresh token abuse)
- Identify pivot points (internal API access via SSRF, API keys in JS)

---

## 9. Cryptography
- Check that sensitive data is encrypted at rest and in transit
- Validate correct cryptographic algorithm usage and key management practices
- Identify use of weak or deprecated algorithms and ensure proper salting and randomness
- Review key storage policies and management across environments

---

## 10. Risky Functionality
### 10.1 File Uploads
- Ensure file type whitelisting and validate content matches declared type
- Enforce file size limits, upload frequency, and total file count restrictions
- Verify antivirus scanning on uploads and sanitize unsafe filenames
- Ensure uploaded files are stored outside the web root or on a separate domain/subdomain
- Confirm file upload endpoints follow authentication/authorization schemas

### 10.2 Card Payment & Sensitive Flows
- Test payment servers for known vulnerabilities and misconfigurations
- Verify absence of default/guessable credentials and exposure of non-production data
- Check for injection vulnerabilities, buffer overflows, and insecure cryptographic storage
- Assess transport layer protection and proper error handling/logging
- Evaluate CSRF and authentication/authorization issues in payment flows

---

## 11. Cloud, Infrastructure & CI/CD
**Cloud & Infrastructure:**
- Enumerate and assess S3 buckets, Azure blobs, Google Cloud Storage (permissions, public access)
- Test serverless configurations (AWS Lambda env vars, Azure function configs)
- Check Kubernetes environments (using Kube-hunter, verifying dashboards)

**CI/CD & DevOps:**
- Enumerate CI/CD endpoints (Jenkins, GitLab CI/CD, etc.) for misconfigurations
- Scan public repositories for secrets (using TruffleHog, Gitleaks)
- Verify deployment pipelines enforce security tests (OWASP Dependency-Check, Snyk)
- Use tools like Pacu, ScoutSuite, and s3scanner for cloud misconfigurations

---

## 12. Modern Web Techniques
**Modern Attack Vectors:**
- Test for web cache poisoning (unkeyed headers, DOM-based)
- Check for host header poisoning (password reset poisoning, host override)
- Evaluate request smuggling variants (CL.TE/TE.CL) with tools like smuggler
- Test for Server-Side Template Injection (SSTI) in modern templating engines (Jinja2, Freemarker, Handlebars) using tplmap
- Combine vulnerabilities (e.g., CSRF via XSS) for compounded impact

**CORS & Webhooks:**
- Assess CORS configurations (trusted null origins, regex flaws)
- Test webhooks for SSRF and other chained attacks

**Tools & Techniques:** Turbo Intruder, smuggler, param-miner

---

## 13. Mobile & Hybrid Apps
**APK & Code Analysis:**
- Analyze APKs using MobSF, jadx, and frida
- Check for insecure API keys or unencrypted traffic in React Native or hybrid bundles

**Deep Linking & WebViews:**
- Test for intent hijacking and scheme abuse in deep links
- Evaluate WebView implementations for XSS (e.g., via loadUrl()) and CORS bypass issues

**Firebase & Cloud Services:**
- Check for misconfigured Firebase databases or cloud integrations using dedicated scanners

---

## 14. Automation & Workflows
**Continuous Scanning:**
- Use Nuclei templates (daily updated for new CVEs) and zap-baseline
- Integrate automated scanning into GitHub Actions or CI/CD pipelines

**Workflow Automation:**
- Schedule recon workflows with tools like GitHub Actions and custom Kali distributions (e.g., TraceLabs)
- Combine Burp Pro with interact.sh for out-of-band testing
- Leverage continuous scanning tools such as arachni for periodic assessments

---

## 15. Critical New Focus Areas & Resources
**Critical Focus Areas:**
- GraphQL: Batch attacks, introspection leaks, and complex queries
- Web3: Explore smart contract interactions via web interfaces
- Serverless: Assess Lambda environment variables, cold-start issues, and function misconfigurations
- CI/CD: Evaluate poisoned pipeline execution (PPE) and insecure automation scripts
- Chaining Vulnerabilities: Example – chaining XSS → CSRF → Account Takeover

**Updated Tools Stack:**
- Recon: amass, subfinder, chaos, dnsx
- Scanning: nuclei, zap, burp suite
- Fuzzing: ffuf, wfuzz, qsreplace
- Analysis: gowitness, jsubfinder, SecretFinder
- Mobile: MobSF, objection, frida
- Cloud: cloudfox, s3scanner, gcpBucketBrute

**Key Resources:**
- OWASP Top 10 2021
- HackerOne Hacktivity Insights
- Bugcrowd University
- PayloadsAllTheThings GitHub
