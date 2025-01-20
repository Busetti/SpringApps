
# Kong Gateway Setup with Docker and cURL

## 1. Set Up Docker Compose for Kong Gateway

Create a `docker-compose.yml` file in your project directory with the following configuration:

```yaml
version: '3'
services:
  kong-database:
    image: kong:latest
    environment:
      - KONG_DATABASE=postgres
      - KONG_PG_HOST=kong-database
      - KONG_PG_PORT=5432
      - KONG_PG_USER=kong
      - KONG_PG_PASSWORD=kong
      - KONG_PG_DATABASE=kong
    ports:
      - "5432:5432"

  kong:
    image: kong:latest
    environment:
      - KONG_DATABASE=postgres
      - KONG_PG_HOST=kong-database
      - KONG_PG_PORT=5432
      - KONG_PG_USER=kong
      - KONG_PG_PASSWORD=kong
      - KONG_PG_DATABASE=kong
    ports:
      - "8000:8000"  # Proxy Port
      - "8001:8001"  # Admin API Port
    depends_on:
      - kong-database

  konga:
    image: pantsel/konga:latest
    environment:
      - KONGA_HOST=http://kong:8001
    ports:
      - "1337:1337"
    depends_on:
      - kong
```

## 2. Start the Containers

Run the following command to start the Kong Gateway, database, and Konga UI services:

```bash
docker-compose up -d
```

This will start the containers in the background.

## 3. Registering a Service, Route, and Target using cURL

### Step 1: Register a Service

To register a service in Kong, use the following `cURL` command:

```bash
curl -i -X POST http://localhost:8001/services   --data name=spring-boot-service   --data url=http://spring-boot-app:8080
```

### Step 2: Register a Route for the Service

Next, register a route for the service to define the path:

```bash
curl -i -X POST http://localhost:8001/services/spring-boot-service/routes   --data 'paths[]=/spring-boot'
```

### Step 3: Register Targets for the Service

If you want to register a target (backend server for your service), use this:

```bash
curl -i -X POST http://localhost:8001/services/spring-boot-service/targets   --data target=spring-boot-app:8080   --data weight=100
```

## 4. Verify Services, Routes, and Targets

You can verify that the service, routes, and targets are registered properly by using these `cURL` commands:

- List Services:

```bash
curl -i http://localhost:8001/services
```

- List Routes:

```bash
curl -i http://localhost:8001/routes
```

- List Targets:

```bash
curl -i http://localhost:8001/services/spring-boot-service/targets
```

## 5. Access Konga UI for Kong Management

Konga is a UI tool to manage Kong Gateway. You can access the Konga UI at `http://localhost:1337` in your web browser. Log in using default credentials if prompted.

## 6. Stop the Containers

To stop the containers and clean up, use the following command:

```bash
docker-compose down
```

This will stop the services and remove the containers but keep your configurations intact.

<img width="907" alt="image" src="https://github.com/user-attachments/assets/6c598c1d-dd8e-4e5d-b9b2-b0540a91911a" />

