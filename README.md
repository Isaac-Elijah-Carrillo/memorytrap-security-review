# MemoryTrap — Data Protection & Access Control Review

## Overview

This document summarizes an internal security review conducted for **MemoryTrap**, a mobile flashcard application. The focus of this assessment is to identify sensitive data, classify it appropriately, evaluate access control risks, and document potential threat scenarios related to user data protection.

This review is risk-identification focused and does not include the implementation of new safeguards at this stage.

---

## Application Context

**Application Type**
- Mobile application (iOS, Android planned)

**User Base**
- Individuals who create, store, and study user-generated flashcard content

### High-Level Architecture
- Authentication & Database: Firebase  
- Subscription Management: RevenueCat  
- Website Hosting: Vercel  

---

## Scope

### In Scope
- Identification of data collected, stored, and transmitted
- Data classification based on sensitivity
- High-level access control and identity considerations
- Threat scenarios related to unauthorized access, abuse, and data exposure

### Out of Scope
- Implementation of new security controls
- Deep configuration review of third-party services
- Penetration testing or active exploitation
- Performance or availability tuning

---

## Data Identification

### Data Collected
- Names
- Email addresses
- Password hashes
- Payment-related metadata (handled by third-party provider)
- Flashcards and decks (user-generated content)
- User-uploaded or captured images

### Data Stored
- User identifiers (name, email)
- Flashcards and decks
- Images
- Password hashes

### Data Transmitted
- Flashcards
- Decks
- Images

### Personally Identifiable Information (PII)
- Email addresses
- Names
- Payment-related information
- Password hashes (authentication material)

### Sensitive Non-PII
- Flashcard content
- Images

> User-generated content may unintentionally reveal sensitive personal context such as occupation, location, interests, or study focus.

---

## Data Classification

### Confidential
- Payment-related information
- Email addresses
- Password hashes
- Flashcards and decks
- Images

### Internal
- Application analytics
- Revenue metrics
- User behavior and engagement data
- Marketing and optimization data

### Public
- Website content
- Landing pages
- Terms of Service
- Privacy Policy

### Impact of Confidential Data Exposure
- Financial fraud
- Identity theft or impersonation
- User profiling based on content and images
- Loss of user trust and potential legal liability

---

## Access Control & Identity

### Authentication
- Apple Sign-In
- Google Sign-In
- Firebase Authentication

### Identity Proof
- Email addresses
- Apple IDs
- Google accounts
- Password hashes (where applicable)

### Authorization Model
- Access controls enforced through Firebase security rules
- Users are restricted to read/write/delete actions on their own flashcards and decks

### Key Risk
- Misconfigured or overly permissive database rules could result in broken access control and cross-user data exposure

### Least Privilege
- Users are granted access only to resources they own
- No intentional access to other users’ data is permitted

---

## Data Protection Controls

### Data in Transit
- Assumed to be protected via HTTPS/TLS enforced by platform defaults
- Not independently verified during this review

### Data at Rest
- Assumed to be encrypted using cloud-provider–managed encryption
- No direct validation performed at the time of assessment

### Secrets Management
- API keys were previously exposed in code and identified through scanning
- Issue has since been remediated

### Token Exposure Risk
- Token compromise could result in unauthorized account access
- Potential financial impact due to abuse of paid services or APIs

---

## Threat Scenarios

### Credential Compromise
- Unauthorized access to flashcards, decks, and images
- Enables user profiling and potential impersonation

### Token Theft
- Risk acknowledged but not fully analyzed
- Requires a deeper understanding of the token lifecycle, expiration, and revocation

### Broken Access Control
- Misconfigured database rules could expose other users’ data

### Abuse & Cost-Exhaustion
- Excessive database read/write activity could degrade service availability
- Potential for elevated operational costs

### Most Likely Threats
- Database abuse through excessive operations
- Weak access control configuration

### Most Damaging Threats
- Account takeover via credential or token compromise
- Cost-exhaustion attacks impacting availability and billing

---

## Logging, Monitoring & Detection

### Security-Relevant Events
- Authentication attempts
- Authorization failures
- Abnormal database read/write patterns

### Data That Should Not Be Logged
- Payment information
- Sensitive PII

> Payment processing logs are primarily handled by the subscription provider.

### Detection Approach
- Platform-provided logs and analytics
- Identification of abnormal usage patterns

### Potential Indicator of Compromise
- Sudden spikes in database write activity from a single account

---

## Risk & Mitigation Summary

**Highest Identified Risk**
- Abuse of database operations leading to denial-of-service or cost escalation

**Existing Mitigations (Assumed)**
- Access control rules
- Platform-enforced usage limits

### Control Types
- **Preventative:** Access controls, limits
- **Detective:** Logging and monitoring
- **Corrective:** Not explicitly defined at the time of review

---

## Incident Readiness

- **Detection:** Log and usage review
- **Initial Response:** Identify and validate the incident
- **Containment:** Potential account isolation or access revocation

### Post-Incident Improvements
- Tighter security rules
- Rate limiting
- Improved guardrails and abuse detection

---

## Assumptions & Identified Knowledge Gaps

During this review, several controls were assumed based on platform defaults but not independently verified.

### Assumptions
- Encryption in transit is enforced via HTTPS/TLS
- Encryption at rest is managed by the cloud provider
- Platform-level abuse protections exist

### Identified Gaps
- Authentication token lifecycle and revocation behavior
- TLS configuration and certificate handling
- Formalized incident containment and recovery procedures

These gaps have been documented for future validation and improvement.

---

## Reflection

This review highlighted the importance of security-focused thinking beyond basic functionality. Identifying data sensitivity, access boundaries, and threat scenarios provides a clearer understanding of user risk and trust.

The assessment aligns closely with least privilege, risk management, and logging/monitoring principles emphasized in foundational security frameworks and Security+ objectives.

---

## Next Steps
- Validate encryption and token-handling assumptions
- Review platform-specific security capabilities in depth
- Formalize incident response and containment procedures
