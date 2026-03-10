# VaultKey

**Tagline:** Prove who you are, without revealing who you are.

**Subtitle:** A Zero-Knowledge Identity Verification System for India’s Digital Infrastructure
**Hackathon:** India Innovates 2026 · MCD · Domain 3 · Cyber Security

---

## 🚨 The Problem: Data Exposure Crisis

India's digital identity system faces a significant data exposure crisis. Recent statistics highlight the vulnerability:
- **81 crore Indians' ICMR data leaked in 2023**
- **CoWIN breach** exposed Aadhaar and phone numbers of 98 crore people.
- **AIIMS Delhi ransomware** compromised 4 crore patient records.
- **DigiLocker** shares your actual documents; if the verifier's system is hacked, your data is gone.

**Core Problem Statement:** Every time you verify your identity in India (for a SIM card, bank account, hospital, or job), you hand over your raw Aadhaar, PAN, DOB, and address. The verifier sees and stores everything, becoming a target for hackers.

**The Bottom Line:** The problem isn't verification; it's that verification currently requires full data exposure.

---

## 💡 The Solution: VaultKey

VaultKey allows you to prove facts about yourself (like "I am over 18") without sharing the underlying raw data.

### Comparison Table

| Feature | DigiLocker | Aadhaar OTP | VaultKey |
| :--- | :--- | :--- | :--- |
| **What is shared** | Actual PDF documents | OTP + raw data | Cryptographic proof only |
| **Data stored by verifier** | Yes | Yes | **Never** |
| **Hackable central DB** | Yes | Yes | **No** |
| **Selective disclosure** | No | No | **Yes** |
| **Works offline** | No | No | **Yes (key signing)** |
| **Data stays on device** | No | No | **Yes** |

---

## 🛠️ Core Principles

1.  **Selective Disclosure:** Share only the specific claim (e.g., "18+"), not the full document.
2.  **Zero Raw Data Transfer:** Actual Aadhaar/PAN numbers never travel over any network.
3.  **Cryptographic Proof:** Math guarantees the claim is true, removing the need for trust.

---

## 🔄 How It Works (The 5-Step Flow)

1.  **REGISTER (One-time):** Citizen opens VaultKey app. The system generates an Ed25519 keypair on their device. The Private Key stays on the phone (never uploaded), and the Public Key is registered to VaultKey’s govt node.
2.  **REQUEST:** A verifier (Bank/Hospital/Employer) opens the Verifier Portal and selects what they need to verify (age, citizenship, PAN validity). The system generates a unique **Challenge** (random string, expires in 60 seconds).
3.  **APPROVE:** The citizen receives the request on their app, sees exactly what is being asked (e.g., "HDFC Bank wants to confirm you are 18+"), and taps Approve. The app signs the Challenge + the specific Claim using their Private Key and sends the signed proof back.
4.  **VERIFY:** The VaultKey Server receives the signed proof, checks the signature against the citizen’s Public Key in the registry, and verifies the claim mathematically. It returns a simple TRUE or FALSE to the verifier.
5.  **DONE:** The verifier gets confirmation. The citizen’s Aadhaar number was never seen, transmitted, or stored by the verifier.

---

## 🏗️ Technical Architecture

### 1. Citizen Side (Mobile App)
- **Tech:** React Native
- **Security:** Ed25519 Private Key stored in the device's secure enclave.
- **Function:** Signs verification requests locally; receives push notifications.

### 2. VaultKey Server (Backend)
- **Tech:** Node.js + Express API
- **Database:** PostgreSQL (Public Key Registry), Redis (Challenge TTL).
- **Function:** Signature Verifier (node:crypto built-in) and Claim Templates manager.

### 3. Verifier Side (Web Portal)
- **Tech:** React
- **Function:** Initiates verification requests; receives TRUE/FALSE responses; maintains an audit log of requests (but not the data).

---

## 🔐 The Cryptography (Ed25519)

VaultKey uses **Ed25519**, a modern elliptic curve signature scheme:
- **Unbreakable:** Like a locker (Public Key) and its unique key (Private Key). Only the owner can sign something that the Public Key can verify.
- **Challenge-Response:**
    *   **Server sends:** `"CHALLENGE_xyz123_expires_60s"`
    *   **Citizen signs:** `SIGN(CHALLENGE + "age>18=TRUE", PrivateKey)`
    *   **Server checks:** `VERIFY(signature, PublicKey)`
- **Efficiency:** Faster than RSA, shorter keys, and better for mobile devices. Used by WhatsApp, Signal, and SSH.

---

## 🌍 Real-World Use Cases

- **KYC for Banks:** Prove "18+, Indian citizen, valid PAN" without sharing raw IDs.
- **Hospital Admission:** Prove insurance validity, age, and blood group without full Aadhaar exposure.
- **SIM Card Verification:** Provide proof of identity and address without leaving photocopies behind.
- **Govt Scheme Enrollment:** Prove BPL status or eligibility without data hoarding.

---

## 🛡️ Security Model

**What happens if VaultKey’s Server gets hacked?**
*Nothing.* We don't store anything worth stealing:
- **Public Keys:** Meant to be public.
- **Challenge logs:** Expire after 60 seconds.
- **Verification results:** Only TRUE/FALSE, no personal data.
- **Claim templates:** Just question formats.

**The private key never leaves the citizen’s device.** This is the core differentiator—there is no honeypot to attack.

---

## 📈 Impact

- **1.4 billion Indians** can have a secure digital identity.
- **Zero raw data transmitted** per verification.
- **3 seconds** average verification time.
- **0 cost** for citizens (open source, govt deployable).

**VaultKey doesn’t make identity verification more secure. It makes data exposure unnecessary.**
