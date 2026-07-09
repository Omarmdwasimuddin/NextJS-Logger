## NextJS Logger

#### src/lib/logger.ts
```bash
type LogContext = Record<string, unknown>;

function format(level: string, message: string, context?: LogContext) {
    return JSON.stringify({
        level,
        message,
        timestamp: new Date().toISOString(),
        ...context,
    });
}

export const log = {
    info(message: string, context?: LogContext) {
        console.log(format("info", message, context));
    },
    warn(message: string, context?: LogContext) {
        console.warn(format("warn", message, context));
    },
    error(message: string, context?: LogContext) {
        console.error(format("error", message, context));
    },
};
```
---

#### src/lib/prisma.ts
```bash
import { PrismaClient } from "@/app/generated/prisma/client";
import { PrismaPg } from "@prisma/adapter-pg";

const globalForPrisma = global as unknown as { 
    prisma: PrismaClient;
 };

 const adapter = new PrismaPg({
    connectionString: process.env.DATABASE_URL,
 });

 const prisma = globalForPrisma.prisma || new PrismaClient({ adapter, log: ['error', 'warn', 'query', 'info']});

 if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = prisma;

 export default prisma;

```
---
