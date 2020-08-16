## Import maps

> これは不安定な機能です。[不安定な機能の詳細](../runtime/stability.md)をご覧ください。

Denoは[import maps](https://github.com/WICG/import-maps)をサポートしています。
`--importmap=<FILE>`のCLIフラグでimport mapsを使用できます。

 現在の制限：
- 単一のインポートマップ
- フォールバックURLなし
- Deno`std:`名前空間をサポートしていません
- `file:`、`http:`、`https:` スキームのみをサポート

例：

**import_map.json**

```js
{
   "imports": {
      "fmt/": "https://deno.land/std@0.65.0/fmt/"
   }
}
```

**color.ts**

```ts
import { red } from "fmt/colors.ts";

console.log(red("hello world"));
```

それから：

```shell
$ deno run --importmap=import_map.json --unstable color.ts
```

ディレクトリを絶対パスでインポートを使用するには：

```json
// import_map.json

{
  "imports": {
    "/": "./"
  }
}
```

```ts
// main.ts

import { MyUtil } from "/util.ts";
```

別のディレクトリをマッピングすることができます（例：src）：

```json
// import_map.json

{
  "imports": {
    "/": "./src"
  }
}
```
