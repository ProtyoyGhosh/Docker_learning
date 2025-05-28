# 🔗 Selective Docker Container Networking (Backend-to-Backend + Backend-to-DB)
This example demonstrates how to create isolated Docker networks and selectively connect containers to control access—such as allowing two backend containers to talk to each other, and only one of them to access the database.
## 🧠 Goal (Network Design)
#### backend1 ↔ backend2 (mutual access)

#### backend2 ↔ database (mutual access)

#### backend1 🚫 database (no access)

## ✅ Solution: Use Two Custom Docker Networks
#### 🎯 Networks:
net-b1b2: For backend1 & backend2

net-b2db: For backend2 & database

This way:

1. backend1 & backend2 are on the same network, so they can talk.

2. backend2 & database are on another shared network.

3. backend1 can’t talk to the database, because they don’t share any network.

## 🧹 1. Clean Up Existing Containers
Remove all previously running/stopped containers:
```bash
docker rm -f $(docker ps -aq)
```
## 🌐 2. Create Custom Docker Networks
Create two separate networks:

net-b1b2: for communication between backend1 and backend2

net-b2db: for communication between backend2 and db
```bash
docker network create net-b1b2
docker network create net-b2db
```

## 🐳 3. Create and Connect Containers
backend1 → net-b1b2

backend2 → net-b1b2 + net-b2db

db → net-b2db
```bash
# Start backend1
docker run -d --name=backend1 --network=net-b1b2 nginx

# Start backend2
docker run -d --name=backend2 --network=net-b1b2 nginx

# Start db container (MySQL as example)
docker run -d --name=db --network=net-b2db -e MYSQL_ROOT_PASSWORD=123456 mysql
```

### Now connect backend2 to the second network:
```bash
docker network connect net-b2db backend2
```
#### ✅ backend2 is now connected to both backend and database networks.

## 🔍 4. Verify Network Attachments
Inspect each container’s networks:
```bash
docker inspect backend1
docker inspect backend2
docker inspect db
```

#### -> backend1 → Only net-b1b2
#### -> db → Only net-b2db
#### -> backend2 → Both net-b1b2 and net-b2db
Check under the "Networks" section of each output.

## 🔄 5. Test Connectivity Between Containers
To test container-to-container access:

### A. Install ping inside containers (if missing)
```bash
apt update && apt install iputils-ping -y
```
### B. From backend1
```bash
docker exec -it backend1 bash
ping backend2         # ✅ should work
ping db               # ❌ should fail (no route)
```
### C. From backend2
```bash
docker exec -it backend2 bash
ping backend1         # ✅ should work
ping db               # ✅ should work
```
### 
```bash
docker exec -it db bash
ping backend2         # ✅ should work
ping backend1         # ❌ should fail
```

## ✅ Final Behavior Summary
```text
| Connection From → To | backend1 | backend2 | db |
| -------------------- | -------- | -------- | -- |
| **backend1**         | ✅        | ✅        | ❌  |
| **backend2**         | ✅        | ✅        | ✅  |
| **db**               | ❌        | ✅        | ✅  |
```