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
