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

```ts {1-6}
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