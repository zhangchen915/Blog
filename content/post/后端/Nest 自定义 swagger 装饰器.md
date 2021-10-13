---
title: "Nest 自定义 swagger 装饰器"
categories: ["Nest.js"]
tags: []
draft: false
slug: "nest-swagger-customer-decorate"
date: "2021-09-03 18:01:45"
---

@nestjs/swagger 是为Nest定制的Swagger模块，用装饰器的方式来生成描述RESTful api的文档。

一般我们会在全局写一个过滤器，放上自定义的状态码和信息，所以我们需要Swagger装饰器也能生成相应格式的返回信息

下面 data 放的是返回数据，通过 getSchemaPath 方法获取对应类型，注意要对数组做一个单独判断
```ts
import type { Type } from '@nestjs/common';
import { applyDecorators } from '@nestjs/common';
import { ApiExtraModels, ApiOkResponse, getSchemaPath } from '@nestjs/swagger';

export function ApiCommonResponse<T extends Type>(
  model: T,
  description?: string,
  isArray?: boolean,
): MethodDecorator {
  const ref = { $ref: getSchemaPath(model) };
  return applyDecorators(
    ApiExtraModels(model),
    ApiOkResponse({
      description,
      schema: {
        allOf: [
          {
            properties: {
              data: isArray ? { type: 'array', items: ref } : ref,
            },
          },
          {
            properties: {
              status: {
                type: 'number',
              },
            },
          },
          {
            properties: {
              message: {
                type: 'string',
              },
            },
          },
        ],
      },
    }),
  );
}

```
