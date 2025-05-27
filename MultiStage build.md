 Go (Golang) Application
Dockerfile
Copy
Edit
# Stage 1: Build
FROM golang:1.20-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o main .

# Stage 2: Run
FROM alpine:latest
WORKDIR /app
COPY --from=builder /app/main .
CMD ["./main"]
✅ Why it's good: The final image is extremely small (~10MB) since it only contains the binary.

📦 2. Node.js Application
Dockerfile
Copy
Edit
# Stage 1: Build
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Run
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json .
CMD ["node", "dist/index.js"]
✅ Why it's good: Only the production build is kept in the final image — not the full source code.

🌐 3. React Application (Frontend SPA)
Dockerfile
Copy
Edit
# Stage 1: Build
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
✅ Why it's good: React gets compiled into static files, served with a lightweight Nginx server.

🐘 4. Python Flask Application
Dockerfile
Copy
Edit
# Stage 1: Install dependencies
FROM python:3.10-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Stage 2: Copy app and run
FROM python:3.10-slim
WORKDIR /app
COPY --from=builder /usr/local/lib/python3.10/site-packages /usr/local/lib/python3.10/site-packages
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
✅ Why it's good: Dependencies are layered and cached; smaller image compared to including pip and source.

☕ 5. Java Spring Boot Application
Dockerfile
Copy
Edit
# Stage 1: Build
FROM maven:3.9.3-eclipse-temurin-17 AS builder
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

# Stage 2: Run
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
✅ Why it's good: Uses a lightweight JRE-only image, avoids shipping the entire Maven build environment.

🐘 6. PostgreSQL Backup Tool (Example Bash + pg_dump)
Dockerfile
Copy
Edit
# Stage 1: Base image with tooling
FROM postgres:15 AS base
WORKDIR /backup
COPY backup.sh .
RUN chmod +x backup.sh

# Final stage with cron or manual execution
FROM debian:bullseye-slim
COPY --from=base /usr/bin/pg_dump /usr/bin/pg_dump
COPY --from=base /backup/backup.sh /backup.sh
ENTRYPOINT ["sh", "/backup.sh"]
✅ Why it's good: Only necessary binary (pg_dump) and script are carried over to the final image.

🧪 How to Demonstrate in Interview
You can say:

"I follow multi-stage builds for production Dockerfiles to reduce image size and limit security exposure. Here's an example of a Node.js app where I separate build and runtime stages..."

Or:

"For a Go or Java app, I always use multi-stage Dockerfiles so only the compiled binary or JAR enters the final runtime image."
