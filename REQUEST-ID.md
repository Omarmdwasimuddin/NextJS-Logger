## Request Tracking with requestId

#### app/api/product/create

![](https://imgur.com/vecX0nn.png)
![](https://imgur.com/dTheUNE.png)

```bash
import { NextRequest, NextResponse } from "next/server";
import prisma from "@/lib/prisma";
import { log } from "@/lib/logger";
import { createProductSchema } from "@/lib/validations/product.schema"

export async function POST(request: NextRequest) {
        const requestId = crypto.randomUUID();
    try {
        log.info(
            {
                requestId,
                path: request.nextUrl.pathname,
                method: request.method,
            },
            "Insert product item received"
        )

        const jsonBody = await request.json();

        const validation = createProductSchema.safeParse(jsonBody);

        if (!validation.success) {
            log.warn(
                {
                    requestId,
                    errors: validation.error.flatten().fieldErrors
                },
                "Invalid input"
            )
            return NextResponse.json(
                {status: "fail", requestId, message: "Invalid Input", errors: validation.error.flatten().fieldErrors},
                {status: 400}
            )
        }

        const createProductItem = await prisma.product.create({
            data: validation.data,
            select: {
                id: true,
                name: true,
                createdAt: true,
                updatedAt: true,

            }
        })

        log.info(
            {
                requestId,
                productID: createProductItem.id,
                productName: createProductItem.name,
                createdAt: createProductItem.createdAt,
                updatedAt: createProductItem.updatedAt,
            },
            "Add product item successfully!"
        )
        return NextResponse.json(
            {status: "success", requestId, message: "Add Product Item Successfully!", data: createProductItem},
            {status: 201}
        )
    } catch (error) {
        log.error(
            { 
                requestId,
                err: error instanceof Error ? error.message : String(error),
             },
            "Product insert failed"
        )
        return NextResponse.json(
            {status: "fail", requestId, message: "Product Insert Failed!"},
            {status: 500}
        )
    }
}
```
---
