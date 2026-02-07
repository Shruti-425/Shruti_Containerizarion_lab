# Experiment 3: Docker – NGINX & Web Application Containerization

This repository contains **Experiment‑3**, demonstrating Docker fundamentals through NGINX deployment, custom image creation, image comparison, volume usage, and preparation of a sample Flask web application for containerization.

---

##  Objective

To understand and implement Docker containerization concepts by:

* Running official Docker images
* Building custom Docker images using different base OS images
* Comparing image sizes and layers
* Serving custom content using Docker volumes
* Preparing a simple web application for containerization

---

##  Experiment Structure

```
EXPERIMENT-3/
│
├── Part 1/                # Official NGINX image
├── Part 2/                # Custom NGINX (Ubuntu base)
├── Part 3/                # Custom NGINX (Alpine base)
├── Part 4/                # Image comparison
├── Part 5/                # Custom HTML using volumes
├── Experiment 3 Part 2/
│   └── flask-app/         # Sample Flask web application
│       ├── app.py
│       └── requirements.txt
└── README.md
```

---

##  Part 1: Running Official NGINX Image

### Pull Image

```bash
docker pull nginx:latest
```

### Run Container

```bash
docker run -d -p 8080:80 --name exp3-nginx nginx
```

### Verify

```bash
curl http://localhost:8080
```

✔ Default **NGINX welcome page** is displayed.

---

##  Part 2: Custom NGINX Using Ubuntu Base Image

### Dockerfile

```dockerfile
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y nginx && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### Build Image

```bash
docker build -t nginx-ubuntu .
```

### Run Container

```bash
docker run -d -p 8081:80 --name nginx-ubuntu nginx-ubuntu
```

### Observation

```bash
docker images nginx-ubuntu
```

 Custom Ubuntu-based NGINX image successfully created.

---

##  Part 3: Custom NGINX Using Alpine Base Image

### Dockerfile

```dockerfile
FROM alpine:latest

RUN apk add --no-cache nginx

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### Build Image

```bash
docker build -t nginx-alpine .
```

### Run Container

```bash
docker run -d -p 8082:80 --name nginx-alpine nginx-alpine
```

### Observation

```bash
docker images nginx-alpine
```

 Alpine-based image created (~16MB), demonstrating lightweight containers.

---

##  Part 4: Image Size & Layer Comparison

### Compare Images

```bash
docker images | findstr nginx
```

### Inspect Layers

```bash
docker history nginx
docker history nginx-ubuntu
docker history nginx-alpine
```

### Observations

* Ubuntu image has multiple filesystem layers
* Alpine image has minimal layers and smallest size
* Official NGINX image is optimized but larger than Alpine

---

##  Part 5: Serving Custom HTML Using Docker Volumes

### Create HTML File

```bash
mkdir html
echo "<h1>Hello from Docker NGINX</h1>" > html/index.html
```

![HTML file](Part 5/P5 Image 1.png)

### Run Container with Volume Mapping (Windows CMD)

```bash
docker run -d -p 8083:80 -v "%cd%\html:/usr/share/nginx/html" nginx
```

![Volume mount output](Part 5/P5 Image 2.png)

 Custom HTML page served successfully via NGINX.

---

## 🔹 Sample Web Application (Flask – Pre‑Containerization)

### app.py

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, Docker!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### requirements.txt

```
flask
```

### Run Locally

```bash
python app.py
```

Access:

```
http://localhost:5000
```

 Application tested locally before containerization.

---


##  Tech Used

* Docker
* NGINX
* Ubuntu & Alpine Linux
* Python Flask

---