# Secure PKI-Based 2FA Microservice

## 📋 Overview
This repository hosts a production-ready, containerized microservice that implements **Two-Factor Authentication (2FA)** using **Public Key Infrastructure (PKI)**. 

The system secures a Time-based One-Time Password (TOTP) seed using **RSA 4096-bit encryption** and exposes a REST API for decrypting the seed, generating 2FA codes, and verifying user attempts. It is fully containerized with **Docker** and includes an automated background cron job for audit logging.

## 🚀 Key Features
* **Enterprise Security:** Uses RSA-OAEP with SHA-256 for secure seed transmission.
* **Standard TOTP:** Generates RFC 6238 compliant 6-digit codes (SHA-1, 30s interval).
* **Dockerized:** Multi-stage Docker build for a lightweight, secure runtime environment.
* **Persistence:** Uses Docker volumes to persist sensitive data across container restarts.
* **Automation:** Integrated Linux Cron job logs 2FA codes every minute for auditing.
* **Cryptographic Proof:** Includes tools to sign git commits using RSA-PSS for identity verification.

---

## 🛠️ Tech Stack
* **Language:** Python 3.11
* **Framework:** FastAPI (with Uvicorn)
* **Containerization:** Docker & Docker Compose
* **Cryptography:** `cryptography` (RSA, OAEP, PSS)
* **Auth:** `pyotp` (TOTP generation)

---

## 📂 Project Structure
```text
PKI-Based-2FA-23A91A0545/
├── app/
│   └── main.py              # Core FastAPI application
├── cron/
│   └── 2fa-cron             # Cron job schedule
├── scripts/
│   ├── log_2fa_cron.py      # Background logging script
│   ├── generate_keys.py     # RSA Key generation utility
│   └── request_seed.py      # Instructor API interaction script
├── docker-compose.yml       # Container orchestration config
├── Dockerfile               # Multi-stage build definition
├── requirements.txt         # Python dependencies
├── student_public.pem       # Your RSA Public Key
└── student_private.pem      # Your RSA Private Key (Keep Secure!)

---

## ⚙️ Setup & Installation
1. Clone the Repository
Bash

git clone [https://github.com/ofctarun/PKI-Based-2FA-23A91A0545.git](https://github.com/ofctarun/PKI-Based-2FA-23A91A0545.git)
cd PKI-Based-2FA-23A91A0545
2. Build and Run with Docker
Make sure Docker Desktop is running, then launch the service:

Bash

docker compose up -d --build
The API will start on http://localhost:8080.

The cron job will start automatically in the background.

---

## 🔌 API Documentation
1. Initialize Seed (POST /decrypt-seed)
Uploads the encrypted seed to the server. The server decrypts it using the private key and stores it securely in the volume.

Endpoint: /decrypt-seed

Method: POST

Example Request:

Bash

curl -X POST http://localhost:8080/decrypt-seed \
  -H "Content-Type: application/json" \
  -d '{"encrypted_seed": "YOUR_BASE64_STRING_HERE"}'
2. Generate 2FA Code (GET /generate-2fa)
Returns the current valid TOTP code and the remaining validity time.

Endpoint: /generate-2fa

Method: GET

Example Request:

Bash

curl http://localhost:8080/generate-2fa
Response:

JSON

{
  "code": "123456",
  "valid_for": 28
}
3. Verify Code (POST /verify-2fa)
Verifies if a user-provided code is valid. Accepts a window of ±30 seconds (1 period).

Endpoint: /verify-2fa

Method: POST

Example Request:

Bash

curl -X POST http://localhost:8080/verify-2fa \
  -H "Content-Type: application/json" \
  -d '{"code": "123456"}'

---

## 🧪 Testing & Verification
1. Run Local Tests
You can run the included Python test scripts to verify logic without Docker:

Bash

# Verify encryption logic
python test_decryption.py

# Verify TOTP generation
python test_totp.py

# Verify API Endpoints (Container must be running)
python test_api.py
2. Check Cron Job Logs
To verify that the background automation is working, inspect the logs inside the running container:

Bash

docker compose exec app cat /cron/last_code.txt
You should see timestamps and codes logged every minute.

---

## 📜 License
This project is part of a secure coding challenge. Private keys are for demonstration purposes only.