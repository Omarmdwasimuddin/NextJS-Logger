## NextJS API---> Logger

#### src/lib/logger.ts for small project
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

#### app/api/order
```bash
import { NextRequest, NextResponse } from "next/server";
import prisma from "@/lib/prisma";
import { log } from "@/lib/logger";

export async function POST(request: NextRequest) {
    try {

        log.info("Create order request received", {
            route: "/api/order",
            method: request.method,
        });

        
        const product = await prisma.product.create({
            data: {
                name: "Node.js Course",
                description: "Complete Backend Course",
                price: 1500,
                imageUrl: "https://example.com/course.jpg",
                stock: 100,
            },
        });

        log.info("Product created successfully", {
            productId: product.id,
            productName: product.name,
            stock: product.stock,
        });

        
        if (product.stock < 10) {
            log.warn("Product stock is running low", {
                productId: product.id,
                stock: product.stock,
            });
        }

        
        const order = await prisma.order.create({
            data: {
                productId: product.id,
                quantity: 2,
                totalAmount: 3000,

                customerName: "Wasim",
                customerEmail: "wasim@example.com",
                customerPhone: "01711111111",
                customerAddress: "Dhaka",
                customerCity: "Dhaka",

                transactionId: "TXN-123456-bac",
            },
        });

        log.info("Order created successfully", {
            orderId: order.id,
            transactionId: order.transactionId,
            status: order.status,
            totalAmount: order.totalAmount.toString(),
        });

        return NextResponse.json(
            {
                status: "success",
                message: "Order created successfully",
                data: order,
            },
            {
                status: 200,
            }
        );

    } catch (error) {

        log.error("Failed to create order", {
            error: error instanceof Error ? error.message : String(error),
        });

        return NextResponse.json(
            {
                status: "error",
                message: "Something went wrong",
            },
            {
                status: 500,
            }
        );
    }
}
```
---
