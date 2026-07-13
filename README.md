# API Security Assessment & Vulnerability Analysis (crAPI)

This repository documents a structured API Penetration Testing engagement performed on the **completely rest-based microservices application (crAPI)** lab environment using **Burp Suite**. The focus was to identify critical business logic flaws, session management anomalies, and data boundary bypasses.

---

## 🛠️ Testing Environment & Toolkit
- **Operating System:** Kali Linux / Windows Host Environment
- **Interception Proxy:** Burp Suite Community Edition
- **Target Architecture:** Microservices deployed via Docker containers

---

## 🔍 Vulnerability Registry & PoC Evidence

### 1. API Reconnaissance & Mapping
- **Action:** Mapped core microservice communication patterns across endpoints.
- ![API Dashboard](./01_api_recon_dashboard.jpeg)

### 2. Session Integrity & JWT Parsing
- **Action:** Analyzed token structure parameters and verification configurations via cryptographic decoders.
- ![JWT Structure Check](./02_jwt_token_structure_decode.jpeg)

### 3. Broken Object Level Authorization (BOLA / IDOR)
- **Target Endpoint:** `/identity/api/v2/vehicle/...`
- **Impact:** Absence of strict contextual tenant validation allowed arbitrary direct object querying.
- ![BOLA Bypass](./03_bola_endpoint_bypass.jpeg)

### 4. Authentication Exception Handling & Hardening
- **Target Endpoint:** `/identity/api/auth/v3/check-otp`
- **Observation:** Analyzed error boundaries where malformed padding or continuous automation triggered unhandled backend runtime exceptions (`HTTP 500`).
- ![Authentication Logic Error](./05_auth_validation_500_error.jpeg)

### 5. Business Logic Flaw (Quantity Parameter Manipulation)
- **Target Endpoint:** `/workshop/api/shop/orders`
- **Impact:** Missing server-side mathematical integrity bounds allowed negative input validation bypass, tampering with core credit balance schemas.
- ![Business Logic Flaw](./06_business_logic_negative_quantity.jpeg)

### 6. Mass Assignment (Property Injection Attack)
- **Target Endpoint:** `/workshop/api/shop/orders`
- **Impact:** Implicit object-property binding accepted non-whitelisted administrative parameters directly from client-supplied JSON payloads.
- ![Mass Assignment Proof](./07_mass_assignment_status_injection.jpeg)

---

## 🛡️ Strategic Remediation Blueprints
- **Imposed Data Transfer Objects (DTOs):** Enforce strict property whitelisting at the controller tier to mitigate arbitrary model additions.
- **Parametric Balance Boundaries:** Implement server-side verification layers confirming numerical values sit strictly within valid logical bounds (`quantity > 0`).
