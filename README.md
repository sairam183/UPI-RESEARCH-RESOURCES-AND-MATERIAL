# UPI-RESEARCH-RESOURCES-AND-MATERIAL

> **Research Focus:** Security Architecture of UPI-based QR Payment Systems  
> **Primary Case Study:** Google Pay (TPAP) & NPCI Infrastructure 
> **Repository:** Comprehensive research material, flow diagrams, technical 
> documentation, and security analysis for UPI QR payment systems

---

##  What Is This Repository?

This repository contains the complete research material accumulated  focused on understanding and analyzing the **security architecture of UPI-based QR payment systems**, with a specific focus on 
the **Google Pay (GPay) application** as a Third Party Application Provider 
(TPAP) operating within the **NPCI ecosystem**.

The research covers:
- The complete end-to-end UPI QR payment transaction flow
- Cryptographic mechanisms (AES-GCM, RSA-OAEP, mTLS)
- Device binding and SIM verification protocols
- NPCI infrastructure components (UPI Switch, VPA Registry, HSM)
- Known threat vectors and attack surfaces
- Regulatory and compliance frameworks (RBI, NPCI)
- A practical penetration testing checklist for future researchers

> ⚠️ **Important:** All research is based on publicly available 
> information, official regulatory documents, and open-source technical 
> specifications. No proprietary systems were accessed or tested during 
> this research.

---

## 🗂️ Repository Structure
UPI-RESEARCH-RESOURCES-AND-MATERIAL/
│
├── 📁 FINAL RESEARCH FILES/
├── 📁 IMPORTANT_research papers/
├── 📁 diagrams/
├── 📁 images/
├── 📁 research papers I found/
│

├── 📄 ANALYSIS OF SOME RESEARCH PAPERS.docx
├── 📄 FINAL RESEARCH QUES ON WHICH FINAL RESEARCH
│ FOLDER IS BASED.docx
├── 📄 Comprehensive upi application pentesting checklist.docx
├── 📄 RESEARCH links (from where I extracted content).md
└── 📄 README.md


---

## 📁 Folder & File Descriptions

---

### 📁 FINAL RESEARCH FILES

This is the **most important folder** in the repository. It contains the 
final, curated, and structured research outputs produced after synthesizing 
all collected material. The content in this folder is organized around the 
core research questions and represents the deepest level of analysis 
conducted during the internship.

The files in this folder cover:

#### 🔷 1. UPI Ecosystem & Architecture Overview
A comprehensive breakdown of the UPI ecosystem including all key 
stakeholders — NPCI, PSPs (Payment Service Providers), TPAPs (Third Party 
Application Providers like Google Pay), Payer Banks, Payee Banks, and 
Merchants. Covers the distinction between PSP and TPAP roles, how NPCI 
acts as the central switch and governance body, and how the layered 
architecture enables real-time interbank transfers.

Key topics covered:
- What UPI is and how it differs from NEFT, RTGS, IMPS, and card payments
- The role of NPCI as the L7 super-switch
- How Google Pay operates as a TPAP partnering with PSP banks
- The VPA (Virtual Payment Address) system and Central Mapper (VPA Registry)
- UPI transaction types: Pay, Collect, QR-based, AutoPay, UPI Circle

#### 🔷 2. QR Code Structure & Payment Flow
Detailed analysis of the UPI QR code format as defined by NPCI, covering 
both static and dynamic QR code variants. Includes a full breakdown of the 
UPI payment URI schema, parameter definitions, and how QR codes are 
generated, decoded, and validated by UPI applications.

Key topics covered:
- UPI QR URI format: `upi://pay?pa=&pn=&am=&cu=&tn=`
- Parameters: `pa` (payee VPA), `pn` (payee name), `am` (amount), 
  `cu` (currency), `tn` (note), `tr` (transaction reference), 
  `mc` (merchant category code)
- Static vs Dynamic QR codes — differences and use cases
- QR scanning using Android CameraX API and Google ML Kit
- Local QR validation before any network call is made
- How the app performs VPA resolution (ReqValAdd) via the PSP and 
  NPCI Switch before displaying the verified merchant name

#### 🔷 3. Cryptographic Mechanisms & Security Architecture
The most technically detailed section of the research. Covers the complete 
cryptographic stack used in UPI to protect the user's PIN and transaction 
credentials from the moment of entry on the device to verification at the 
bank's HSM.

Key topics covered:
- **NPCI Common Library:** Its role as an isolated, NPCI-controlled 
  component for PIN entry and credential generation. The host TPAP app 
  (Google Pay) never has access to the plaintext PIN.
- **AES-GCM encryption:** How the transaction digest is encrypted using 
  AES-256 in Galois/Counter Mode, providing both confidentiality and 
  integrity protection.
- **RSA-OAEP encryption:** How the PIN block and encrypted digest are 
  further wrapped using RSA with Optimal Asymmetric Encryption Padding 
  using NPCI's public key, ensuring only NPCI's HSM can decrypt.
- **Why dual encryption:** The rationale for combining symmetric and 
  asymmetric cryptography in the credential protection flow.
- **NPCI HSM operations:** How NPCI's FIPS 140-2 Level 3 certified HSM 
  decrypts the credential block and re-encrypts the PIN block using the 
  destination bank's key before forwarding it for verification.
- **Bank HSM PIN verification:** The final PIN verification step at the 
  Payer Bank's HSM.

#### 🔷 4. Device Binding & SIM Verification
Analysis of the device binding mechanism — one of the two factors in UPI's 
two-factor authentication model. Covers how UPI ties a registration to a 
specific device and SIM card, what happens when either changes, and the 
security implications.

Key topics covered:
- SIM-based verification using silent SMS during registration
- NPCI's Device Binding Checklist requirements for PSP applications
- Android Keystore, TEE (Trusted Execution Environment), and StrongBox 
  usage in Google Pay for hardware-backed key storage
- Play Integrity API (formerly SafetyNet) for device attestation
- What happens when a user changes SIM or device — re-registration flow
- SIM swap attack surface and its implications for UPI security

#### 🔷 5. End-to-End Transaction Flow Analysis
A phase-by-phase walkthrough of the complete UPI QR payment transaction, 
from the moment the user opens the app to the final payment confirmation 
notification. Based on synthesis of NPCI API specifications, Google Pay 
developer documentation, and sequence diagrams created during the research.

The flow is broken into 6 major phases:
1. **QR & Validation** — Scan, decode, local validate, VPA resolution
2. **PIN & Credential Protection** — NPCI Common Library invocation, 
   digest generation, AES-GCM + RSA-OAEP encryption
3. **Secure Transmission** — ReqPay construction, mTLS to PSP, 
   PSP to NPCI Switch, risk checks
4. **Bank Authentication** — NPCI HSM re-encryption, debit request 
   to Payer Bank, HSM PIN verify, CBS balance check and debit
5. **Credit & Response** — Credit to Payee Bank, DebitSuccess, 
   RespPay SUCCESS propagation
6. **Completion** — Signed transaction response to merchant, 
   Payment Successful to user, SMS notifications

#### 🔷 6. Threat Analysis & Attack Vectors
Security-focused analysis of known and theoretical attack vectors in the 
UPI ecosystem. Each threat is analyzed from the perspective of attack 
methodology, why it works, and existing or recommended mitigations.

Threats covered:
- **QR Code Spoofing & Phishing:** Physical sticker replacement of merchant 
  QRs, digital fake QR distribution via messaging apps
- **Deep Link Abuse:** Crafting malicious `upi://` URIs that pre-populate 
  fraudulent payment details, delivered via phishing sites or malicious apps
- **Device Binding Bypass:** SIM swap attacks, emulator-based 
  registration attempts, device ID spoofing
- **UPI Collect Request Abuse:** Social engineering using collect 
  requests framed as "receiving money," widely used in e-commerce fraud
- **Phone Number Enumeration:** Sequential VPA querying to discover 
  active UPI users via the ReqValAdd API
- **Man-in-the-Middle Risks:** SSL unpinning on rooted devices, 
  rogue WiFi interception, malicious VPN/proxy apps

##### Other details can be found in the final research questions....docx file. Just this folder files needs to be mapped with the questions present in the ques file. 
---

### 📁 IMPORTANT_research papers

This folder contains the most technically significant and directly relevant 
academic and technical papers identified during the research. These were 
selected from a larger pool of papers found during the literature review 
as being most directly applicable to the UPI security architecture topic.

Papers in this folder cover:
- Academic analysis of real-time payment system security architectures
- Cryptographic protocol analysis relevant to mobile payment systems
- Research on hardware security module implementations in financial systems
- Studies on mobile application security and Android security architecture
- Papers on QR code security vulnerabilities and countermeasures
- Research on device binding and SIM-based authentication mechanisms
- Analysis of social engineering attacks targeting digital payment users

> These papers formed the academic foundation of the research and are 
> cited where relevant in the final internship report.

---

### 📁 diagrams

This folder contains the puml and md file containing the code of the sequence and other uml diagrams or images that are present in the images folder. 

---

### 📁 images

This folder contains all the flow diagrams created during the research to 
visualize the UPI architecture and transaction flows. These diagrams were 
created from scratch based on synthesis of NPCI specifications, Google Pay 
developer documentation, and technical analysis.

#### 📊 trimmed_upi_flow.png
**Detailed 6-Phase UPI Sequence Diagram**

A sequence diagram showing the complete UPI QR payment flow broken into 
6 security-labeled phases across all system actors: User, UPI App, NPCI 
Common Library, PSP, NPCI Switch, Payer Bank, Bank CBS, and Merchant. 
Each numbered step (1–26) shows the exact message exchanged between 
components. Phases: (1) QR & Validation, (2) PIN & Credential Protection, 
(3) Secure Transmission, (4) Bank Authentication, (5) Credit & Response, 
(6) Completion.

#### 📊 upi_gpay_flow_1.png
**UPI QR Payment Flow — Complete High-Level UML (Version 1)**

High-level UML architecture diagram with 19 numbered steps showing the 
complete transaction pathway across Payer Side App (Google Pay/TPAP), 
Payer PSP (e.g., Axis Bank UPI Switch), NPCI Infrastructure (UPI Switch 
Hub, VPA Registry/Central Mapper, HSM), Payee PSP (e.g., HDFC), Payer 
Bank systems (UPI Module, HSM/PIN Verify, CBS/Debit), Payee Bank systems 
(UPI Module, HSM/PIN Verify, CBS/Credit), and Merchant/Beneficiary. 
Includes a notes section explaining key security properties (TLS 1.3, 
PIN never in plaintext, merchant notification on success).

#### 📊 upi_gpay_flow_2.png
**UPI QR Payment — Complete Flow High-Level UML (Version 2)**

An enhanced version of the high-level UML with more granular bank-side 
module breakdown. Shows the Google Pay → PSP → NPCI → Banks chain with 
individual module-level detail for both Payer Bank (UPI Module, HSM PIN 
Verify, CBS Debit) and Payee Bank (UPI Module, HSM PIN Verify, CBS Credit). 
19 numbered steps with legend distinguishing Request/Forwarding arrows from 
Response arrows.

#### 📊 upi_mermaid_flow.png
**Ultra-Detailed Multi-Phase Sequence Diagram (15 Phases)**

The most comprehensive diagram in the repository. A full sequence diagram 
spanning 15 distinct phases of the UPI transaction lifecycle across all 
system actors including Google Pay, NPCI Common Library, Android Keystore/
TEE, Payer PSP, NPCI UPI Switch, NPCI HSM, NPCI VPA Registry, Payer Bank 
UPI Module, Payer Bank HSM, Payer Bank CBS, Payee PSP, Payee Bank, and 
Merchant. Phases covered:
- Phase 0: App Launch Security
- Phase 1: QR Scan & Decode
- Phase 2: Local Validation
- Phase 3: VPA Resolution
- Phase 4: Human Confirmation
- Phase 5: Session Establishment
- Phase 6: UPI PIN Entry
- Phase 7: Credential Construction
- Phase 8: ReqPay Transmission
- Phase 9: NPCI Switch Processing
- Phase 10: HSM Decryption
- Phase 11: Payer Bank Auth & Debit
- Phase 12: Payee Credit
- Phase 13: Merchant Propagation
- Phase 14: Receipt & Storage
- Phase 15: Post-Transaction Notifications

The Mermaid source code for this diagram is also available (shared 
separately) for rendering or modification.

#### 📊 upi_qr_payment_flow.png
**Comprehensive Component-Level Architecture Diagram**

A detailed component interaction diagram showing the Google Pay internal 
architecture (TPAP + NPCI Common Library + Android Keystore/TEE/StrongBox) 
communicating with the Payer PSP, NPCI UPI Switch (Hub), NPCI HSM (RSA/
Credential Decryption), NPCI VPA Registry (Central Mapper), Payer Bank 
(UPI Module → HSM PIN Verification → CBS Balance+Debit), Payee PSP, Payee 
Bank CBS (Credit), and Merchant/Beneficiary. All inter-component messages 
are labeled including device attestation verdicts, opaque credential blocks, 
token responses, VPA verification, DebitSuccess, credit notifications, and 
the final status response to the app.


---

### 📁 research papers I found

This folder contains the broader collection of research papers, articles, 
and technical documents encountered during the literature review phase 
of the research. Unlike the `IMPORTANT_research papers` folder, this 
collection includes all papers found — including those that were 
peripherally relevant, provided background context, or were ultimately 
not directly cited in the final report.

Topics spanned by papers in this folder include:
- General digital payment system security
- Mobile banking application vulnerabilities
- QR code attack research
- Android security architecture
- Fraud detection in real-time payment systems
- Network security protocols (TLS, mTLS) in financial systems
- Social engineering in fintech contexts

> This folder is useful for anyone wanting to go deeper into adjacent 
> topics or conduct a more comprehensive literature review.

---

### 📄 ANALYSIS OF SOME RESEARCH PAPERS.docx

This document contains structured analysis of selected research papers 
reviewed during the internship. For each paper, the analysis covers:

- **Paper title and source**
- **Core argument / main findings**
- **Methodology used**
- **Relevance to UPI security research**
- **Key technical concepts introduced**
- **Limitations or gaps identified**
- **How the findings informed this research**

This document is particularly useful for new interns as it saves the 
time of reading every paper in full — the analysis extracts the most 
relevant insights and maps them to the UPI security context. Papers 
analyzed include academic work on mobile payment security, HSM 
implementations, device binding protocols, QR code vulnerabilities, 
and cryptographic protocol analysis relevant to the UPI architecture.

---

### 📄 FINAL RESEARCH QUES ON WHICH FINAL RESEARCH FOLDER IS BASED.docx

This document contains the **structured research questions** that guided 
the entire research process and form the backbone of the `FINAL RESEARCH 
FILES` folder. Understanding these questions is essential for any new 
intern picking up this work, as they define the scope, boundaries, and 
direction of the research.

The research questions are organized into thematic clusters:

**Cluster 1 — Architecture & Flow:**
- How does a UPI QR payment transaction flow end-to-end?
- What are the roles of each component (TPAP, PSP, NPCI Switch, 
  VPA Registry, HSM, Bank CBS)?
- How does Google Pay as a TPAP interact with its PSP bank?
- What messages are exchanged at each step of the transaction?

**Cluster 2 — Cryptography & Security Mechanisms:**
- How is the UPI PIN protected from the moment of entry to bank 
  verification?
- What specific cryptographic algorithms are used (AES-GCM, RSA-OAEP)?
- What is the role of the NPCI Common Library in credential protection?
- How does the NPCI HSM participate in PIN verification?
- How does device binding work technically?

**Cluster 3 — QR Code Security:**
- What is the structure of a UPI QR code?
- What is the difference between static and dynamic QR codes?
- How does the app validate a QR code before initiating payment?
- What are the known attack vectors specific to QR-based UPI payments?

**Cluster 4 — Threat Analysis:**
- What are the major threat vectors in the UPI ecosystem?
- How do QR spoofing, deep link abuse, and collect request fraud work?
- What is phone number enumeration and how is it possible in UPI?
- What are the SIM swap risks to UPI device binding?

**Cluster 5 — Regulation & Compliance:**
- What are NPCI's mandatory security requirements for UPI applications?
- What does FIPS 140-2 Level 3 mean and why does it matter for UPI HSMs?
- What are RBI's mandates for mobile payment security?
- What is the UDIR framework for dispute resolution?

> This document should be the **first file read** by any new intern 
> joining this project, as it contextualizes all subsequent research.

---

### 📄 RESEARCH links (from where I extracted content).md

This file contains the complete, deduplicated list of **184 research 
sources** used during this research, organized by category. Every link 
has been verified and represents a legitimate, publicly accessible 
source. AI-generated mirrors and unreliable sources have been excluded.

**Source categories and counts:**

| Category | Number of Sources |
|---|---|
| Android / App Architecture | 20 |
| Device Registration / Binding / SIM | 17 |
| UPI Architecture and System Design | 18 |
| NPCI Official & Regulatory Sources | 17 |
| NPCI Common Library & Technical Specs | 7 |
| PSP / TPAP / Payment Gateway | 14 |
| VPA / UPI ID | 12 |
| QR Code Structure & Standards | 20 |
| QR Security / Fraud | 7 |
| UPI Registration / How-To | 12 |
| Google Pay Specific | 5 |
| UPI Ecosystem — General | 21 |
| QR Code Libraries / Open Source | 6 |
| YouTube Resources | 8 |

**Key sources include:**
- NPCI Official: `npci.org.in/product/upi`, BHIM UPI Guidelines 2026, 
  OC No. 226
- RBI: Mobile payment FAQs, Working Group on FinTech and Digital Banking
- NPCI Common Library Specification: `github.com/librefin-in/cl-specification`
- Google Developers: ML Kit, Google Pay India API, Android Keystore
- Academic/Technical: Medium deep-dives on UPI system design, 
  cryptography perspective, NPCI architecture
- Security: OWASP resources, QR phishing research, device binding 
  security analyses
- YouTube: UPI architecture walkthroughs, payment flow explanations

> This file is the **complete bibliography** of the research and should 
> be the primary reference for anyone wanting to go deeper into any 
> specific topic covered in this project.

---

### 📄 Comprehensive UPI APPLICATION PENTESTING CHECKLIST (based on the research conducted)

This file contains a detailed practical app sec checklist for any researcher verifying all the claims made in this research project by doing hands on practical. This might save the practioner's time. 

---

## 🔄 How This Research Was Conducted

The research followed a structured methodology:

**Phase 1: Literature Review**
│ ├── Collected 184+ sources across 14 categories
│ ├── Read NPCI official documentation and circulars
│ ├── Studied academic papers on mobile payment security
│ └── Analyzed Google Pay and UPI developer documentation
│

**Phase 2: Architecture Mapping**
│ ├── Mapped complete UPI ecosystem stakeholders
│ ├── Documented all component interactions
│ ├── Created high-level UML diagrams
│ └── Created detailed sequence diagrams
│

**Phase 3: Security Analysis**
│ ├── Analyzed cryptographic mechanisms layer by layer
│ ├── Studied device binding and SIM verification protocols
│ ├── Identified threat vectors from research and reported cases
│ └── Mapped regulatory requirements to technical controls
│

**Phase 4: Synthesis & Documentation**
│ ├── Wrote structured research question analysis
│ ├── Analyzed key research papers
│ ├── Created comprehensive flow diagrams (5 diagrams)
│ └── Produced final internship research report
│


---

## 🚀  Where to Start

If you are picking up this project for the first time, follow this 
reading order:

**Day 1–2: Understand the Scope**
1. Read `FINAL RESEARCH QUES ON WHICH FINAL RESEARCH FOLDER IS BASED.docx`
   — understand the research questions
2. Read the `FINAL RESEARCH FILES` folder descriptions above
3. Look at all 5 diagrams in the `diagrams/` folder to get a visual 
   understanding of the system

**Day 3–5: Build Foundation**
1. Read NPCI's UPI product page: https://www.npci.org.in/product/upi
2. Read the Medium article on UPI system design: 
   https://medium.com/@rishabhkochar27/upi-system-design-the-complete-deep-dive-part-i-b6c0028ec4f5
3. Read the UPI cryptography article: 
   https://medium.com/@math.club/understanding-upi-transactions-a-systems-and-cryptography-perspective-50a812d697d0
4. Read `ANALYSIS OF SOME RESEARCH PAPERS.docx`

**Day 6–10: Go Deep**
1. Read NPCI Common Library spec: 
   https://github.com/librefin-in/cl-specification
2. Read BHIM UPI Guidelines 2026 (official NPCI PDF)
3. Read NPCI Device Binding Checklist
4. Study the `IMPORTANT_research papers` folder

**Week 3 onwards: Practical Work**
1. Refer to the standalone **UPI App Penetration Testing Checklist** 
   (available in this repository)
2. Set up the testing environment as described in the checklist
3. Begin with static analysis of UPI APKs
4. Progress through the checklist systematically

---

## 📊 Key Technical Concepts Quick Reference

| Concept | What It Is | Where to Learn More |
|---|---|---|
| VPA | Virtual Payment Address — UPI ID like name@bank | `FINAL RESEARCH FILES` → Section on VPA |
| PSP | Payment Service Provider — bank providing UPI switch | Research links category: PSP/TPAP |
| TPAP | Third Party App Provider — Google Pay, PhonePe | Research links category: PSP/TPAP |
| NPCI Common Library | NPCI-provided SDK for secure PIN handling | `github.com/librefin-in/cl-specification` |
| AES-GCM | Symmetric authenticated encryption for transaction digest | `FINAL RESEARCH FILES` → Cryptography |
| RSA-OAEP | Asymmetric encryption wrapping PIN block | `FINAL RESEARCH FILES` → Cryptography |
| mTLS | Mutual TLS — both client and server authenticate | `FINAL RESEARCH FILES` → Transport Security |
| HSM | Hardware Security Module — FIPS 140-2 Level 3 certified | `FINAL RESEARCH FILES` → NPCI Infrastructure |
| ReqValAdd | NPCI API call to resolve/verify a VPA | NPCI API Specification PDF |
| ReqPay | NPCI API call to initiate a payment | NPCI API Specification PDF |
| TEE | Trusted Execution Environment in Android processor | Android Security docs |
| StrongBox | Dedicated security chip in newer Android devices | Android Keystore docs |
| Static QR | Fixed QR with VPA only — payer enters amount | `FINAL RESEARCH FILES` → QR Section |
| Dynamic QR | Per-transaction QR with amount pre-encoded | `FINAL RESEARCH FILES` → QR Section |
| Device Binding | Linking UPI registration to specific device+SIM | NPCI Device Binding Checklist |

---

## ⚖️ Legal & Ethical Notice

This repository is for **educational and research purposes only.**

- All content is based on publicly available information
- No NPCI, bank, or payment system infrastructure was accessed or 
  tested without authorization
- The penetration testing checklist is intended for use only on systems 
  you own or have explicit written permission to test
- Unauthorized testing of UPI applications or banking infrastructure 
  is a criminal offense under the **IT Act, 2000** and the 
  **Payment and Settlement Systems Act, 2007**
- Bug bounty programs are available for authorized security research — 
  refer to the pentesting checklist for links

---

## 📬 Contributing & Continuing This Work

If you are continuing this research, here are the most 
valuable directions to take it forward:

1. **Practical Security Testing:** Set up an authorized testing 
   environment and execute the penetration testing checklist. 
   Document all findings with evidence.

2. **Biometric Authentication Analysis:** NPCI's OC No. 226 introduced 
   biometric authentication for UPI. Research the security implications 
   and implementation details.

3. **Cross-Border UPI Security:** UPI is expanding internationally 
   (Singapore PayNow linkage, UAE, etc.). Research the security 
   architecture of cross-border UPI flows.

4. **Fraud Detection System Analysis:** Research NPCI's and banks' 
   ML-based fraud detection systems — their architecture, effectiveness, 
   and adversarial robustness.

5. **Post-Quantum Cryptography:** UPI currently relies on RSA. 
   Research migration pathways to post-quantum cryptographic schemes.

6. **UPI Lite Security:** UPI Lite enables small offline transactions. 
   Analyze its security model compared to regular UPI.

---

## 🙏 Acknowledgements

Sources: NPCI official documentation, RBI guidelines, Google Developer 
documentation, OWASP, academic research papers, and the broader 
open-source security research community.

---

*Last updated: 2026*
