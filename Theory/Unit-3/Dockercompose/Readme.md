## Docker Compose vs Docker Run

---

# Running Nginx with Docker Run

```bash
docker run \
  --name my-nginx \
  -p 8080:80 \
  -v ./html:/usr/share/nginx/html \
  -e NGINX_HOST=localhost \
  --restart unless-stopped \
  -d \
  nginx:alpine
```

---

#  Same Setup with Docker Compose

## Step 1: Create `docker-compose.yml`

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    container_name: my-nginx
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - NGINX_HOST=localhost
    restart: unless-stopped
```

---

## Step 2: Create Container Using Docker Compose

```bash
docker-compose up -d
```

---

## Step 3: Stop and Remove Containers

```bash
docker-compose down
```

---

## Step 4: View Logs

```bash
docker-compose logs
```

---

## Step 5: Check Running Containers

```bash
docker-compose ps
```

---

# Conclusion

Docker Compose is a YAML-based abstraction layer over multiple `docker run` commands.

* Translates directly → Every Compose option has a corresponding Docker run flag
* Simplifies complex setups → Define everything in one file
* Manages relationships → Handles dependencies automatically
* Provides consistency → Same configuration every time

### Key Difference

* `docker run` → Imperative approach ("Do these steps")
* `docker-compose` → Declarative approach ("Here's what I want")

Docker Compose is especially useful for development environments and multi-service applications.

---

# Scaling with Docker Compose

---

## Docker Compose Scaling Command

```bash
docker compose up --scale web=3 --scale worker=2 -d
```

---

## Create `docker-compose.yml`

```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080-8082:80"
    networks:
      - app-network

  worker:
    image: alpine:latest
    command: sh -c "while true; do echo 'Working...'; sleep 5; done"
    networks:
      - app-network

  redis:
    image: redis:alpine
    networks:
      - app-network

networks:
  app-network:
```

---

## Check Running Services

```bash
docker compose ps
```

---

# Scaling WordPress and MySQL with Reverse Proxy

---

## Step 1: Create `docker-compose.yml`


---

## Step 2: Create `nginx.conf` File

(Configure reverse proxy settings)

---

## Step 3: Start Containers

```bash
docker compose up -d
```

---

## Step 4: Scale WordPress

```bash
docker compose up --scale wordpress=3 -d
```

---

## Step 5: Check Logs

```bash
docker compose logs nginx
```