# Docker Training Guide - Microservices Deployment

## Overview
This guide will walk you through containerizing and orchestrating the microservices using Docker and Docker Compose.

---

## Step 1: Create Dockerfiles for Each Microservice

For each microservice directory, create a file named `Dockerfile` with the appropriate content.

### 1.1 hqccc_auth Service

**Location:** `hqccc_auth/Dockerfile`

**Content:**
```dockerfile
FROM tomcat:9.0

# Copy the WAR file to Tomcat's webapps directory
COPY target/hqccc_auth-0.0.1-SNAPSHOT.war /usr/local/tomcat/webapps/hqccc_auth.war

# Expose port 8080
EXPOSE 8080

# Start Tomcat
CMD ["catalina.sh", "run"]
```

### 1.2 hqccc_incident Service

**Location:** `hqccc_incident/Dockerfile`

**Content:**
```dockerfile
FROM tomcat:9.0

# Copy the WAR file to Tomcat's webapps directory
COPY target/hqccc_incident-0.0.1-SNAPSHOT.war /usr/local/tomcat/webapps/hqccc_incident.war

# Expose port 8080
EXPOSE 8080

# Start Tomcat
CMD ["catalina.sh", "run"]
```

---

## Step 2: Build Maven Packages and Docker Images

Run the following commands to build each microservice:

```powershell
# Navigate to each service directory and build

# hqccc_auth
cd hqccc_auth
mvn clean package -DskipTests
docker build -t hqccc_auth:latest .
cd ..

# hqccc_incident
cd hqccc_incident
mvn clean package -DskipTests
docker build -t hqccc_incident:latest .
cd ..
```

### Verify Docker Images

After building, verify that all images were created successfully:

```powershell
docker images | grep -E "hqccc_"
```

You should see output similar to:
```
hqccc_auth                latest    <image-id>    <created>    <size>
hqccc_incident            latest    <image-id>    <created>    <size>
```

---

## Step 3: Create Docker Compose Configuration

Create a new directory called `docker` at the root level and create a `docker-compose.yml` file inside it.

**Location:** `docker/docker-compose.yml`

**Content:**
```yaml
services:
  hqccc_auth:
    image: hqccc_auth:1.0.0
    container_name: hqccc_auth_container
    ports:
      - "8001:8081"
    environment:
      JAVA_OPTS: "-Xmx512m -Xms256m"
    # networks:
    #   - microservices_network
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/hqccc_auth/"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  hqccc_incident:
    image: hqccc_incident:1.0.0
    container_name: hqccc_incident_container
    ports:
      - "8002:8087"
    environment:
      JAVA_OPTS: "-Xmx512m -Xms256m"
    # networks:
    #   - microservices_network
    restart: unless-stopped
    depends_on:
      hqccc_auth:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/hqccc_incident/"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s







---

## Troubleshooting

### Service won't start
- Check logs: `docker-compose logs <service-name>`
- Verify the WAR file was built: Check `target/` directory

### Port conflicts
- If a port is already in use, change the port mapping in `docker-compose.yml`
- Format: `"<host-port>:8080"`

### Container exits immediately
- Check logs for error messages: `docker-compose logs <service-name>`
- Ensure the Docker image was built successfully: `docker images`

---

