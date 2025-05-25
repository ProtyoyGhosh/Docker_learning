
# 🐳 Docker Learning

This repository documents my hands-on learning and experiments with Docker. It includes how to:

- Run multiple containers on different ports
- Use bind mounts for persistent storage
- Use Docker volumes for managed persistent storage
- Run MySQL containers with both volume types

---

## 🔹 Part 1: Docker Network Port Mapping

Run multiple containers (e.g., Nginx) on a single host by mapping different ports.

```bash
docker run -d --name=container1 -p 8081:80 nginx
docker run -d --name=container2 -p 8083:80 nginx

curl http://localhost:8081
curl http://localhost:8083
```

---

## 🔹 Part 2: Persistent MySQL Storage

### 🔸 Option 1: Bind Mount

1. Create a local directory:
   ```bash
   mkdir mysql_data
   chmod 777 -Rf mysql_data
   ```

2. Run MySQL with bind mount:
   ```bash
   docker run --name some-mysql \
     -v /absolute/path/to/mysql_data:/var/lib/mysql \
     -e MYSQL_ROOT_PASSWORD=my-secret-pw \
     -d mysql
   ```

3. Create database inside container and test persistence after container removal.

---

### 🔸 Option 2: Docker Volume

1. Create and list volumes:
   ```bash
   docker volume create mysql-vol
   docker volume ls
   ```

2. Run MySQL with Docker volume:
   ```bash
   docker run --name container-2 \
     -v mysql-vol:/var/lib/mysql \
     -e MYSQL_ROOT_PASSWORD=my-secret-pw \
     -d mysql
   ```

3. Create database and verify persistence after removing and restarting container.

---

## ⚖️ Bind Mount vs Docker Volume

| Feature    | Bind Mount             | Docker Volume           |
|------------|------------------------|--------------------------|
| Managed By | User (host path)       | Docker                   |
| Use Case   | Dev/debug              | Production/persistence   |
| Portability| Low (path-specific)    | High (Docker-managed)    |

---

## 📁 Scripts Included

- `start_mysql.sh` – MySQL with bind mount
- `mqsql-docker-vol.sh` – MySQL with Docker volume

---

## 🧠 Learning Summary

This project covers key Docker features for application networking and storage. I learned:

- How to run containers on custom ports
- How to make data persistent using bind mounts and volumes
- Difference between Docker volumes and host mounts


# 🔄 Docker Container-to-Container Communication

This guide demonstrates how to enable communication between two Docker containers using a **user-defined bridge network**.

---

## 🌐 Objective

Allow container1 and container2 to access each other using container names as hostnames.

---

## 📋 Step-by-Step Instructions

### 1️⃣ Check Docker Networks

```bash
docker network ls
```
> This lists all existing Docker networks.

---

### 2️⃣ Create a Custom Network

```bash
docker network create mynetwork
```
> Creates a user-defined bridge network which allows name-based container discovery.

---

### 3️⃣ Run Containers on the Same Network

```bash
docker run -d --name=container1 --network=mynetwork nginx
docker run -d --name=container2 --network=mynetwork nginx
```
> Launch two Nginx containers, attached to the `mynetwork`.

---

### 4️⃣ Modify `index.html` to Identify Containers

#### 🔹 Container 1

```bash
docker exec -it container1 bash
cd /usr/share/nginx/html
echo "hello from container1" > index.html
cat index.html
```

#### 🔹 Container 2

```bash
docker exec -it container2 bash
cd /usr/share/nginx/html
echo "hello from container2" > index.html
cat index.html
```

---

### 5️⃣ Test Communication Between Containers

#### From container1:

```bash
docker exec -it container1 bash
curl localhost             # Should show: hello from container1
curl container2            # Should show: hello from container2
```

#### From container2:

```bash
docker exec -it container2 bash
curl localhost             # Should show: hello from container2
curl container1            # Should show: hello from container1
```

> ✅ This proves container-to-container communication using Docker's internal DNS.

---

## 📌 Summary

- Containers must be on the same **user-defined bridge network**.
- Docker provides **automatic DNS resolution** using container names.
- Great for microservices communication in isolated environments.


