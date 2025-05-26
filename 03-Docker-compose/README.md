# Docker Compose From Scratch: MySQL + Nginx Setup

This guide walks through how to use Docker Compose to set up a basic development environment using two containers: MySQL (for database) and Nginx (for serving web content). You'll learn how Docker Compose simplifies multi-container management.

---

## 📌 What is Docker Compose?
Docker Compose is a tool for defining and running multi-container Docker applications. You define your services in a YAML file (`docker-compose.yml`), then use a single command to spin up or shut down everything.

---

## 🧱 Directory Structure
project-root/
├── docker-compose.yml
├── nginx-html/
│ └── index.html

---

```bash

`nginx-html/index.html` contains the content Nginx will serve.

---

## 📄 docker-compose.yml (Explained)

```yaml
version: '3.9'

services:
  db:
    image: mysql:latest
    container_name: mysql-db
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    volumes:
      - mysql-data:/var/lib/mysql

  web:
    image: nginx:latest
    container_name: nginx-web
    ports:
      - "8081:80"
    volumes:
      - ./nginx-html:/usr/share/nginx/html:ro
    depends_on:
      - db

volumes:
  mysql-data:

```
### 🔍 Breakdown of Sections:
version: '3.9'
Specifies the Compose file format version. 3.9 is compatible with modern Docker versions.

#### services:
Defines the services (containers) to be started.

 #### Service: db
 image: mysql:latest – Pulls MySQL from Docker Hub.

container_name: mysql-db – Names the container for easier reference.

environment: – Passes the root password using MYSQL_ROOT_PASSWORD.

volumes: – Maps a Docker named volume (mysql-data) to the internal MySQL data directory. This ensures data persists across container restarts or deletions.

#### Service: web
image: nginx:latest – Pulls Nginx from Docker Hub.

container_name: nginx-web – Gives the container a friendly name.

ports: – Maps port 8081 on the host to port 80 inside the container, so you can access Nginx via http://localhost:8081.

volumes: – Mounts the local directory ./nginx-html into the Nginx content directory. The :ro means read-only.

depends_on: – Ensures that the db container starts before the web container.

---

### 🏃 How to Run This Setup
#### 1. Create your project structure
```bash
mkdir docker-compose-demo
cd docker-compose-demo
mkdir nginx-html
```
#### 2. Add an HTML file
```bash
echo "<h1>Hello from Nginx via Docker Compose!</h1>" > nginx-html/index.html
```
#### 3. Create docker-compose.yml
Copy the YAML file above and save it as docker-compose.yml.

#### 4. Start the containers
```bash
docker compose up -d
```
#### 5. Verify
Visit http://localhost:8081 to see your Nginx page.

Log into MySQL:
```bash
docker exec -it mysql-db mysql -u root -p
# Enter password: 123456
```




