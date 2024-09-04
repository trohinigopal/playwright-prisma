# playwright-prisma
example to connect prisma, palywright and postgres DB
Setting Up Prisma with Playwright and PostgreSQL in a Docker Container
Step 1: Initialize the Project
1. Create a new project directory and initialize a Node.js project:
 ```bash
 mkdir playwright-prisma-postgres
 cd playwright-prisma-postgres
 npm init -y
 ```
2. Install dependencies:
 ```bash
 npm install @playwright/test prisma @prisma/client pg
 npm install -g prisma
 ```
Step 2: Set Up Docker for PostgreSQL
1. Create `docker-compose.yml`:
 ```yaml
 version: '3.8'
 services:
 postgres:
 image: postgres:latest
 restart: always
 environment:
 POSTGRES_USER: user
 POSTGRES_PASSWORD: password
 POSTGRES_DB: mydb
 ports:
 - "5432:5432"
 volumes:
 - postgres-data:/var/lib/postgresql/data
 volumes:
 postgres-data:
 ```
2. Run Docker Compose:
 ```bash
 docker-compose up -d
 ```
