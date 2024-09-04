# playwright-prisma
Setup Prisma with Playwright, Docker, and PostgreSQL
1. Set Up the Project
First, create a new directory for your project and initialize it with npm.
mkdir playwright-prisma-project
cd playwright-prisma-project
npm init -y
2. Install Dependencies
Install Playwright, Prisma, and the PostgreSQL client:
npm install playwright @playwright/test prisma @prisma/client
npm install --save-dev ts-node typescript
3. Initialize Prisma
Run Prisma's initialization command to set up Prisma in your project:
npx prisma init
4. Update `schema.prisma`
Edit the `prisma/schema.prisma` file to define your PostgreSQL datasource and a sample model. Update it like this:
generator client {
 provider = "prisma-client-js"
}
datasource db {
 provider = "postgresql"
 url = env("DATABASE_URL")
}
model User {
 id Int @id @default(autoincrement())
 name String
 email String @unique
}

Setup Prisma with Playwright, Docker, and PostgreSQL
5. Set Up Docker and PostgreSQL
Create a `docker-compose.yml` file to define your PostgreSQL service:
version: '3.8'
services:
 postgres:
 image: postgres:latest
 environment:
 POSTGRES_USER: user
 POSTGRES_PASSWORD: password
 POSTGRES_DB: testdb
 ports:
 - "5432:5432"
 volumes:
 - postgres-data:/var/lib/postgresql/data
volumes:
 postgres-data:
6. Update Environment Variables
Edit the `.env` file to include the connection string for PostgreSQL:
DATABASE_URL="postgresql://user:password@localhost:5432/testdb"
7. Run Docker Compose
Start your PostgreSQL container using Docker Compose:
docker-compose up -d
8. Generate Prisma Client
Now that the PostgreSQL database is running, use Prisma to generate the Prisma client:
npx prisma migrate dev --name init
npx prisma generate

Setup Prisma with Playwright, Docker, and PostgreSQL
9. Set Up Playwright Test with Prisma
Create a sample Playwright test that connects to the Prisma client:
import { test, expect } from '@playwright/test';
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();
test.beforeAll(async () => {
 await prisma.user.create({
 data: {
 name: 'John Doe',
 email: 'john.doe@example.com',
 },
 });
});
test('should have created user', async ({ page }) => {
 const user = await prisma.user.findUnique({
 where: { email: 'john.doe@example.com' },
 });
 expect(user).not.toBeNull();
 expect(user?.name).toBe('John Doe');
});
test.afterAll(async () => {
 await prisma.user.deleteMany();
 await prisma.$disconnect();
});
10. Run the Tests
Run your Playwright tests using the Playwright test runner:
npx playwright test

