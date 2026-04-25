#  Experiment 10: SonarQube — Static Code Analysis

##  Overview

This project demonstrates how to use **SonarQube** for static code analysis using Docker. It includes:

* SonarQube Server (analysis + dashboard)
* PostgreSQL Database (data storage)
* Sample Java application with intentional issues

---

##  Problem Statement

Code bugs and security issues are often discovered too late. Manual code reviews:

* Are slow
* Do not scale
* Are inconsistent

 **Solution:** Use SonarQube to automatically detect:

* Bugs
* Vulnerabilities
* Code Smells
* Technical Debt

---

##  Architecture

```
Your Code → Sonar Scanner → SonarQube Server → PostgreSQL DB → Dashboard
```

### Components:

* **SonarQube Server** → Stores results, applies rules, shows dashboard
* **Sonar Scanner** → Analyzes code and sends report
* **PostgreSQL** → Stores analysis data

---

##  Prerequisites

* Docker
* Docker Compose
* Java (JDK 11+)
* Maven

---

##  Step 1: Setup SonarQube using Docker

Create `docker-compose.yml`:

```yaml
=======
# Experiment 10: SonarQube - Static Code Analysis

##  Objective

To set up SonarQube using Docker Compose, perform static code analysis on a Java project, and view code quality results.

---

##  Step 1: Start the SonarQube Server

We will use Docker Compose to start both the SonarQube server and its PostgreSQL database together.

### Create a file called `docker-compose.yml`

```yaml
version: '3.8'
>>>>>>> 935e3a6 (Added)
services:
  sonar-db:
    image: postgres:13
    container_name: sonar-db
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonarqube
      POSTGRES_HOST_AUTH_METHOD: trust
    volumes:
      - sonar-db-data:/var/lib/postgresql/data
    networks:
      - sonarqube-lab

  sonarqube:
    image: sonarqube:lts-community
    container_name: sonarqube
    ports:
      - "9000:9000"
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://sonar-db:5432/sonarqube
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonar-data:/opt/sonarqube/data
      - sonar-extensions:/opt/sonarqube/extensions
=======
>>>>>>> 935e3a6 (Added)
    depends_on:
      - sonar-db
    networks:
      - sonarqube-lab

volumes:
  sonar-db-data:
<<<<<<< HEAD
  sonar-data:
  sonar-extensions:
=======
>>>>>>> 935e3a6 (Added)

networks:
  sonarqube-lab:
```

###  Run Containers

```bash
docker-compose up -d
docker-compose logs -f sonarqube
```

 Open in browser:
http://localhost:9000


=======
### Start both containers

```bash
docker-compose up -d
```

### Check logs

```bash
docker-compose logs -f sonarqube
```

Wait until SonarQube starts successfully.

### Open in browser

```
http://localhost:9000
```

---

##  Step 2: Generate Token

1. Go to **My Account → Security**
2. Generate token (e.g., `scanner-token`)
3. Copy it (shown only once)

---

##  Step 3: Sample Java Project

### Project Structure

```
sample-java-app/
 └── src/main/java/com/example/Calculator.java
 └── pom.xml
```

### Features with Issues:

* Division by zero bug
* SQL Injection vulnerability
* Null pointer risk
* Duplicate code
* Unused variables

---

##  Step 4: Maven Configuration

Add in `pom.xml`:

```xml
<properties>
    <sonar.projectKey>sample-java-app</sonar.projectKey>
    <sonar.host.url>http://localhost:9000</sonar.host.url>
    <sonar.login>YOUR_TOKEN</sonar.login>
</properties>
=======
Inside SonarQube dashboard:

* Click **Profile (top-right)**
* Go to **My Account → Security**
* Generate a token
* Save it for later use

---

##  Step 3: Create Sample Java Project

```bash
mkdir -p sample-java-app/src/main/java/com/example
cd sample-java-app
```

### Create `Calculator.java`

```java
package com.example;

public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}
```

### Create `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>sample-java-app</artifactId>
    <version>1.0-SNAPSHOT</version>

    <build>
        <plugins>
            <plugin>
                <groupId>org.sonarsource.scanner.maven</groupId>
                <artifactId>sonar-maven-plugin</artifactId>
                <version>3.9.1.2184</version>
            </plugin>
        </plugins>
    </build>

</project>
```

---

##  Step 5: Run SonarQube Analysis

### Using Maven
=======
##  Step 4: Run Scanner

Inside the project folder:

```bash
mvn sonar:sonar -Dsonar.login=YOUR_TOKEN
```

---

##  Step 6: View Dashboard

Open:
=======
Replace `YOUR_TOKEN` with the generated token.

---

##  Step 5: View Results

Open in browser:

```
http://localhost:9000
```

---

##  API Example

```bash
curl -u admin:YOUR_TOKEN \
"http://localhost:9000/api/issues/search?projectKeys=sample-java-app&types=BUG"
=======
You will see:

* Code quality metrics
* Bugs and vulnerabilities
* Code smells
* Analysis reports

---

##  Step 6: Stop the Server

```bash
docker-compose down
>>>>>>> 935e3a6 (Added)
```

---

##  CI/CD Integration (Jenkins)

Pipeline Flow:

```
Checkout → Analyze → Quality Gate → Build → Deploy
```

Example snippet:

```groovy
stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('SonarQube') {
            sh 'mvn clean verify sonar:sonar'
        }
    }
}
```

---

##  Key Concepts

| Term           | Meaning              |
| -------------- | -------------------- |
| Bug            | Code that may fail   |
| Vulnerability  | Security risk        |
| Code Smell     | Poor design          |
| Technical Debt | Effort to fix issues |
| Quality Gate   | Pass/Fail criteria   |

---

##  Conclusion

* SonarQube helps maintain high code quality
* Scanner + Server both are required
* Enables automated code review in CI/CD

---