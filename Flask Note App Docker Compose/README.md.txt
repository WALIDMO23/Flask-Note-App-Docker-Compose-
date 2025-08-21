# 📝 Flask Notes Webapp with Docker Compose

A simple **note-taking web application** built with **Python Flask** and **MySQL**, containerized using **Docker** and orchestrated with **Docker Compose**.
The app provides both an **HTML UI** and a **JSON API** for managing notes.

---

## 🚀 Features

- Add and list notes via web UI (most recent first).
- REST API endpoints to create and list notes.
- Delete notes from the UI.
- Health check endpoint (`/healthz`) to verify DB connectivity.
- Persistent MySQL storage using Docker volumes.
- Environment variables managed via `.env` file (no secrets in repo).
- Non-root user for Flask container.
- Healthchecks and startup dependency ordering between services.

---

## 📂 Project Structure

flask-notes-app/
│── app/
│ ├── app.py
│ ├── init.py
│ ├── templates/
│ │ └── index.html
│ └── static/
│
│── db/
│ └── init.sql
│
│── requirements.txt
│── Dockerfile
│── docker-compose.yml
│── .env.example
│── README.md



---

## ⚙️ Prerequisites

- Docker Engine 20+
- Docker Compose v2
- Python not required on host (runs inside container)
- Open port `8080` in EC2 Security Group (if deployed on AWS)

---

## 🔧 Setup & Run

### 1. Clone the repository

git clone <repo-url>
cd flask-notes-app
2. Create .env file
Copy the example and adjust values:

cp .env.example .env
Example .env:

env
MYSQL_ROOT_PASSWORD=rootpass123
MYSQL_DATABASE=notesdb
MYSQL_USER=notesuser
MYSQL_PASSWORD=pass123
MYSQL_HOST=db
MYSQL_PORT=3306

FLASK_ENV=development
3. Build & Start the containers
docker compose build
docker compose up -d
4. Access the app
Web UI:
http://localhost:8080
(or http://<EC2_PUBLIC_IP>:8080 if on AWS)

🌐 API Endpoints
Create a note
curl -X POST -H "Content-Type: application/json" \
-d '{"content":"Buy milk"}' \
http://localhost:8080/notes
Response:

json
{"message": "Note created"}
List notes
curl http://localhost:8080/notes
Response:

json
[
  {"id": 1, "content": "Buy milk", "created_at": "2025-08-21T12:00:00Z"}
]
Health check

curl http://localhost:8080/healthz
Response:

json
{"status": "ok"}
🗂 Persistent Storage
MySQL data is stored in a named volume:

flask-notes-app_mysql_data
This ensures notes persist across container restarts.
To wipe data:

Check logs:

docker compose logs db
docker compose logs web
Change port
Edit docker-compose.yml:

yaml
ports:
  - "8080:5000"
📋 Acceptance Criteria Checklist
✔ Single command starts both containers.
✔ UI reachable at host port 8080.
✔ Notes persist after container restart.
✔ JSON API available (/notes, /healthz).
✔ Secrets managed via .env (no hardcoding).
✔ Web container runs as non-root user.
✔ Healthchecks for both services.

🖼 Architecture Diagram (Optional)
css
   Browser / curl
        │
        ▼
   [ Flask Web App ]
        │
        ▼
   [   MySQL DB   ]
   (with volume)
