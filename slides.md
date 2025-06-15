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

## End-to-end-to-end type-safety for API

---

# Why Schema?

```js
function add2(a, b) {
  return a + b;
}
add2(1, 2); // 3
add2(1, "2"); // '12'
add2(41, true); // 42

```

<v-click>

```ts twoslash
function add1(a: number, b: number) {
  return a + b;
}

add1(1, 2);
add1(1, "2");
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
```ts {1,4}
import { ValidationPipe } from '@nestjs/common';

const app = await NestFactory.create(AppModule);
app.useGlobalPipes(new ValidationPipe());
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