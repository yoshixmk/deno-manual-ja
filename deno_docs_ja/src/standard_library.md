# 標準ライブラリ

Denoは、コアチームによって監査され、Denoでの動作が保証されている一連の標準モジュールを提供します。

標準ライブラリは<https://deno.land/std/>で入手できます。

## バージョン管理と安定性

標準ライブラリはまだ安定していないため、Denoとはバージョンが異なります。最新のリリースについては、<https://deno.land/std/>または<https://deno.land/std/version.ts>。を参照してください。標準ライブラリは、Denoがリリースされるたびにリリースされます。

意図しない変更を避けるために、常に標準ライブラリの固定バージョンでインポートを使用することを強くお勧めします。例えば、いつでも変更される可能性があるコードのマスターブランチにリンクするのではなく、コンパイルエラーや予期しない動作を引き起こす可能性があります。

```typescript
// imports from master, this should be avoided
import { copy } from "https://deno.land/std/fs/copy.ts";
```

代わりに、バージョンを指定してstdライブラリを使用すればイミュータブルです：

```typescript
// imports from v0.65.0 of std, never changes
import { copy } from "https://deno.land/std@0.65.0/fs/copy.ts";
```

## トラブルシューティング

標準ライブラリで提供されているモジュールの一部は、不安定なDeno APIを使用しています。 

このようなモジュールを`--unstable` CLIフラグなしで実行しようとすると、`Deno`名前空間にいくつかのAPIが存在しないことを示唆する多くのTypeScriptエラーが発生します。

```typescript
// main.ts
import { copy } from "https://deno.land/std@0.65.0/fs/copy.ts";

copy("log.txt", "log-old.txt");
```

```shell
$ deno run --allow-read --allow-write main.ts
Compile file:///dev/deno/main.ts
Download https://deno.land/std@0.65.0/fs/copy.ts
Download https://deno.land/std@0.65.0/fs/ensure_dir.ts
Download https://deno.land/std@0.65.0/fs/_util.ts
error: TS2339 [ERROR]: Property 'utime' does not exist on type 'typeof Deno'.
    await Deno.utime(dest, statInfo.atime, statInfo.mtime);
               ~~~~~
    at https://deno.land/std@0.65.0/fs/copy.ts:90:16

TS2339 [ERROR]: Property 'utimeSync' does not exist on type 'typeof Deno'.
    Deno.utimeSync(dest, statInfo.atime, statInfo.mtime);
         ~~~~~~~~~
    at https://deno.land/std@0.65.0/fs/copy.ts:101:10
```

この問題を解決するには、`--unstable`フラグを追加する必要があります:

```shell
deno run --allow-read --allow-write --unstable main.ts
```

API生成エラーが不安定であることを確認するには、
[lib.deno.unstable.d.ts](https://github.com/denoland/deno/blob/master/cli/dts/lib.deno.unstable.d.ts)
を確認してください。

この問題は近い将来に修正される予定です。依存する特定のモジュールがフラグなしで正常にコンパイルされる場合は、フラグを省略してもかまいません。
