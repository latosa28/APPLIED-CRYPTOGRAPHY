# Secure Bulletin Board System (BBS)

**Course:** Foundations of Cybersecurity / Applied Cryptography (A.A. 2023-24)  
**Project:** Secure Bulletin Board System with Authentication and Confidential Communication

---

## 1. Project Overview

The Bulletin Board System (BBS) is a distributed service that allows registered users to read messages and post their own. Each user is identified by a unique nickname set during registration, paired with a password.

A message consists of the following fields:  
- **Identifier**: uniquely identifies the message within the BBS  
- **Title**  
- **Author**: the nickname of the user who posted the message  
- **Body**

Users can perform the following operations **after successful login** over a secure channel:  
- `List(int n)`: lists the latest *n* messages  
- `Get(int mid)`: downloads the message with the specified identifier  
- `Add(String title, String author, String body)`: adds a new message  

Users must be logged in to perform operations. Logging out terminates the secure session.

The BBS is centralized, running on a server listening at a known (IP, port). The server holds an RSA key pair; the public key (`pubKbbs`) is distributed to clients beforehand and used to establish secure communication.

---

## 2. Registration Phase

- The client securely connects to the server and submits an email address, nickname, and password.
- The server sends a challenge (OTP) to the specified email (simulated by creating a folder and file containing the OTP).
- The client must return the correct challenge to complete registration.
- If the challenge is incorrect, registration is aborted.

---

## 3. Login Phase

- Registered users securely connect and authenticate using their nickname and password.
- Upon successful verification, a secure session is established.
- The session remains active until the user logs out.

---

## 4. Security Requirements

- **Passwords** are never stored or transmitted in cleartext.
- All communications guarantee:  
  - **Confidentiality**  
  - **Integrity**  
  - **Replay protection**  
  - **Non-malleability**
- The system provides **Perfect Forward Secrecy (PFS)**.
- Code vulnerabilities are minimized.
- Implementation in **C or C++** using **OpenSSL**, without using OpenSSL TLS APIs.

---

## 5. Implementation Choices

### 5.1 Usernames and Passwords

- Usernames:  
  - Lowercase letters only  
  - No digits or special characters  
  - Maximum length: 20 characters  
  - Must be unique

- Passwords:  
  - At least 8 characters  
  - Include at least one uppercase letter, one lowercase letter, one digit, and one special character

### 5.2 Keys and Symmetric Encryption

- Clients possess the server’s **RSA public key** beforehand, stored in `PublicKey/Server.pem` with an authenticating certificate.
- For each connection (registration or login), the client generates a fresh **ephemeral symmetric key** (AES-GCM), which is never stored.
- The symmetric key is securely transported to the server encrypted with the server’s RSA public key.

### 5.3 Encryption Protocol

- Symmetric encryption/decryption uses **AES-GCM** with:  
  - 12-byte IV (sent in clear)  
  - 16-byte authentication tag (sent in clear)  
- AES-GCM provides:  
  - Confidentiality (only parties with the secret key can decrypt)  
  - Integrity and authenticity (via the authentication tag)  
  - Non-malleability (unauthorized modifications cause decryption failure)

- Digital signatures were considered but omitted since the server is trusted; security focuses on protection against third parties.

### 5.4 Replay Protection

- Each encrypted message includes a unique **nonce**.
- The receiver maintains a list of used nonces to detect replay attempts.
- If a nonce is reused, the message is rejected as a replay attack.
- The nonce is echoed back by the receiver to ensure session validity and message sequence integrity.

### 5.5 Registration Challenge-Response

- The client sends email, username, and password to the server.
- The server sends a 6-digit OTP to the user’s email (simulated by file creation).
- The client must provide the correct OTP to complete registration.
- On success, credentials are saved in `Database.txt` in the format: email:username:salt$hash(salt || password)

## 6. Further Documentation

For detailed information on design, protocol flows, cryptographic choices, and data structures, refer to the full technical specification document:  
`Report progetto crittografia_LATOSA_SCARABAGGIO.pdf`

## 7. Authors

- [Giuseppe Pio La Tosa] 
- [Giuseppe Scarabaggio]
