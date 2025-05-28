# 🧭 Docker Network Configuration
This section demonstrates how to manage Docker's default networking and assign custom IP ranges to containers, both for standalone docker run and docker-compose.

---
## 📌 Change Default Docker Bridge IP (docker0)
1. Check current IP configuration:
```bash
ifconfig
```
2. Change Docker's default bridge (docker0) IP range:

#### Create or modify /etc/docker/daemon.json:
```bash
{
  "bip": "10.10.10.1/24"
}
```
#### 🔹 bip stands for Bridge IP.

3. Restart Docker to apply the changes:
```bash
sudo systemctl restart docker
sudo systemctl status docker
```
4. Check if the new IP is applied:
```bash
ifconfig
```
5. Create a container and inspect its IP (it should be from the 10.10.10.x range):
```bash
docker run -d --name=nginx-test nginx
docker inspect nginx-test
```
---

## ⚠️ Docker Compose Won't Use bip Setting by Default
By default, Docker Compose creates its own custom network, which does not use the bip setting from daemon.json.

Example (won't use custom bip range): run this docker compose but you will see it's not using 10.10 series ip
```bash
services:
  my-custome-nginx:
    image: nginx
```
#### run the docker compose
```bash
docker compose up -d
docker inspect <container_name>
```
## Solution: ✅ Assign Custom Subnet via Compose
Update your docker-compose.yml to explicitly define the network:
```bash
version: "3.9"

networks:
  my-super-custome-net:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.0.0/24
          gateway: 192.168.0.1

services:
  my-custome-nginx:
    image: nginx
    networks:
      - my-super-custome-net
```
Then run:
```bash
docker compose up -d
docker inspect <container_name>
```
## 🎯 Assign Static (fixed) IP to a Container
To assign a specific IP address within the subnet:
```bash
version: "3.9"

networks:
  my-super-custome-net:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.0.0/24
          gateway: 192.168.0.1

services:
  my-custome-nginx:
    image: nginx
    networks:
      my-super-custome-net:
        ipv4_address: 192.168.0.200
```
#### 📍 Now the container my-custome-nginx will always receive the IP 192.168.0.200.


