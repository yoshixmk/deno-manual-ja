## アサーション

開発者がテストを記述できるように、Deno標準ライブラリには、
`https：//deno.land/std@0.65.0/testing/asserts.ts`からインポートできる組み込みの[アサーションモジュール](https://deno.land/std/testing/asserts.ts)が付属しています。

```js
import { assert } from "https://deno.land/std@0.65.0/testing/asserts.ts";

Deno.test("Hello Test", () => {
  assert("Hello");
});
```

アサーションモジュールは、9つのアサーションを提供します：

- `assert(expr: unknown, msg = ""): asserts expr`
- `assertEquals(actual: unknown, expected: unknown, msg?: string): void`
- `assertNotEquals(actual: unknown, expected: unknown, msg?: string): void`
- `assertStrictEquals(actual: unknown, expected: unknown, msg?: string): void`
- `assertStringContains(actual: string, expected: string, msg?: string): void`
- `assertArrayContains(actual: unknown[], expected: unknown[], msg?: string): void`
- `assertMatch(actual: string, expected: RegExp, msg?: string): void`
- `assertThrows(fn: () => void, ErrorClass?: Constructor, msgIncludes = "", msg?: string): Error`
- `assertThrowsAsync(fn: () => Promise<void>, ErrorClass?: Constructor, msgIncludes = "", msg?: string): Promise<Error>`

### アサート

`assert`メソッドは単純な「真の」アサーションであり、`true`と推定できる任意の値をアサートするために使用できます。

```js
Deno.test("Test Assert", () => {
  assert(1);
  assert("Hello");
  assert(true);
});
```

### 等価性

利用可能な等価アサーションには、`assertEquals()`、`assertNotEquals()` 、 `assertStrictEquals()`の3つがあります

`assertEquals()` と `assertNotEquals()` のメソッドは、一般的な等価チェックを提供し、プリミティブ型とオブジェクト間の等価をアサートすることができます。

```js
Deno.test("Test Assert Equals", () => {
  assertEquals(1, 1);
  assertEquals("Hello", "Hello");
  assertEquals(true, true);
  assertEquals(undefined, undefined);
  assertEquals(null, null);
  assertEquals(new Date(), new Date());
  assertEquals(new RegExp("abc"), new RegExp("abc"));

  class Foo {}
  const foo1 = new Foo();
  const foo2 = new Foo();

  assertEquals(foo1, foo2);
});

Deno.test("Test Assert Not Equals", () => {
  assertNotEquals(1, 2);
  assertNotEquals("Hello", "World");
  assertNotEquals(true, false);
  assertNotEquals(undefined, "");
  assertNotEquals(new Date(), Date.now());
  assertNotEquals(new RegExp("abc"), new RegExp("def"));
});
```

対照的に、`assertStrictEquals()`は、`===`演算子に基づいて、より単純で厳密な等価チェックを提供します。結果として、同一オブジェクトの2つのインスタンスは参照上同じではないため、アサートされません。

```js
Deno.test("Test Assert Strict Equals", () => {
  assertStrictEquals(1, 1);
  assertStrictEquals("Hello", "Hello");
  assertStrictEquals(true, true);
  assertStrictEquals(undefined, undefined);
});
```

`assertStrictEquals()`アサーションは、2つのプリミティブ型を正確にチェックする場合に最適です。

### 包含

値が値を含むことをアサートするために使用できるメソッドは、`assertStringContains()`と`assertArrayContains()`の2つです。

`assertStringContains()`アサーションは、対象の文字列に対して、単純に期待される文字列が含まれているかどうかを調べます。

```js
Deno.test("Test Assert String Contains", () => {
  assertStringContains("Hello World", "Hello");
});
```

`assertArrayContains()`アサーションは少し高度で、配列内の値と配列内の値の配列の両方を見つけることができます。

```js
Deno.test("Test Assert Array Contains", () => {
  assertArrayContains([1, 2, 3], [1]);
  assertArrayContains([1, 2, 3], [1, 2]);
  assertArrayContains(Array.from("Hello World"), Array.from("Hello"));
});
```

### Regex

`assertMatch()` を介して正規表現をアサートできます。

```js
Deno.test("Test Assert Match", () => {
  assertMatch("abcdefghi", new RegExp("def"));

  const basicUrl = new RegExp("^https?://[a-z.]+.com$");
  assertMatch("https://www.google.com", basicUrl);
  assertMatch("http://facebook.com", basicUrl);
});
```

### エラーのスロー

Denoでエラーがスローされたかどうかをアサートするには、`assertThrows()`と`assertAsyncThrows()`の2つの方法があります。どちらのアサーションでも、[Error](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Error)がスローされたこと、スローされたエラーのタイプ、メッセージが何であったかを確認できます。

2つのアサーションの違いは、`assertThrows()`が標準関数を受け入れ、`assertAsyncThrows()`が[Promise](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Promise) を返す関数を受け入れることです。

`assertThrows()`は、エラーがスローされたことを確認し、オプションで、スローされたエラーが正しいタイプであることを確認し、エラーメッセージが期待どおりであることをアサートします。

```js
Deno.test("Test Assert Throws", () => {
  assertThrows(
    () => {
      throw new Error("Panic!");
    },
    Error,
    "Panic!",
  );
});
```

`assertAsyncThrows()` は、主にPromiseを処理するため、もう少し複雑です。しかし、基本的にはPromiseでスローされたエラーや拒否をキャッチします。オプションで、エラーの種類とエラーメッセージを確認することもできます。

```js
Deno.test("Test Assert Throws Async", () => {
  assertThrowsAsync(
    () => {
      return new Promise(() => {
        throw new Error("Panic! Threw Error");
      });
    },
    Error,
    "Panic! Threw Error",
  );

  assertThrowsAsync(
    () => {
      return Promise.reject(new Error("Panic! Reject Error"));
    },
    Error,
    "Panic! Reject Error",
  );
});
```

### カスタムメッセージ

Denoの組み込みアサーションはそれぞれ、必要に応じて標準のCLIエラーメッセージを上書きできます。

例えば、この例では標準のCLIエラーメッセージではなく、"Values Don't Match!"が出力されます。

```js
Deno.test("Test Assert Equal Fail Custom Message", () => {
  assertEquals(1, 2, "Values Don't Match!");
});
```
