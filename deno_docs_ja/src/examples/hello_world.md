# Hello World

Denoは、JavaScriptとTypeScriptの両方の安全なランタイムです。以下のhello worldの例は、同じ機能をJavaScriptまたはTypeScriptで作成できることを示しているので、Denoは両方を実行します。

## JavaScript

このJavaScriptの例では、メッセージ`Hello [name]`がコンソールに出力され、コードにより、提供された名前が大文字で使用されることが保証されます。

**Command：** `deno run hello-world.js`

```js
function capitalize(word) {
  return word.charAt(0).toUpperCase() + word.slice(1);
}

function hello(name) {
  return "Hello " + capitalize(name);
}

console.log(hello("john"));
console.log(hello("Sarah"));
console.log(hello("kai"));

/**
 * Output:
 *
 * Hello John
 * Hello Sarah
 * Hello Kai
**/
```

## TypeScript

このTypeScriptの例は、上記のJavaScriptの例とまったく同じです。コードには、TypeScriptがサポートする追加の型情報が含まれています。

`deno run`コマンドを使うのはまったく同じです。`*.js`ファイルではなく`*.ts`ファイルを参照するだけです。

**Command：** `deno run hello-world.ts`

```ts
function capitalize(word: string): string {
  return word.charAt(0).toUpperCase() + word.slice(1);
}

function hello(name: string): string {
  return "Hello " + capitalize(name);
}

console.log(hello("john"));
console.log(hello("Sarah"));
console.log(hello("kai"));

/**
 * Output:
 *
 * Hello John
 * Hello Sarah
 * Hello Kai
**/
```
