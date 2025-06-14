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
