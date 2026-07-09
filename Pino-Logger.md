## NextJS API ---> Pino Logger

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

#### src/lib/logger.ts   [Production + Development]
```bash
import pino from "pino";

export const log = pino({
    level: process.env.LOG_LEVEL || "info",
    timestamp: pino.stdTimeFunctions.isoTime,

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

#### app/api/order
```bash
import prisma from "@/lib/prisma";
import { log } from "@/lib/logger";
import { NextRequest, NextResponse } from "next/server";

export async function POST(request: NextRequest) {
    try {

        log.info("Create order request received");

        const orderData = await request.json();

        const { searchParams } = new URL(request.url);
        const productId = searchParams.get("id");

        if (!productId) {

            log.warn(
                {
                    route: request.nextUrl.pathname,
                },
                "Product ID is missing"
            );

            return NextResponse.json(
                {
                    status: "error",
                    message: "Product ID is required.",
                },
                {
                    status: 400,
                }
            );
        }

        log.info(
            {
                productId,
                customerEmail: orderData.customerEmail,
            },
            "Creating new order"
        );

        const newOrder = await prisma.order.create({
            data: {
                ...orderData,
                productId,
            },
        });

        log.info(
            {
                orderId: newOrder.id,
                transactionId: newOrder.transactionId,
                status: newOrder.status,
            },
            "Order created successfully"
        );

        return NextResponse.json(
            {
                status: "success",
                message: "Order inserted successfully!",
                data: newOrder,
            },
            {
                status: 200,
            }
        );

    } catch (error) {

        log.error(
            {
                error,
            },
            "Failed to create order"
        );

        return NextResponse.json(
            {
                status: "error",
                message: "Failed to insert order.",
            },
            {
                status: 500,
            }
        );
    }
}
```

![](https://imgur.com/5ALpXgB.png)
![](https://imgur.com/PW1KyHR.png)
---
