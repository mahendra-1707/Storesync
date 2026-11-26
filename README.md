# StoreSync — Real-Time Distributed Retail Inventory & Omnichannel Storefront

**StoreSync** is a full-stack, event-driven retail platform designed to eliminate overselling and stale catalog search across multi-city stores (`Bengaluru`, `Mumbai`, `Delhi`, `Hyderabad`, `Chennai`). Built on **Aiven PostgreSQL**, **Aiven Apache Kafka**, and **Aiven OpenSearch**, it pairs atomic row-level concurrency locks with real-time search indexing, Razorpay/UPI checkout, Gmail SMTP OTP authentication, and a **Groq-powered AI Shopping Assistant**.

---

## Live Deployment

* **Live Storefront (Vercel):** `https://storesync-719q.vercel.app/`
* **Live API Backend (Render):** `https://store-sync-ywr6.onrender.com`
* **GitHub Repository:** `https://github.com/mahendra-1707/Storesync`

---

## Core Architecture & Event Pipeline

```text
[React + Vite Storefront]
         │
         ▼
[Python Flask REST API] ──► [Groq LLM API (llama-3.3-70b-versatile)]
         │              ──► [Gmail SMTP (6-Digit OTP Auth)]
         ▼
[Aiven PostgreSQL 18]  (ACID Transactions + SELECT ... FOR UPDATE Row Locks)
         │
         ▼ (Publishes ORDER_PLACED / ORDER_CANCELLED / STOCK_UPDATED)
[Aiven Apache Kafka]   (Topic: order-events, SSL/TLS mTLS Auth)
         │
         ▼
[Kafka Background Consumer]
         │
         ▼
[Aiven OpenSearch]     (Sub-second Fuzzy Search & Live Stock Sync)
Key Features
Zero-Oversell Concurrency Control: Uses PostgreSQL row-level locking (SELECT ... FOR UPDATE) during checkout so simultaneous orders on the last remaining unit never double-sell inventory.

Event-Driven Catalog Sync (Kafka → OpenSearch): Order placements and cancellations publish events to Aiven Kafka (order-events), updating OpenSearch stock counts in real time.

Typo-Tolerant Fuzzy Search: Powered by Aiven OpenSearch (AUTO fuzziness), allowing shoppers to find products even with spelling mistakes (e.g., searching "bluetoth" matches Bluetooth devices) alongside multi-city and category filters.

Cross-City Express Fulfillment: Automatically detects local stockouts (stock = 0) and surfaces instant inter-city transfer options from neighboring Indian hubs.

Groq AI Shopping Assistant: Grounded directly in live PostgreSQL/OpenSearch catalog and order data using llama-3.3-70b-versatile to recommend in-stock products in ₹ with one-click Add to Bag.

Authentication & Real Gmail SMTP OTP: Supports local email/password signup, Google sign-in, and a 3-step Forgot Password flow delivering 6-digit OTP verification codes via Gmail SMTP.

Razorpay / UPI Checkout & Order Lifecycle: Supports Razorpay payment verification, live order tracking (Confirmed → Packed → Out for Delivery → Delivered), and instant order cancellation that automatically restores inventory across PostgreSQL and OpenSearch.

Tech Stack
Frontend: React, Vite, Tailwind CSS, Lucide Icons (Hosted on Vercel)

Backend: Python 3, Flask, Gunicorn, psycopg2, kafka-python, opensearch-py, smtplib

Managed Cloud Infrastructure (Aiven): PostgreSQL 18, Apache Kafka, OpenSearch

AI & Payments: Groq Cloud API (llama-3.3-70b-versatile), Razorpay Checkout / UPI

Local Setup & Running
1. Clone the Repository
Bash
git clone [https://github.com/likhithbn7-code/Storesync.git](https://github.com/likhithbn7-code/Storesync.git)
cd Storesync/storesync
2. Configure Backend Environment (storesync/backend/.env)
Create a .env file inside storesync/backend/ (see .env.example):

Code snippet
POSTGRES_URI=postgres://<user>:<password>@<host>:<port>/defaultdb?sslmode=require
OPENSEARCH_URI=https://<user>:<password>@<host>:<port>
KAFKA_BOOTSTRAP_SERVERS=<host>:<port>
KAFKA_TOPIC=order-events

GROQ_API_KEY=gsk_...

SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_EMAIL=your-email@gmail.com
SMTP_PASSWORD=your-16-char-app-password
(Place your Aiven Kafka SSL files ca.pem, service.cert, and service.key inside storesync/backend/certs/.)

3. Start the Backend & Kafka Consumer
Bash
cd backend
python -m venv venv
# Windows PowerShell:
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt

python seed.py
python app.py
In a second terminal (with venv activated):

Bash
python consumer.py
4. Start the Frontend
Bash
cd ..
npm install
npm run dev
Open http://localhost:5173/ in your browser.


---

### Quick PowerShell Command to Write & Push `README.md` Directly
If you want to save this straight to `README.md` from PowerShell (replace the two URLs first):

```powershell
Set-Content -Path "README.md" -Value @"
# StoreSync — Real-Time Distributed Retail Inventory & Omnichannel Storefront

**StoreSync** is a full-stack, event-driven retail platform designed to eliminate overselling and stale catalog search across multi-city stores (\`Bengaluru\`, \`Mumbai\`, \`Delhi\`, \`Hyderabad\`, \`Chennai\`). Built on **Aiven PostgreSQL**, **Aiven Apache Kafka**, and **Aiven OpenSearch**, it pairs atomic row-level concurrency locks with real-time search indexing, Razorpay/UPI checkout, Gmail SMTP OTP authentication, and a **Groq-powered AI Shopping Assistant**.

## Live Deployment
* **Live Storefront (Vercel):** https://storesync-719q.vercel.app/
* **Live API Backend (Render):** https://store-sync-ywr6.onrender.com
* **GitHub Repository:** https://github.com/mahendra-1707/Storesync
"@
git add README.md
git commit -m "Update README with live deployment links and architecture documentation"
git push origin main
