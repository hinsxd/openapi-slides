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
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
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

