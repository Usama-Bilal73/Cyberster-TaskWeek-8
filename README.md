# Client-Side Security Assessment (Purple Team - Week 8)

## Overview
This repository contains documentation and findings from the Week 8 Client-Side Security Assessment conducted on **Damn Vulnerable Web Application (DVWA) v1.0.7**. The primary objective of this exercise was to identify, exploit, and mitigate vulnerabilities related to Cross-Site Scripting (XSS), Cross-Site Request Forgery (CSRF), and insecure cookie-based session management. Testing was systematically executed across Low, Medium, and High security levels to evaluate defensive control effectiveness.

## Student & Assessment Details
* **Student Name:** Muhammad Usama Bilal
* **Roll Number:** CSI-B1-460
* **Instructor:** Muhammad Saad
* **Date:** May 3, 2026
* **Environment:** Kali Linux (Target Deployment: `http://192.168.0.108`)

---

## Testing Strategy & Workflow
The assessment followed a structured purple-team auditing methodology:
1.  **Environment Validation:** Deploying and configuring target laboratory instances using default administrator credentials.
2.  **Reconnaissance:** Initial client-side discovery using built-in browser developer tools (Storage, Network, and Inspector panels).
3.  **XSS Testing:** Executing Reflected and Persistent (Stored) Cross-Site Scripting payloads within input parameters.
4.  **Post-Exploitation Simulation:** Demonstrating session hijacking via cookie theft, client-side keylogging, and layout defacement.
5.  **CSRF Evaluation:** Testing password manipulation parameters using forged, hidden cross-site request pages.
6.  **Defensive Mechanism Review:** Assessing behavioral changes in application filters when transitioning between Low, Medium, and High application settings.

---

## Core Findings Matrix

| ID | Vulnerability | Severity | Affected Security Levels |
| :--- | :--- | :--- | :--- |
| **V-01** | Reflected XSS | Critical | Low, Medium |
| **V-02** | Stored (Persistent) XSS | Critical | Low, Medium |
| **V-03** | Session Hijacking / Cookie Theft | Critical | All Levels |
| **V-04** | CSRF Password Manipulation | High | Low, Medium |
| **V-05** | Insecure Cookie Attribute Setup (Missing `HttpOnly`) | High | All Levels |
| **V-06** | Insecure Cookie Transmission (Missing `Secure` & `SameSite`) | Medium | All Levels |

---

## Key Exploits Demonstrated

### 1. Cross-Site Scripting (XSS)
* **Reflected:** Injected basic scripts via URL parameters to display alerts and print session identifiers (`document.cookie`).
* **Stored:** Placed permanent payload strings into the Guestbook component text fields. The payload successfully triggered code execution on initial load, subsequent tab reloads, and freshly established user sessions.

### 2. Cookie Flag Weakness
Under default configurations, session metrics lacked proper containment protections:
* `HttpOnly = false` (Allowed full access to browser scripts)
* `Secure = false` (Transmitted data over unencrypted HTTP pathways)
* `SameSite = None` (Permitted session distribution during cross-domain queries)

### 3. Cross-Site Request Forgery (CSRF)
Constructed an external standalone malicious HTML landing page that silently triggered a hidden POST request back to the target host web app, executing an unauthorized account password reset on behalf of an authenticated user.

---

## Remediation & Hardening Roadmap
To eliminate the identified security gaps, the following mitigation actions must be applied globally:

* **Enforce Global Output Encoding:** Implement context-aware context encoding (HTML, JavaScript, Attribute) to ensure user-supplied input is never rendered directly as executable script by the browser.
* **Secure Session Cookies:** Explicitly restrict cookie exposure by appending essential hardening headers:
    * `HttpOnly` to halt programmatic script read-access.
    * `Secure` to guarantee transmission exclusively over HTTPS connections.
    * `SameSite=Strict` or `SameSite=Lax` to negate cross-domain forged requests.
* **Implement CSRF Anti-Forgery Tokens:** Require unique, cryptographically secure, and unpredictable anti-CSRF tokens mapped explicitly to user sessions for all state-changing operations.
* **Deploy Content Security Policy (CSP):** Configure standard restrictive CSP headers to allow script execution only from explicitly whitelisted and trusted origins, neutralising any injected unauthorized scripts.
* **Adopt Whitelist Input Validation:** Transition application logic away from easily bypassable blacklist-based strings filtering to rigorous whitelist rule validations.
