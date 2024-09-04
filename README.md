# playwright-prisma

Setup Prisma with Playwright, Docker, and PostgreSQL

1. Set Up the Project
  First, create a new directory for your project and initialize it with npm.
```bash
mkdir playwright-prisma-project
cd playwright-prisma-project
npm init -y
```

2. Install Dependencies
  Install Playwright, Prisma, and the PostgreSQL client:
```bash
npm install playwright @playwright/test prisma @prisma/client
npm install --save-dev ts-node typescript
```

3. Initialize Prisma
Run Prisma's initialization command to set up Prisma in your project:
```bash
npx prisma init
```

4. Update `schema.prisma`
Edit the `prisma/schema.prisma` file to define your PostgreSQL datasource and a sample model. Update it like this:
```bash
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
```

6. Set Up Docker and PostgreSQL
Create a `docker-compose.yml` file to define your PostgreSQL service:
```bash
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
```

6. Update Environment Variables
Edit the `.env` file to include the connection string for PostgreSQL:
```bash
DATABASE_URL="postgresql://user:password@localhost:5432/testdb"
```

8. Run Docker Compose
Start your PostgreSQL container using Docker Compose:
```bash
docker-compose up -d
```

10. Generate Prisma Client
Now that the PostgreSQL database is running, use Prisma to generate the Prisma client:
```bash
npx prisma migrate dev --name init
npx prisma generate
```

12. Set Up Playwright Test with Prisma
Create a sample Playwright test that connects to the Prisma client:
```bash
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
```

14. Run the Tests
Run your Playwright tests using the Playwright test runner:
```bash
npx playwright test
```
