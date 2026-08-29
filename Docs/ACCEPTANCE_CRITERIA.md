# Acceptance Criteria: Sentinel Scam Validator (SSV)

**Document Version:** 1.0.0  
**Status:** Approved / Baseline  
**Date:** August 29, 2026  
**Module:** User Stories & Acceptance Criteria  
**Parent Document:** [PROJECT_CHARTER.md](file:///C:/Users/yolanda/OneDrive/Desktop/Agile-Software-Development/Docs/PROJECT_CHARTER.md)  
**Related Document:** [REQUIREMENTS_SPECIFICATION.md](file:///C:/Users/yolanda/OneDrive/Desktop/Agile-Software-Development/Docs/REQUIREMENTS_SPECIFICATION.md)  

---

## 1. Overview & Testing Philosophy

This document defines the formal Acceptance Criteria for the Sentinel Scam Validator (SSV) MVP. All criteria are written in **Behavior-Driven Development (BDD) / Gherkin format** (`Given - When - Then`) to provide verifiable contracts for engineering, quality assurance (QA), and automated test suites (e.g., Playwright, Jest, PyTest, Cucumber).

---

## 2. User Story Breakdown & Scenarios

### User Story 1: Analyze a Suspicious Phishing URL (Feature 1: Scam Checker)
> **As an** internet user receiving an unexpected communication with an unfamiliar link  
> **I want to** paste the link into Sentinel Scam Validator  
> **So that** I know if the website is safe to visit before I click it.

#### Scenario 1.1: Obvious Phishing Link with Typosquatting / Homoglyph
* **Given** the user is on the Sentinel Scam Validator homepage
* **When** the user pastes `http://secure-login.paypal.verify-notice.top/account` into the scanner input
* **And** clicks the **"Analyze"** button
* **Then** the application processes the request in $\le 1.5$ seconds
* **And** the result displays a **"High Risk"** banner with a score $\ge 70 / 100$
* **And** the reasons list includes:
  * *"Suspicious high-risk top-level domain (.top)"*
  * *"Brand name spoofing detected in subdomain"*
  * *"Unencrypted insecure connection (HTTP)"*
* **And** the recommendations prominently display:
  * *"Do not click this link"*
  * *"Do not enter your login credentials or payment details"*
  * *"Delete the message containing this link"*.

#### Scenario 1.2: Shortened URL Redirecting to an Unverified Target
* **Given** a user pastes a shortened link `https://bit.ly/3xSampleScam`
* **When** the user submits the link for analysis
* **Then** the system unshortens the URL to reveal the final destination domain
* **And** if the target domain has no valid reputation or exhibits scam traits, classifies the result as **"Suspicious"** or **"High Risk"**
* **And** displays the expanded destination URL so the user can see where it leads.

#### Scenario 1.3: Verified Legitimate Institutional Domain
* **Given** a user inputs a known authentic URL: `https://www.chase.com/personal/banking`
* **When** the user runs the analysis
* **Then** the system computes a **"Low Risk"** classification (Score: $0 - 29$)
* **And** displays confirmation: *"Legitimate brand domain verified"*, *"Valid SSL/TLS certificate found"*
* **And** advises: *"Always verify that the browser address bar matches the intended destination before entering login details."*

---

### User Story 2: Analyze an Urgent Scam SMS/Message (Feature 2: Scam Risk Score)
> **As a** smartphone user receiving an alarming text message regarding an account or delivery  
> **I want to** paste the message text into the validator  
> **So that** I can determine whether the message is a fraudulent attempt to panic me.

#### Scenario 2.1: Smishing Message with Artificial Urgency & Threatened Action
* **Given** an incoming message: *"FINAL NOTICE: Your bank account will be suspended within 24 hours due to suspicious activity. Call 1-800-555-0199 or verify your identity immediately."*
* **When** the user pastes this text and clicks **"Analyze"**
* **Then** the system redacts sensitive phone numbers before persisting to logs
* **And** evaluates the text and returns a score $\ge 70$ (**"High Risk"**)
* **And** details reasons:
  * *"High-pressure deadline tactic ('within 24 hours') detected"*
  * *"Coercive account suspension threat identified"*
  * *"Unverified emergency contact channel"*
* **And** displays recommendations:
  * *"Do not reply to this message"*
  * *"Do not call the number provided"*
  * *"Contact your financial institution directly through their verified official app or the phone number on your card"*.

#### Scenario 2.2: Lottery / Unexpected Prize Scam Message
* **Given** a message: *"Congratulations! You have been selected as the winner of a $1,000 Walmart gift card! Claim here: http://free-gift-promo.xyz"*
* **When** the user analyzes the message
* **Then** the system identifies the dual threat (financial reward bait + risky `.xyz` TLD)
* **And** assigns a **"High Risk"** rating with reasons:
  * *"Unsolicited financial prize or reward bait"*
  * *"Suspicious web destination (.xyz)"*
* **And** prescribes: *"Do not click the claim link — legitimate retailers do not award random prizes via SMS."*

---

### User Story 3: Context-Sensitive Safety Advice (Feature 3: Recommendations)
> **As a** non-technical user receiving a suspicious message  
> **I want to** be given clear, imperative commands on what action to take  
> **So that** I avoid taking risky steps out of uncertainty.

#### Scenario 3.1: Recommendations for High Risk Results
* **Given** a scan returns a **High Risk** score ($70 - 100$)
* **When** the result card is rendered
* **Then** the recommendation block shall display with red visual emphasis
* **And** contain at least three unambiguous imperatives:
  1. *"Do not click any embedded links."*
  2. *"Do not reply or share one-time authentication codes (OTPs)."*
  3. *"Block and report the sender."*

#### Scenario 3.2: Recommendations for Suspicious Results
* **Given** a scan returns a **Suspicious** score ($30 - 69$)
* **When** the result card is rendered
* **Then** the recommendation block shall display with amber/warning visual emphasis
* **And** contain targeted guidance:
  1. *"Verify with the official source using public, published contact channels."*
  2. *"Do not use phone numbers or links provided inside this message."*
  3. *"Confirm the sender’s identity before sharing any information."*

#### Scenario 3.3: Recommendations for Low Risk Results
* **Given** a scan returns a **Low Risk** score ($0 - 29$)
* **When** the result card is rendered
* **Then** the recommendation block shall display with green/reassuring visual emphasis
* **And** contain standard hygiene guidance:
  1. *"No common scam patterns detected."*
  2. *"Exercise standard digital hygiene: ensure the sender is familiar and verify URLs."*

---

### User Story 4: Input Validation & Boundary Conditions

#### Scenario 4.1: Empty or Pure Whitespace Input
* **Given** the user is focused on the input text area
* **When** the user leaves the box empty or enters only whitespace characters and clicks "Analyze"
* **Then** no network request is sent to the backend scoring engine
* **And** a client-side validation error is shown: *"Please paste a message or URL to analyze."*
* **And** the input field is highlighted with an error state.

#### Scenario 4.2: Maximum Length Constraint
* **Given** a user attempts to paste a message exceeding 4,000 characters
* **When** the content is entered
* **Then** the UI indicates that characters beyond 4,000 are truncated or prompts the user to reduce length
* **And** the backend rejects payloads $> 4,000$ characters with HTTP `422 Unprocessable Entity`.

#### Scenario 4.3: HTML and Script Tag Neutralization (XSS Prevention)
* **Given** a user inputs malicious markup (e.g., `<script>alert('xss')</script>` or `<img src=x onerror=alert(1)>`)
* **When** the result page renders the submitted text or extracted reasons
* **Then** the characters are strictly escaped and rendered as plaintext without executing any DOM script.

---

### User Story 5: Feedback & History Permalinks

#### Scenario 5.1: Generating and Sharing Scan Result
* **Given** a completed scan
* **When** the result view loads
* **Then** a unique URL containing a scan token (`/scan/[UUIDv4]`) is generated
* **And** clicking **"Copy Share Link"** copies this sanitized permalink to the clipboard.

#### Scenario 5.2: User Submits Accuracy Feedback
* **Given** a completed scan result
* **When** the user clicks the thumbs-down button (*"False Positive / Inaccurate"*)
* **Then** an optional feedback modal prompts: *"Why do you think this result is incorrect?"*
* **And** submitting feedback sends an asynchronous payload to `/api/v1/scan/{id}/feedback` with HTTP 200 confirmation.

---

## 3. Definition of Done (DoD) Checklist for Stories

Before any MVP user story is marked as **Done**, it must satisfy:

- [ ] **Functional Completion:** All Gherkin scenarios pass in end-to-end integration tests.
- [ ] **Unit Test Coverage:** Core scoring and heuristic parser functions have $\ge 85\%$ automated unit test coverage.
- [ ] **Performance Threshold:** Average response time meets the p95 SLA ($\le 1.5\text{ seconds}$).
- [ ] **Accessibility (a11y):** Screen reader passes all form inputs, contrast ratios $\ge 4.5:1$, keyboard navigable.
- [ ] **Security Audit:** Code passes static analysis (SAST) for XSS, SQL injection, and PII leakage.
- [ ] **Code Review:** Approved by at least one peer engineer and the product owner.
