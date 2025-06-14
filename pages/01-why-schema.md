# Why Schema?

- Provides a contract between function maker and user
- A source of truth for validation
- Elimates guessing

<div class="my-auto">

```ts {*}{maxHeight:'170px'} twoslash
function add1(a: number, b: number) {
  return a + b;
}

add1(1, 2);
add1(1, "2");

function add2(a: any, b: any) {
  return a + b;
}

add2(1, 2); // 3
add2(1, "2"); // 12
add2([], {}); // '[object Object]'
add2(0, []); // '0'
add2(0, {}); // '0[object Object]'

```

</div>
