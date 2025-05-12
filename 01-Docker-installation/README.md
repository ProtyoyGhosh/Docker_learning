# 🐳 Docker Installation & Basic Commands

This document records the Docker basics I’ve learned so far.

---

## 🚀 1. Install Docker Engine on Linux

Use Docker's convenience script for quick, non-interactive installation:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
⚠️ Requires root/sudo privileges. Not recommended for production.

##✅ 2. Verify Installation
docker --version
docker

##🧪 3. Test with Hello World
docker run hello-world
This pulls the hello-world image from Docker Hub and runs it in a container.

##📋 4. Container and Image Commands
docker ps            # Active containers
docker ps -a         # All containers
docker images        # List downloaded images

##🌐 5. Run NGINX
docker run nginx

Above will exit if not run in background. To keep it running:
docker run -d --name=protyoy1 nginx

##🔁 6. Make NGINX Restart Automatically
docker run -d --name=protyoy2 -p 80:80 --restart=always nginx
curl localhost

##🔍 7. Explore Container Internals
docker exec -it protyoy2 bash
cd /etc/nginx
cat nginx.conf
Follow the include path to find the actual HTML file and modify it.

##🖊️ 8. Modify Web Page Content
Navigate to root (typically /usr/share/nginx/html), edit index.html, then:

exit
curl localhost   # shows updated content

##🧱 9. Commit Container as New Image
docker commit protyoy2 nginx:latest

##💾 10. Save Image as Tar File
docker save nginx:latest | gzip > nginx-custom.tar.gz

##🧩 11. Load and Run Saved Image
docker rm -f <container_id>
docker rmi <image_id>

## 12. Then load and run:
docker load -i nginx-custom.tar.gz
docker run -d -p 80:80 --name=protyoy_tar --restart=always nginx:latest
curl localhost








