# モジュールのインポートとエクスポート

Denoはデフォルトで、JavaScriptとTypeScriptの両方でモジュールをインポートする方法を標準化しています。 ECMAScript 6の`import/export`標準に従いますが、注意点が1つあります。ファイルタイプはインポートステートメントの最後に含める必要があります。

```js
import {
  add,
  multiply,
} from "./arithmetic.ts";
```

依存関係も直接インポートされ、パッケージ管理のオーバーヘッドはありません。ローカルモジュールは、リモートモジュールとまったく同じ方法でインポートされます。以下の例に示すように、同じ機能をローカルモジュールまたはリモートモジュールを使用して同じ方法で作成できます。

## ローカルインポート

この例では、`add`関数と`multiply`関数がローカルの`arithmetic.ts`モジュールからインポートされています。

**Command：** `deno run local.ts`

```ts
import { add, multiply } from "./arithmetic.ts";

function totalCost(outbound: number, inbound: number, tax: number): number {
  return multiply(add(outbound, inbound), tax);
}

console.log(totalCost(19, 31, 1.2));
console.log(totalCost(45, 27, 1.15));

/**
 * Output
 *
 * 60
 * 82.8
 */
```

## エクスポート

上記の例では、`add`関数と`multiply`関数はローカルに保存された算術モジュールからインポートされます。これを可能にするには、算術モジュールに保存されている関数をエクスポートする必要があります。

これを行うには、以下に示すように、関数シグネチャの先頭に`export`のキーワードを追加します。

```ts
export function add(a: number, b: number): number {
  return a + b;
}

export function multiply(a: number, b: number): number {
  return a * b;
}
```

外部モジュール内でアクセスできる必要があるすべての関数、クラス、定数、変数はエクスポートする必要があります。それらに`export`キーワードを前に付けるか、ファイルの下部にあるexportステートメントに含めます。

ECMAScriptエクスポート機能の詳細については、[MDNドキュメント](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Statements/export)をご覧ください。

## リモートインポート

上記のローカルインポートの例では、`add`および`multiply`メソッドがローカルに保存された算術モジュールからインポートされます。同じ機能は、リモートモジュールから`add`メソッドと`multiply`メソッドをインポートすることによっても作成できます。 

この場合、バージョン番号を含むRamdaモジュールが参照されます。また、JavaScriptモジュールがTypeSriptモジュールに直接インポートされることにも注意してください。Denoはこれを問題なく処理します。

**Command：** `deno run ./remote.ts`

```ts
import {
  add,
  multiply,
} from "https://x.nest.land/ramda@0.27.0/source/index.js";

function totalCost(outbound: number, inbound: number, tax: number): number {
  return multiply(add(outbound, inbound), tax);
}

console.log(totalCost(19, 31, 1.2));
console.log(totalCost(45, 27, 1.15));

/**
 * Output
 *
 * 60
 * 82.8
 */
```
