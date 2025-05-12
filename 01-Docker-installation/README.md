# Docker-Basic
=======

# 🚀 Docker Learning Notes

This repository contains my learning progress with Docker. It includes installation steps, useful commands, container customization, and image sharing.

---

## 📦 Docker Installation

Install Docker Engine on Linux using the convenience script:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Verify installation:
```bash
docker --version
```

---

## 🐳 Docker Basics

### 1. Run Hello World
```bash
docker run hello-world
```

### 2. List Containers
```bash
docker ps        # Running containers
docker ps -a     # All containers
```

### 3. List Images
```bash
docker images
```

---

## 🌐 Working with NGINX

### Run NGINX in the background
```bash
docker run -d --name=protyoy1 nginx
```

### Run NGINX with port and restart policy
```bash
docker run -d --name=protyoy2 -p 80:80 --restart=always nginx
curl localhost
```

---

## 🛠️ Modify Container Content

```bash
docker exec -it protyoy2 bash
cd /etc/nginx
cat nginx.conf
# Follow includes to the HTML root folder and modify index.html
```

Exit the container and verify with:
```bash
curl localhost
```

---

## 📸 Commit and Save Image

```bash
docker commit protyoy2 nginx:latest
docker save nginx:latest | gzip > nginx-custom.tar.gz
```

---

## 📥 Load and Run the Image

```bash
docker load -i nginx-custom.tar.gz
docker run -d -p 80:80 --name=protyoy_tar --restart=always nginx:latest
curl localhost
```

---

## 📚 More to Learn

- Volumes
- Networks
- Dockerfile
- Docker Compose

---

## 🧑‍💻 Author

- [Protyoy](https://github.com/ProtyoyGhosh)
