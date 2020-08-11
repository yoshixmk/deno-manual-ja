## TypeScriptの使用

<!-- TODO(lucacasonato): text on 'just import .ts' -->

Denoは、実行時にファーストクラス言語としてJavaScriptとTypeScriptの両方をサポートします。つまり、拡張子（または正しいメディアタイプを提供するサーバー）を含む完全修飾モジュール名が必要です。さらに、Denoには「魔法の」モジュール解決策がありません。代わりに、インポートされたモジュールは、ファイル（拡張子を含む）または完全修飾URLインポートとして指定されます。 Typescriptモジュールは直接インポートできます。例えば：

```ts
import { Response } from "https://deno.land/std@0.64.0/http/server.ts";
import { queue } from "./collections.ts";
```

### `--no-check` オプション

`deno run`、`deno test`、`deno cache`、`deno info`、`deno bundle`を使用する場合、`--no-check`フラグを指定してTypeScriptタイプのチェックを無効にすることができます。これにより、プログラムの起動にかかる時間を大幅に短縮できます。これは、エディターによって型チェックが提供され、起動時間をできるだけ速くしたい場合（ファイルウォッチャーでプログラムを自動的に再起動する場合など）に非常に役立ちます。

`--no-check`はTypeScriptの型チェックを行わないため、型情報が必要になるため、型のみのインポートとエクスポートを自動的に削除することはできません。この目的のために、TypeScriptは[`importタイプとexportタイプの構文`](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html#type-only-imports-and-exports)を提供します。タイプを別のファイルにエクスポートするには、`export type {AnInterface} from "./mod.ts";`を使用します。タイプをインポートするには、 `import type { AnInterface } from "./mod.ts";`を使用します。 `importsNotUsedAsValues` TypeScriptコンパイラオプションを`"error"`に設定することで、必要に応じてインポートタイプとエクスポートタイプを使用していることを確認できます。このオプションを使用した[`tsconfig_test.json`](https://github.com/denoland/deno/blob/master/std/tsconfig_test.json)の例は、標準ライブラリ内にあります。

### 外部型定義の使用

ただし、標準のTypeScriptコンパイラは、JavaScriptモジュールにタイプを適用するために、拡張機能のないモジュールとNode.jsモジュール解決ロジックの両方に依存しています。

 このギャップを埋めるために、Denoは「魔法の」解決に頼ることなくタイプ定義ファイルを参照する3つの方法をサポートしています。

#### コンパイラーのヒント

JavaScriptモジュールをインポートしていて、そのモジュールの型定義の場所がわかっている場合は、インポート時に型定義を指定できます。これはコンパイラのヒントの形をとります。コンパイラーのヒントは、`.d.ts`ファイルの場所と、それらが関連付けられているインポートされたJavaScriptコードをDenoに通知します。ヒントは`@deno-types`であり、指定すると、JavaScriptモジュールの代わりにコンパイラで値が使用されます。

たとえば、`foo.js`があったが、それに加えてファイルのタイプであるfoo.d.tsであることがわかっている場合、コードは次のようになります:

```ts
// @deno-types="./foo.d.ts"
import * as foo from "./foo.js";
```

値は、モジュールのインポートと同じ解決ロジックに従います。つまり、ファイルには拡張子が必要であり、現在のモジュールに対して相対的です。リモート指定子も使用できます。

ヒントは、`@deno-types`の値が、指定されたモジュールの代わりにコンパイル時に置換される次の`import`ステートメント（または`export ... from`ステートメント）に影響します。上記の例のように、Denoコンパイラは`./foo.js`ではなく`./foo.d.ts`をロードします。 Denoはプログラムの実行時に`./foo.js`を引き続きロードします。

#### JavaScriptファイルのトリプルスラッシュ参照ディレクティブ

Denoで使用するモジュールをホストしていて、型定義の場所についてDenoに通知する場合は、実際のコードでトリプルスラッシュディレクティブを使用できます。たとえば、JavaScriptモジュールがあり、そのファイルの横にある型定義の場所をDenoに提供する場合、`foo.js`という名前のJavaScriptモジュールは次のようになります。：

```js
/// <reference types="./foo.d.ts" />
export const foo = "foo";
```

Denoはこれを確認し、コンパイラはファイルの型チェック時に`foo.d.ts`を使用しますが、`foo.js`は実行時にロードされます。ディレクティブの値の解決は、モジュールのインポートと同じ解決ロジックに従います。

つまり、ファイルには拡張子が必要であり、現在のファイルに対して相対的です。リモート指定子も使用できます。

#### X-TypeScript-Types カスタムヘッダー

Denoによって使用されるモジュールをホストしていて、Denoに型定義の場所を通知する場合は、`X-TypeScript-Types`のカスタムHTTPヘッダーを使用して、そのファイルの場所をDenoに通知できます。

ヘッダーは、前述のトリプルスラッシュリファレンスと同じように機能します。つまり、JavaScriptファイル自体のコンテンツを変更する必要がなく、型定義の場所をサーバー自体が決定できることを意味します。

**すべての型定義がサポートされているわけではありません。**

Denoはコンパイラヒントを使用して、指定された`.d.ts`ファイルをロードしますが、一部の`.d.ts`ファイルにはサポートされていない機能が含まれています。具体的には、一部の`.d.ts`ファイルは、モジュール解決ロジックを使用して、他のパッケージからタイプ定義をロードまたは参照できると想定しています。たとえば、`node`を含めるタイプ参照ディレクティブは、。`./node_modules/@types/node/index.d.ts`のようなパスに解決されることを期待しています。これは相対的でない「魔法の」解像度に依存するため、Denoはこれを解決できません。

**TypeScriptファイルでトリプルスラッシュタイプの参照を使用しないのはなぜですか？**

TypeScriptコンパイラは、タイプ参照ディレクティブを含むトリプルスラッシュディレクティブをサポートします。 Denoがこれを使用した場合、TypeScriptコンパイラの動作に干渉します。 DenoはJavaScript（およびJSX）ファイルでディレクティブを検索するだけです。

### カスタムTypeScriptコンパイラオプション

Denoエコシステムでは、デフォルトで`厳格`であるというTypeScriptの理想に準拠するために、すべての厳格なフラグが有効になっています。ただし、カスタマイズをサポートする方法を提供するために、プログラムの実行時に`tsconfig.json`などの構成ファイルがDenoに提供される場合があります。 

アプリケーションを実行するときに`-C`（または`--config`）引数を設定して、この構成を探す場所をDenoに明示的に指示する必要があります。

```shell
deno run -c tsconfig.json mod.ts
```

以下は、現在Denoで許可されている設定とそのデフォルト値です：

```json
{
  "compilerOptions": {
    "allowJs": false,
    "allowUmdGlobalAccess": false,
    "allowUnreachableCode": false,
    "allowUnusedLabels": false,
    "alwaysStrict": true,
    "assumeChangesOnlyAffectDirectDependencies": false,
    "checkJs": false,
    "disableSizeLimit": false,
    "generateCpuProfile": "profile.cpuprofile",
    "jsx": "react",
    "jsxFactory": "React.createElement",
    "lib": [],
    "noFallthroughCasesInSwitch": false,
    "noImplicitAny": true,
    "noImplicitReturns": true,
    "noImplicitThis": true,
    "noImplicitUseStrict": false,
    "noStrictGenericChecks": false,
    "noUnusedLocals": false,
    "noUnusedParameters": false,
    "preserveConstEnums": false,
    "removeComments": false,
    "resolveJsonModule": true,
    "strict": true,
    "strictBindCallApply": true,
    "strictFunctionTypes": true,
    "strictNullChecks": true,
    "strictPropertyInitialization": true,
    "suppressExcessPropertyErrors": false,
    "suppressImplicitAnyIndexErrors": false,
    "useDefineForClassFields": false
  }
}
```

許可される値と使用例に関するドキュメントについては、[typescript docs](https://www.typescriptlang.org/docs/handbook/compiler-options.html)をご覧ください。.

**注意**：上記にリストされていないオプションは、Denoでサポートされていないか、TypeScriptドキュメントで非推奨/実験的としてリストされています。
