---
# You can also start simply with 'default'
theme: dracula
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
# background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Fullstack OpenAPI
info: |
  ## Fullstack OpenAPI
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: fade-out
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
lineNumbers: true
---

# Fullstack OpenAPI

## End-to-end type-safety for API


---
layout: intro
---

# Outline

<v-clicks depth="2">

- Why Schema?
- OpenAPI
- Backends
  - NestJS
  - Fastify
- Frontend
  - React with Orval

</v-clicks>

---

# Why Schema?

```js
function add1(a, b) {
  return a + b;
}
add1(1, 2); // 3
add1(1, "2"); // '12'
add1(41, true); // 42

```

<v-click>

```ts twoslash
function add2(a: number, b: number) {
  return a + b;
}

add2(1, 2);
add2(1, "2");
```

</v-click>

<v-click>

- Provides a contract between function maker and user
- A source of truth for validation
- Elimates guessing

</v-click>

---

# What about API?

`fetch` has always been `any`

````md magic-move
```ts
const result = await fetch("https://your-api/users/1", {
  body: JSON.stringify({
    // what should I put here?
  }),
}).then((r) => r.json());
```

```ts {1-6,9}
type GetUserBody = {
  id: number;
};
const body: GetUserBody = {
  id: 1,
};

const result = await fetch("https://your-api/users/1", {
  body: JSON.stringify(body),
}).then((r) => r.json());
```

```ts {4-6,11}
type GetUserBody = {
  id: number;
};
type User = {
  id: number;
};
const body: GetUserBody = {
  id: 1,
};

const result: User = await fetch("https://your-api/users/1", {
  body: JSON.stringify(body),
}).then((r) => r.json());
```
````

<div v-click fade-in>

Problem:

- validation
- requires manual typing

</div>

---


# OpenAPI (former swagger) come to rescue!


<div class="grid grid-cols-2 gap-4">

<div>

```yaml {all|2,7-13|17-23|29-}{maxHeight:'400px'}
paths:
  /planets/{planetId}:
    post:
      operationId: createPlanet
      summary: Create a planet
      description: Create a planet
      parameters:
      - name: planetId
        in: path
        required: true
        schema:
          type: number
          example: 4
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                name:
                  type: string
                  description: Name of planet
                  example: "Saturn"
      responses:
        '201':
          description: The planet
          content:
            application/json:
              schema:
                type: object
                properties:
                  name:
                    type: string
                    description: Name of planet
                    example: "Saturn"
```
</div>

<div>

<div class="text-2xl font-bold">We can specify</div>

<v-clicks at="1">

- URL parameters
- the shape of the request body
- the shape of the response body
- and more!
</v-clicks>

</div>
</div>

---
layout: image-right
image: https://docs.nestjs.com/assets/logo-small-gradient.svg
class: flex flex-col items-center justify-center
---

<div class="text-[64px] font-bold text-right">
NestJS Demo
</div>

---

# NestJS Demo

<div class="grid grid-cols-2 gap-4">

````md magic-move
```ts 
// animal.dto.ts
class CreateAnimalDto {
  name: string;
}


// animal.entity.ts
class Animal {
  id: number;
  name: string;
}

```

```ts {2,5,10,13,16}
// animal.dto.ts
import { ApiProperty } from '@nestjs/swagger';

class CreateAnimalDto {
  @ApiProperty()
  name: string;
}

// animal.entity.ts
import { ApiProperty } from '@nestjs/swagger';

class Animal {
  @ApiProperty()
  id: number;

  @ApiProperty()
  name: string;
}
```

````

````md magic-move {wrap:true,at:1}
```ts 
// animal.controller.ts
@Controller('animal')
export class AnimalController {
  @Post()
  create(@Body() createAnimalDto: CreateAnimalDto) {
    return this.animalService.create(createAnimalDto);
  }
}
```

```ts {5-9,12}
// animal.controller.ts
@Controller('animal')
export class AnimalController {
  @Post()
  @ApiResponse({
    status: 201,
    description: 'Success',
    type: Animal,
  })
  create(
    @Body() createAnimalDto: CreateAnimalDto,
  ): Promise<Animal> {
    return this.animalService.create(createAnimalDto);
  }
}
```
````
</div>

---

# Generated OpenAPI Schema

```json {all|8-17|18-29|13,24|53-63|64-82}{maxHeight:'450px'}
{
  "openapi": "3.0.0",
  "paths": {
    "/animal": {
      "post": {
        "operationId": "AnimalController_create",
        "parameters": [],
        "requestBody": {
          "required": true,
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/CreateAnimalDto"
              }
            }
          }
        },
        "responses": {
          "201": {
            "description": "The record has been successfully created.",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/Animal"
                }
              }
            }
          }
        },
        "tags": [
          "Animal"
        ]
      }
    }
  },
  "info": {
    "title": "Nestjs OpenAPI example",
    "description": "The Nestjs OpenAPI example",
    "version": "1.0",
    "contact": {

    }
  },
  "tags": [
    {
      "name": "Nestjs OpenAPI",
      "description": ""
    }
  ],
  "servers": [],
  "components": {
    "schemas": {
      "CreateAnimalDto": {
        "type": "object",
        "properties": {
          "name": {
            "type": "string"
          }
        },
        "required": [
          "name"
        ]
      },
      "Animal": {
        "type": "object",
        "properties": {
          "id": {
            "type": "number",
            "description": "The id of the animal",
            "example": "1"
          },
          "name": {
            "type": "string",
            "description": "The name of the animal",
            "example": "Dog"
          }
        },
        "required": [
          "id",
          "name"
        ]
      }
    }
  }
}
```

---

# Request Body Validation

https://docs.nestjs.com/techniques/validation

## Global Validation

<div class="grid grid-cols-2 gap-4">

````md magic-move
```ts
const app = await NestFactory.create(AppModule);
await app.listen(3000);
```
```ts {1,4-6}
import { ValidationPipe } from '@nestjs/common';

const app = await NestFactory.create(AppModule);
app.useGlobalPipes(new ValidationPipe({
  whitelist: true, // remove extra properties
}));
await app.listen(3000);
```
````

````md magic-move {at:1}
```ts
// animal.dto.ts
import { ApiProperty } from '@nestjs/swagger';

class CreateAnimalDto {
  @ApiProperty()
  name: string;
}
```
```ts {3,7}
// animal.dto.ts
import { ApiProperty } from '@nestjs/swagger';
import { IsString } from 'class-validator';

class CreateAnimalDto {
  @ApiProperty()
  @IsString()
  name: string;
}
```
````

</div>

<v-click>

## Result
````md magic-move
```bash
➜ curl -X POST 'http://localhost:3000/animal' -d '{ "name": 1 }'
```
```bash
➜ curl -X POST 'http://localhost:3000/animal' -d '{ "name": 1 }'
{"message":["name must be a string"],"error":"Bad Request","statusCode":400}
```
````

</v-click>

<v-click>
Clear error message!

</v-click>

---

# Reducing boilerplate with NestJS CLI Plugin

https://docs.nestjs.com/openapi/cli-plugin


<v-click>

The Swagger plugin will automatically:

<v-clicks at=2>

- annotate all DTO properties with `@ApiProperty` unless `@ApiHideProperty` is used
- set the required property depending on the question mark 
  - e.g. `name?: string` will set `required: false`
- set the `type` or `enum` property depending on the type (supports arrays as well)
- set the `default` property based on the assigned default value
- set several validation rules based on `class-validator` decorators (if `classValidatorShim` set to true)
- add a response decorator to every endpoint with a proper `status` and `type` (response model)
- generate descriptions for properties and endpoints based on comments (if `introspectComments` set to true)
- generate example values for properties based on comments (if `introspectComments` set to true)

</v-clicks>

</v-click>

---

# Reducing boilerplate with NestJS CLI Plugin

````md magic-move
```ts
// animal.dto.ts
import { ApiProperty } from '@nestjs/swagger';
import { IsString } from 'class-validator';

class CreateAnimalDto {
  @ApiProperty({ description: 'The name of the animal' })
  @IsString()
  name: string;
}
```
```ts
// animal.dto.ts
class CreateAnimalDto {
  /** The name of the animal */
  name: string;
}
```
````

````md magic-move
```ts
// animal.controller.ts
@Controller('animal')
export class AnimalController {
  @Post()
  @ApiResponse({
    status: 201,
    description: 'Success',
    type: Animal,
  })
  @ApiOperation({ summary: "Create an animal" })
  create(@Body() createAnimalDto: CreateAnimalDto): Promise<Animal> {
    return this.animalService.create(createAnimalDto);
  }
}
```
```ts
// animal.controller.ts
@Controller('animal')
export class AnimalController {
  /** Create an animal */
  @Post()
  create(@Body() createAnimalDto: CreateAnimalDto): Promise<Animal> {
    return this.animalService.create(createAnimalDto);
  }
}
```
````

---
layout: image
image: https://fastify.dev/img/logos/fastify-white.svg
backgroundSize: 50%
---

---

# Fastify

Let's start with a basic Fastify server

<div class="grid grid-cols-2 gap-4">

<!-- Left -->
````md magic-move
```ts
import Fastify from 'fastify';

const fastify = Fastify();

fastify.post(
  '/animal',
  (req, res) => {
    res.send('Hello World');
  }
);

fastify.listen({ port: 3000 }, (err, address) => {})
```
```ts {6}
import Fastify from 'fastify';

const fastify = Fastify();

fastify.post<{
  Body: CreateAnimalDto
}>(
  '/animal',
  (req, res) => {
    res.send('Hello World');
  }
);

fastify.listen({ port: 3000 }, (err, address) => {})
```
```ts {7-9}
import Fastify from 'fastify';

const fastify = Fastify();

fastify.post<{
  Body: CreateAnimalDto,
  Reply: {
    201: Animal
  }
}>(
  '/animal',
  (req, res) => {
    res.status(201).send({ id: 1, name: req.body.name });
  }
);

fastify.listen({ port: 3000 }, (err, address) => {})
```
```ts {9-11}
fastify.post<{
  Body: CreateAnimalDto,
  Reply: {
    201: Animal
  }
}>(
  '/animal',
  {
    schema: {
      body: createAnimalDtoSchema,
    }
  },
  (req, res) => {
    res.status(201).send({ id: 1, name: req.body.name });
  }
);
```
```ts {11-13}
fastify.post<{
  Body: CreateAnimalDto,
  Reply: {
    201: Animal
  }
}>(
  '/animal',
  {
    schema: {
      body: createAnimalDtoSchema,
      reply: {
        201: animalSchema,
      }
    }
  },
  (req, res) => {
    res.status(201).send({ id: 1, name: req.body.name });
  }
);
```
````

<div>

<!-- Right -->
````md magic-move {at:1}
```ts
// types.ts
```
```ts {2-4}
// types.ts
type CreateAnimalDto = {
  name: string;
}
```
```ts {6-8}
// types.ts
type CreateAnimalDto = {
  name: string;
}

type Animal = {
  id: number;
  name: string;
}
```
```ts
// schemas.ts
const createAnimalDtoSchema = {
  type: 'object',
  properties: {
    name: { type: 'string' },
  },
  required: ['name'],
}
```
```ts {10-17}
// schemas.ts
const createAnimalDtoSchema = {
  type: 'object',
  properties: {
    name: { type: 'string' },
  },
  required: ['name'],
}

const animalSchema = {
  type: 'object',
  properties: {
    id: { type: 'number' },
    name: { type: 'string' },
  },
  required: ['id', 'name'],
}
```
````
<v-click>

<div class="flex items-center justify-center">
  <div class="text-4xl">👆</div> Where should this come from?
</div>

</v-click>
</div>

</div>

---

# If we look closer

<div class="grid grid-cols-2 gap-4">

<v-switch>
<template #1 transition="fade">

```ts
type CreateAnimalDto = {
  name: string;
}
type Animal = {
  id: number;
  name: string;
}
const createAnimalDtoSchema = {
  type: 'object',
  properties: {
    name: { type: 'string' },
  },
  required: ['name'],
}
const animalSchema = {
  type: 'object',
  properties: {
    id: { type: 'number' },
    name: { type: 'string' },
  },
  required: ['id', 'name'],
}
```
</template>

<template #2>

```ts {2-5,9-14}
fastify.post<{
  Body: CreateAnimalDto,
  Reply: {
    201: Animal
  }
}>(
  '/animal',
  {
    schema: {
      body: createAnimalDtoSchema,
      reply: {
        201: animalSchema,
      }
    }
  },
  (req, res) => {
    res.send('Hello World');
  }
);
```
</template>
</v-switch>

<v-clicks at="1">

- Can we infer the types from schemas?
- Can we infer the fastify schema from schemas?

</v-clicks>

</div>

---
layout: cover
---

# Yes! 🎉

---

# Type Providers

````md magic-move
```ts
const fastify = Fastify()

fastify.post<{
  Body: CreateAnimalDto,
  Reply: {
    201: Animal
  }
}>(
  '/animal',
  {
    schema: {
      body: createAnimalDtoSchema,
      reply: {
        201: animalSchema,
      }
    }
  },
  (req, res) => {
    res.status(201).send({ id: 1, name: req.body.name });
  }
);
```
```ts {1-2|7-12|15-18}
import type { JsonSchemaToTsProvider } from "@fastify/type-provider-json-schema-to-ts";
const fastify = Fastify().withTypeProvider<JsonSchemaToTsProvider>()

fastify.post(
  '/animal',
  {
    schema: {
      body: createAnimalDtoSchema,
      reply: {
        201: animalSchema,
      }
    } // Types are inferred from the schema
  },
  (req, res) => {
    res.status(201).send({ id: 1, name: req.body.name });

    // Error: Property 'id' is missing in type '{ name: string; }'
    res.status(201).send({ name: req.body.name });
  }
);
```
````

<v-click>
No need to create types manually!
</v-click>

---

# Or if you use routes as plugins

```ts
import type { FastifyPluginAsyncJsonSchemaToTs } from "@fastify/type-provider-json-schema-to-ts";
const plugin: FastifyPluginAsyncJsonSchemaToTs = async (
  fastify,
  options
) => {
  fastify.post(
    "/animals",
    {
      schema: {
        body: createAnimalDto,
        response: {
          "201": animalSchema,
        },
      },
    },
    (request, reply) => {
      reply.status(201).send({
        id: 1,
        name: request.body.name,
      });
    }
  );
};
```

---

# OpenAPI schema

https://github.com/fastify/fastify-swagger

## Setup

```ts
import Fastify from 'fastify'

const fastify = Fastify()


await fastify.register(import('@fastify/swagger'))

await fastify.register(import('@fastify/swagger-ui'), {
  routePrefix: "/documentation",
  // ...just follow the docs
})

// Then add your schema
fastify.addSchema(createAnimalDtoSchema)
fastify.addSchema(animalSchema)
```



---


# Looks good!

<img src="/fastify_ui_1.png" class="max-h-[400px]" />

---

# Really?

```json {*|27-39|48-59}{maxHeight:'350px'}
{
  "openapi": "3.0.3",
  "info": {
    "version": "9.5.1",
    "title": "@fastify/swagger"
  },
  "components": {
    "schemas": {
      "def-0": {
        "type": "object",
        "properties": {
          "id": {
            "type": "number",
            "description": "The id of the animal"
          },
          "name": {
            "type": "string",
            "description": "The name of the animal"
          }
        },
        "required": [
          "id",
          "name"
        ],
        "title": "Animal"
      },
      "def-1": {
        "type": "object",
        "properties": {
          "name": {
            "type": "string",
            "description": "The name of the animal"
          }
        },
        "required": [
          "name"
        ],
        "title": "CreateAnimalDto"
      }
    }
  },
  "paths": {
    "/animals": {
      "post": {
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "type": "object",
                "properties": {
                  "name": {
                    "type": "string",
                    "description": "The name of the animal"
                  }
                },
                "required": [
                  "name"
                ]
              }
            }
          },
          "required": true
        },
        "responses": {
          "201": {
            "description": "Default Response",
            "content": {
              "application/json": {
                "schema": {
                  "type": "object",
                  "properties": {
                    "id": {
                      "type": "number",
                      "description": "The id of the animal"
                    },
                    "name": {
                      "type": "string",
                      "description": "The name of the animal"
                    }
                  },
                  "required": [
                    "id",
                    "name"
                  ]
                }
              }
            }
          }
        }
      }
    }
  }
}
```

<v-clicks at="0">

What is `def-1`?

Why isn't it referenced?

</v-clicks>

---

# What we want

It would be better if we could reference the schemas by name, while keeping the type inference

<div class="grid grid-cols-2 gap-4">

````md magic-move {at:1}
```json
  "components": {
    "schemas": {
      "def-1": {
        "type": "object",
        "properties": {
          "name": {
            "type": "string",
            "description": "The name of the animal"
          }
        },
        "required": [
          "name"
        ],
        "title": "CreateAnimalDto"
      }
    }
  },
```
```json {3}
  "components": {
    "schemas": {
      "CreateAnimalDto": {
        "type": "object",
        "properties": {
          "name": {
            "type": "string",
            "description": "The name of the animal"
          }
        },
        "required": [
          "name"
        ],
        "title": "CreateAnimalDto"
      }
    }
  },
```

````

````md magic-move {at:1}
```json
  "requestBody": {
    "content": {
      "application/json": {
        "schema": {
          "type": "object",
          "properties": {
            "name": {
              "type": "string",
              "description": "The name of the animal"
            }
          },
          "required": [
            "name"
          ]
        }
      }
    },
    "required": true
  },
```
```json {5}
  "requestBody": {
    "content": {
      "application/json": {
        "schema": {
          "$ref": "#/components/schemas/CreateAnimalDto"
        }
      }
    },
    "required": true
  },
```
````

</div>

<v-click at="2">

TS files should be unchanged!

</v-click>

---

# Last secret sauce

Fact: we can refer to schemas by `$id`

```ts
  fastify.post(
    "/animals",
    {
      schema: {
        body: { $ref: "CreateAnimalDto" },
      },
    },
    // ...handler
  )
```

<v-click>

But this will lose type inference

</v-click>

---

# Let `@fastify/swagger` transform the schema

https://github.com/fastify/fastify-swagger?tab=readme-ov-file#managing-your-refs

https://github.com/fastify/fastify-swagger?tab=readme-ov-file#transform

```ts {7,9-14|22-30|2-5}{maxHeight:'350px'}
import type { JSONSchema } from "json-schema-to-ts";
function refFromSchema(schema: Exclude<JSONSchema, boolean>) {
  if (schema?.$id) return { $ref: schema.$id };
  return schema;
}

await fastify.register(import("@fastify/swagger"), {
  openapi: {},
  refResolver: {
    buildLocalReference: (json, baseUri, fragment, i) => {
      // Solve the def-1 problem
      return `${json.$id || baseUri.path}`;
    },
  },
  transform: ({ schema, url }) => {
    if (!schema) {
      return {
        schema,
        url,
      };
    }
    if (schema.response) {
      Object.keys(schema.response).forEach((key) => {
        schema.response[key] = refFromSchema(schema.response[key]);
      });
    }
    schema.body = refFromSchema(schema.body);
    schema.querystring = refFromSchema(schema.querystring);
    schema.headers = refFromSchema(schema.headers);
    schema.params = refFromSchema(schema.params);

    return {
      schema,
      url,
    };
  },

});
```

---


# Final result

```json {*|9-25|26-37|43-51|52-63}{maxHeight:'400px'}
{
  "openapi": "3.0.3",
  "info": {
    "version": "9.5.1",
    "title": "@fastify/swagger"
  },
  "components": {
    "schemas": {
      "Animal": {
        "type": "object",
        "properties": {
          "id": {
            "type": "number",
            "description": "The id of the animal"
          },
          "name": {
            "type": "string",
            "description": "The name of the animal"
          }
        },
        "required": [
          "id",
          "name"
        ]
      },
      "CreateAnimalDto": {
        "type": "object",
        "properties": {
          "name": {
            "type": "string",
            "description": "The name of the animal"
          }
        },
        "required": [
          "name"
        ]
      }
    }
  },
  "paths": {
    "/animals": {
      "post": {
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/CreateAnimalDto"
              }
            }
          }
        },
        "responses": {
          "201": {
            "description": "Default Response",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/Animal"
                }
              }
            }
          }
        }
      }
    }
  }
}
```

---

# React integration

https://orval.dev/

`orval.config.ts`
```ts {*|5|7|8|9|10|11}{maxHeight:'300px'}
import { defineConfig } from "orval";

export default defineConfig({
  api: {
    input: "http://localhost:4000/api-json",
    output: {
      workspace: "./src/api", // Base directory for the generated files
      target: "generated/index.ts", // Main file
      schemas: "generated/schemas", // Schemas directory
      client: "react-query", // Client library
      mode: "tags-split", // Split by tags
      clean: true,
      override: {
        query: {
          // version: 3 
          version: 5
        },
        mutator: {
          path: "./fetcher.ts",
          name: "fetcher",
        },
      },
    },
  },
});
```


<v-click>

Run `npx orval` to do the magic

</v-click>

---


# Fetcher

````md magic-move
```ts
import Axios, { type AxiosRequestConfig } from "axios";

export const AXIOS_INSTANCE = Axios.create({ baseURL: process.env.VITE_API_URL });

export const fetcher = async <T>(config: AxiosRequestConfig, options?: AxiosRequestConfig): Promise<T> => {
  const source = Axios.CancelToken.source();

  const promise = AXIOS_INSTANCE({
    ...config,
    ...options,
    cancelToken: source.token,
  }).then(({ data }) => data);

  (promise as any).cancel = () => {
    source.cancel("Query was cancelled");
  };

  return promise;
};

```

```ts {8-10,15}
import Axios, { type AxiosRequestConfig } from "axios";

export const AXIOS_INSTANCE = Axios.create({ baseURL: process.env.VITE_API_URL });

export const fetcher = async <T>(config: AxiosRequestConfig, options?: AxiosRequestConfig): Promise<T> => {
  const source = Axios.CancelToken.source();

  const token = await getToken();
  const headers = new Headers()
  if (token) headers.set("Authorization", `Bearer ${token}`);

  const promise = AXIOS_INSTANCE({
    ...config,
    ...options,
    headers: { ...options?.headers, ...headers },
    cancelToken: source.token,
  }).then(({ data }) => data);

  (promise as any).cancel = () => {
    source.cancel("Query was cancelled");
  };

  return promise;
};

```


````

---

# Generated code

```ts {*|1-13|241-257|123-125|141-150|153-171|220-240}{maxHeight:'400px'}
/**
 * @summary Get all animals
 */
export const animalControllerFindAll = (
  options?: SecondParameter<typeof fetcher>,
  signal?: AbortSignal
) => {
  return fetcher<Animal[]>({ url: `/animal`, method: "GET", signal }, options);
};

export const getAnimalControllerFindAllQueryKey = () => {
  return [`/animal`] as const;
};

export const getAnimalControllerFindAllQueryOptions = <
  TData = Awaited<ReturnType<typeof animalControllerFindAll>>,
  TError = unknown
>(options?: {
  query?: Partial<
    UseQueryOptions<
      Awaited<ReturnType<typeof animalControllerFindAll>>,
      TError,
      TData
    >
  >;
  request?: SecondParameter<typeof fetcher>;
}) => {
  const { query: queryOptions, request: requestOptions } = options ?? {};

  const queryKey =
    queryOptions?.queryKey ?? getAnimalControllerFindAllQueryKey();

  const queryFn: QueryFunction<
    Awaited<ReturnType<typeof animalControllerFindAll>>
  > = ({ signal }) => animalControllerFindAll(requestOptions, signal);

  return { queryKey, queryFn, ...queryOptions } as UseQueryOptions<
    Awaited<ReturnType<typeof animalControllerFindAll>>,
    TError,
    TData
  > & { queryKey: DataTag<QueryKey, TData, TError> };
};

export type AnimalControllerFindAllQueryResult = NonNullable<
  Awaited<ReturnType<typeof animalControllerFindAll>>
>;
export type AnimalControllerFindAllQueryError = unknown;

export function useAnimalControllerFindAll<
  TData = Awaited<ReturnType<typeof animalControllerFindAll>>,
  TError = unknown
>(
  options: {
    query: Partial<
      UseQueryOptions<
        Awaited<ReturnType<typeof animalControllerFindAll>>,
        TError,
        TData
      >
    > &
      Pick<
        DefinedInitialDataOptions<
          Awaited<ReturnType<typeof animalControllerFindAll>>,
          TError,
          Awaited<ReturnType<typeof animalControllerFindAll>>
        >,
        "initialData"
      >;
    request?: SecondParameter<typeof fetcher>;
  },
  queryClient?: QueryClient
): DefinedUseQueryResult<TData, TError> & {
  queryKey: DataTag<QueryKey, TData, TError>;
};
export function useAnimalControllerFindAll<
  TData = Awaited<ReturnType<typeof animalControllerFindAll>>,
  TError = unknown
>(
  options?: {
    query?: Partial<
      UseQueryOptions<
        Awaited<ReturnType<typeof animalControllerFindAll>>,
        TError,
        TData
      >
    > &
      Pick<
        UndefinedInitialDataOptions<
          Awaited<ReturnType<typeof animalControllerFindAll>>,
          TError,
          Awaited<ReturnType<typeof animalControllerFindAll>>
        >,
        "initialData"
      >;
    request?: SecondParameter<typeof fetcher>;
  },
  queryClient?: QueryClient
): UseQueryResult<TData, TError> & {
  queryKey: DataTag<QueryKey, TData, TError>;
};
export function useAnimalControllerFindAll<
  TData = Awaited<ReturnType<typeof animalControllerFindAll>>,
  TError = unknown
>(
  options?: {
    query?: Partial<
      UseQueryOptions<
        Awaited<ReturnType<typeof animalControllerFindAll>>,
        TError,
        TData
      >
    >;
    request?: SecondParameter<typeof fetcher>;
  },
  queryClient?: QueryClient
): UseQueryResult<TData, TError> & {
  queryKey: DataTag<QueryKey, TData, TError>;
};
/**
 * @summary Get all animals
 */

export function useAnimalControllerFindAll<
  TData = Awaited<ReturnType<typeof animalControllerFindAll>>,
  TError = unknown
>(
  options?: {
    query?: Partial<
      UseQueryOptions<
        Awaited<ReturnType<typeof animalControllerFindAll>>,
        TError,
        TData
      >
    >;
    request?: SecondParameter<typeof fetcher>;
  },
  queryClient?: QueryClient
): UseQueryResult<TData, TError> & {
  queryKey: DataTag<QueryKey, TData, TError>;
} {
  const queryOptions = getAnimalControllerFindAllQueryOptions(options);

  const query = useQuery(queryOptions, queryClient) as UseQueryResult<
    TData,
    TError
  > & { queryKey: DataTag<QueryKey, TData, TError> };

  query.queryKey = queryOptions.queryKey;

  return query;
}

/**
 * @summary Create an animal
 */
export const animalControllerCreate = (
  createAnimalDto: CreateAnimalDto,
  options?: SecondParameter<typeof fetcher>,
  signal?: AbortSignal
) => {
  return fetcher<Animal>(
    {
      url: `/animal`,
      method: "POST",
      headers: { "Content-Type": "application/json" },
      data: createAnimalDto,
      signal,
    },
    options
  );
};

export const getAnimalControllerCreateMutationOptions = <
  TError = unknown,
  TContext = unknown
>(options?: {
  mutation?: UseMutationOptions<
    Awaited<ReturnType<typeof animalControllerCreate>>,
    TError,
    { data: CreateAnimalDto },
    TContext
  >;
  request?: SecondParameter<typeof fetcher>;
}): UseMutationOptions<
  Awaited<ReturnType<typeof animalControllerCreate>>,
  TError,
  { data: CreateAnimalDto },
  TContext
> => {
  const mutationKey = ["animalControllerCreate"];
  const { mutation: mutationOptions, request: requestOptions } = options
    ? options.mutation &&
      "mutationKey" in options.mutation &&
      options.mutation.mutationKey
      ? options
      : { ...options, mutation: { ...options.mutation, mutationKey } }
    : { mutation: { mutationKey }, request: undefined };

  const mutationFn: MutationFunction<
    Awaited<ReturnType<typeof animalControllerCreate>>,
    { data: CreateAnimalDto }
  > = (props) => {
    const { data } = props ?? {};

    return animalControllerCreate(data, requestOptions);
  };

  return { mutationFn, ...mutationOptions };
};

export type AnimalControllerCreateMutationResult = NonNullable<
  Awaited<ReturnType<typeof animalControllerCreate>>
>;
export type AnimalControllerCreateMutationBody = CreateAnimalDto;
export type AnimalControllerCreateMutationError = unknown;

/**
 * @summary Create an animal
 */
export const useAnimalControllerCreate = <TError = unknown, TContext = unknown>(
  options?: {
    mutation?: UseMutationOptions<
      Awaited<ReturnType<typeof animalControllerCreate>>,
      TError,
      { data: CreateAnimalDto },
      TContext
    >;
    request?: SecondParameter<typeof fetcher>;
  },
  queryClient?: QueryClient
): UseMutationResult<
  Awaited<ReturnType<typeof animalControllerCreate>>,
  TError,
  { data: CreateAnimalDto },
  TContext
> => {
  const mutationOptions = getAnimalControllerCreateMutationOptions(options);

  return useMutation(mutationOptions, queryClient);
};
/**
 * @summary Get an animal by id
 */
export const animalControllerFindOneById = (
  id: number,
  options?: SecondParameter<typeof fetcher>,
  signal?: AbortSignal
) => {
  return fetcher<Animal>(
    { url: `/animal/${id}`, method: "GET", signal },
    options
  );
};

export const getAnimalControllerFindOneByIdQueryKey = (id: number) => {
  return [`/animal/${id}`] as const;
};

export const getAnimalControllerFindOneByIdQueryOptions = <
  TData = Awaited<ReturnType<typeof animalControllerFindOneById>>,
  TError = unknown
>(
  id: number,
  options?: {
    query?: Partial<
      UseQueryOptions<
        Awaited<ReturnType<typeof animalControllerFindOneById>>,
        TError,
        TData
      >
    >;
    request?: SecondParameter<typeof fetcher>;
  }
) => {
  const { query: queryOptions, request: requestOptions } = options ?? {};

  const queryKey =
    queryOptions?.queryKey ?? getAnimalControllerFindOneByIdQueryKey(id);

  const queryFn: QueryFunction<
    Awaited<ReturnType<typeof animalControllerFindOneById>>
  > = ({ signal }) => animalControllerFindOneById(id, requestOptions, signal);

  return {
    queryKey,
    queryFn,
    enabled: !!id,
    ...queryOptions,
  } as UseQueryOptions<
    Awaited<ReturnType<typeof animalControllerFindOneById>>,
    TError,
    TData
  > & { queryKey: DataTag<QueryKey, TData, TError> };
};

export type AnimalControllerFindOneByIdQueryResult = NonNullable<
  Awaited<ReturnType<typeof animalControllerFindOneById>>
>;
export type AnimalControllerFindOneByIdQueryError = unknown;

export function useAnimalControllerFindOneById<
  TData = Awaited<ReturnType<typeof animalControllerFindOneById>>,
  TError = unknown
>(
  id: number,
  options: {
    query: Partial<
      UseQueryOptions<
        Awaited<ReturnType<typeof animalControllerFindOneById>>,
        TError,
        TData
      >
    > &
      Pick<
        DefinedInitialDataOptions<
          Awaited<ReturnType<typeof animalControllerFindOneById>>,
          TError,
          Awaited<ReturnType<typeof animalControllerFindOneById>>
        >,
        "initialData"
      >;
    request?: SecondParameter<typeof fetcher>;
  },
  queryClient?: QueryClient
): DefinedUseQueryResult<TData, TError> & {
  queryKey: DataTag<QueryKey, TData, TError>;
};
export function useAnimalControllerFindOneById<
  TData = Awaited<ReturnType<typeof animalControllerFindOneById>>,
  TError = unknown
>(
  id: number,
  options?: {
    query?: Partial<
      UseQueryOptions<
        Awaited<ReturnType<typeof animalControllerFindOneById>>,
        TError,
        TData
      >
    > &
      Pick<
        UndefinedInitialDataOptions<
          Awaited<ReturnType<typeof animalControllerFindOneById>>,
          TError,
          Awaited<ReturnType<typeof animalControllerFindOneById>>
        >,
        "initialData"
      >;
    request?: SecondParameter<typeof fetcher>;
  },
  queryClient?: QueryClient
): UseQueryResult<TData, TError> & {
  queryKey: DataTag<QueryKey, TData, TError>;
};
export function useAnimalControllerFindOneById<
  TData = Awaited<ReturnType<typeof animalControllerFindOneById>>,
  TError = unknown
>(
  id: number,
  options?: {
    query?: Partial<
      UseQueryOptions<
        Awaited<ReturnType<typeof animalControllerFindOneById>>,
        TError,
        TData
      >
    >;
    request?: SecondParameter<typeof fetcher>;
  },
  queryClient?: QueryClient
): UseQueryResult<TData, TError> & {
  queryKey: DataTag<QueryKey, TData, TError>;
};
/**
 * @summary Get an animal by id
 */

export function useAnimalControllerFindOneById<
  TData = Awaited<ReturnType<typeof animalControllerFindOneById>>,
  TError = unknown
>(
  id: number,
  options?: {
    query?: Partial<
      UseQueryOptions<
        Awaited<ReturnType<typeof animalControllerFindOneById>>,
        TError,
        TData
      >
    >;
    request?: SecondParameter<typeof fetcher>;
  },
  queryClient?: QueryClient
): UseQueryResult<TData, TError> & {
  queryKey: DataTag<QueryKey, TData, TError>;
} {
  const queryOptions = getAnimalControllerFindOneByIdQueryOptions(id, options);

  const query = useQuery(queryOptions, queryClient) as UseQueryResult<
    TData,
    TError
  > & { queryKey: DataTag<QueryKey, TData, TError> };

  query.queryKey = queryOptions.queryKey;

  return query;
}

```

--- 

# Let's use it

```tsx {*|2-3|4,15|5-11|8}{maxHeight:'350px'}
const queryClient = useQueryClient();
const { data } = useAnimalControllerFindOneById(2);
//      ^? const data: Animal | undefined
const { mutate } = useAnimalControllerCreate({
  mutation: {
    onSettled: () => {
      queryClient.invalidateQueries({
        queryKey: getAnimalControllerFindAllQueryKey(),
      });
    },
  },
});

const handleCreate = () => {
  mutate({ data: { name: "giraffe" } });
};



```

---
layout: end
---

# Thank you!


<v-clicks>

Special thanks to:

- Rex
- CJ

Slide made with [Slidev](https://sli.dev)

</v-clicks>


