## heading...

#### Install Pino
```bash
npm install pino
```
---

#### Development-এ সুন্দর করে log দেখতে চাইলে:
```bash
npm install -D pino-pretty
```
---

#### src/lib/logger.ts
```bash
import pino from "pino";

export const log = pino({
    level: process.env.LOG_LEVEL || "info",

    transport:
        process.env.NODE_ENV === "development"
            ? {
                  target: "pino-pretty",
                  options: {
                      colorize: true,
                      translateTime: "SYS:standard",
                  },
              }
            : undefined,
});
```
---
