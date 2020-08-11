# 依存関係の管理

Denoでは、外部モジュールがローカルモジュールに直接インポートされるため、パッケージマネージャーの概念はありません。これは、パッケージマネージャーなしでリモートの依存関係を管理する方法の問題を提起します。依存関係の多い大きなプロジェクトでは、モジュールを個別のモジュールに個別にインポートすると、モジュールを更新するのが面倒で時間がかかります。

Denoでこの問題を解決するための標準的な方法は、`deps.ts`ファイルを作成することです。必要なすべてのリモート依存関係がこのファイルで参照され、必要なメソッドとクラスが再エクスポートされます。依存するローカルモジュールは、リモートの依存関係ではなく`deps.ts`を参照します。

これにより、大規模なコードベース全体のモジュールを簡単に更新できるようになり、「パッケージマネージャーの問題」があったとしても、それが解決されます。開発の依存関係は、別の`dev_deps.ts`ファイルで管理することもできます。

**deps.ts 例**

```ts
/**
 * deps.ts re-exports the required methods from the remote Ramda module.
 **/
export {
  add,
  multiply,
} from "https://x.nest.land/ramda@0.27.0/source/index.js";
```

この例では、[ローカルおよびリモートのインポート例](./import_export.md)と同じ機能が作成されます。ただし、この場合、Ramdaモジュールが直接参照される代わりに、ローカルの`deps.ts`モジュールを使用してプロキシによって参照されます。

**Command：** `deno run dependencies.ts`

```ts
import {
  add,
  multiply,
} from "./deps.ts";

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
