# Experiment 6A: Comparison of Docker Run and Docker Compose

---

##  Objective

To understand the relationship between `docker run` and Docker Compose, and compare their syntax, features, and use cases.

---

##  PART A - THEORY

### 1. Docker Run (Imperative Approach)

The `docker run` command is used to create and start a container from an image. It requires explicit flags:

- Port mapping (`-p`)
- Volume mounting (`-v`)
- Environment variables (`-e`)
- Network (`--network`)
- Restart policies (`--restart`)
- Resource limits (`--memory`, `--cpus`)
- Container name (`--name`)

 This is an **imperative approach** (step-by-step execution).

#### Example:
```bash
docker run -d \
  --name my-nginx \
  -p 8080:80 \
  -v ./html:/usr/share/nginx/html \
  -e NGINX_HOST=localhost \
  --restart unless-stopped \
  nginx:alpine
````

---

### 2. Docker Compose (Declarative Approach)

Docker Compose uses a YAML file (`docker-compose.yml`) to define services.

 This is a **declarative approach** (desired state).

#### Command:

```bash
docker compose up -d
```

#### Equivalent Compose File:

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
      NGINX_HOST: localhost
    restart: unless-stopped
```

---

### 3. Mapping: Docker Run vs Compose

| Docker Run  | Docker Compose                   |
| ----------- | -------------------------------- |
| `-p`        | `ports`                          |
| `-v`        | `volumes`                        |
| `-e`        | `environment`                    |
| `--name`    | `container_name`                 |
| `--network` | `networks`                       |
| `--restart` | `restart`                        |
| `--memory`  | `deploy.resources.limits.memory` |
| `--cpus`    | `deploy.resources.limits.cpus`   |

---

### 4. Advantages of Docker Compose

* Multi-container support
* Easy reproducibility
* Version control friendly
* Simplified lifecycle management
* Supports scaling

```bash
docker compose up --scale web=3
```

---

##  PART B - PRACTICAL TASKS

###  Task 1: Single Container

#### Using Docker Run

```bash
docker run -d \
 --name lab-nginx \
 -p 8081:80 \
 -v $(pwd)/html:/usr/share/nginx/html \
 nginx:alpine
```

Access:

```
http://localhost:8081
```

Stop:

```bash
docker stop lab-nginx
docker rm lab-nginx
```

---

#### Using Docker Compose

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    container_name: lab-nginx
    ports:
      - "8082:80"
    volumes:
      - ./html:/usr/share/nginx/html
```

Run:

```bash
docker compose up -d
```

Stop:

```bash
docker compose down
```

---

###  Task 2: Multi-Container (WordPress + MySQL)

#### Using Docker Run

```bash
docker network create wp-net

docker run -d \
 --name mysql \
 --network wp-net \
 -e MYSQL_ROOT_PASSWORD=secret \
 -e MYSQL_DATABASE=wordpress \
 mysql:5.7

docker run -d \
 --name wordpress \
 --network wp-net \
 -p 8082:80 \
 -e WORDPRESS_DB_HOST=mysql \
 -e WORDPRESS_DB_PASSWORD=secret \
 wordpress:latest
```

Access:

```
http://localhost:8082
```

---

#### Using Docker Compose

```yaml
version: '3.8'

services:
  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: wordpress
    volumes:
      - mysql_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    ports:
      - "8083:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_PASSWORD: secret
    depends_on:
      - mysql

volumes:
  mysql_data:
```

Run:

```bash
docker compose up -d
```

Stop:

```bash
docker compose down
```

---

##  PART C – CONVERSION TASKS

### Task 3: Convert Docker Run to Compose

```bash
docker run -d \
 --name webapp \
 -p 5000:5000 \
 -e APP_ENV=production \
 -e DEBUG=false \
 --restart unless-stopped \
 node:18-alpine
```

Equivalent:

```yaml
version: '3.8'

services:
  webapp:
    image: node:18-alpine
    container_name: webapp
    ports:
      - "5000:5000"
    environment:
      APP_ENV: production
      DEBUG: "false"
    restart: unless-stopped
```

---

### Task 4: Resource Limits

```bash
docker run -d \
 --name limited-app \
 -p 9000:9000 \
 --memory="256m" \
 --cpus="0.5" \
 --restart always \
 nginx:alpine
```

Equivalent:

```yaml
version: '3.8'

services:
  limited-app:
    image: nginx:alpine
    container_name: limited-app
    ports:
      - "9000:9000"
    restart: always
    deploy:
      resources:
        limits:
          memory: 256M
          cpus: "0.5"
```

---

##  PART D – DOCKERFILE

### Node App Example

#### app.js

```js
const http = require('http');

http.createServer((req, res) => {
  res.write("Hello from Node Docker App");
  res.end();
}).listen(3000);
```

#### Dockerfile

```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY app.js .

EXPOSE 3000
CMD ["node", "app.js"]
```

#### docker-compose.yml

```yaml
version: '3.8'

services:
  nodeapp:
    build: .
    container_name: custom-node-app
    ports:
      - "3000:3000"
```

Run:

```bash
docker compose up --build -d
```

Access:

```
http://localhost:3000
```

---

##  Observations

* `docker run` is simple for single containers
* Docker Compose is better for multi-container setups
* Compose improves readability and maintainability

---

##  Result

Successfully compared Docker Run and Docker Compose and implemented real-world containerized applications.

---