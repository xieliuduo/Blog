typeScript使用技巧



函数参数类型约束

先说一个场景

我们希望参数 `a` 和 `b` 的类型都是一致的，即 `a` 和 `b` 同时为 `number` 或 `string` 类型。当它们的类型不一致的值，TS 类型检查器能自动提示对应的错误信息

```typescript
function f(a: string | number, b: string | number) {
  if (typeof a === 'string') {
    return a + ':' + b; // no error but b can be number!
  } else {
    return a + b; // error as b can be number | string
  }
}

f(2, 3); // Ok
f(1, 'a'); // Error
f('a', 2); // Error
f('a', 'b') // Ok
```

ts的函数重载

#### 函数重载

```ts
function f(a: string, b: string): string
function f(a: number, b: number): number
function f(a: string | number, b: string | number ): string | number {
  if (typeof a === 'string') {
    return a + ':' + b;
  } else {
    return ((a as number) + (b as number));
  }
}

f(2, 3); // Ok
f(1, 'a'); // Error
f('a', 2); // Error
f('a', 'b') // Ok
```

参数约定为一种类型 并用  `...args` 结构 

```ts
const isStrArr = (a: string[] | number[]): a is string[] => typeof a[0] === 'string'

function f(...args: string[] | number[]) {
  if (isStrArr(args)) {
    return args[0] + ':' + args[1];
  } else {
    return args[0] + args[1];
  }
}

f(2, 3); // Ok
f(1, 'a'); // Error
f('a', 2); // Error
f('a', 'b') // Ok
```

参数继承 

```ts
function f<T extends string | number>(a: T, b: T) {
  if (typeof a === 'string') {
    return a + ':' + b; // no error but b can be number!
  } else {
    return (a as number) + (b as number); // error as b can be number | string
  }
}

f(2, 3); // Ok
f(1, 'a'); // Error
f('a', 2); // Error
f(2, 2) // Ok
```
