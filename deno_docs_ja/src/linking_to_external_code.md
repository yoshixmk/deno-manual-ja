# サードパーティのコードへのリンク

「[入門]((./getting_started.md))」のセクションでは、DenoがURLからスクリプトを実行できることを確認しました。ブラウザのJavaScriptと同様に、DenoはURLから直接ライブラリをインポートできます。この例では、URLを使用してアサーションライブラリをインポートします。

**test.ts**

```ts
import { assertEquals } from "https://deno.land/std@0.65.0/testing/asserts.ts";

assertEquals("hello", "hello");
assertEquals("world", "world");

console.log("Asserted! ✓");
```

これを実行してみてください：

```shell
$ deno run test.ts
Compile file:///mnt/f9/Projects/github.com/denoland/deno/docs/test.ts
Download https://deno.land/std@0.65.0/testing/asserts.ts
Download https://deno.land/std@0.65.0/fmt/colors.ts
Download https://deno.land/std@0.65.0/testing/diff.ts
Asserted! ✓
```

このプログラムに`--allow-net`フラグを提供する必要はなかったが、ネットワークにアクセスしたことに注意してください。ランタイムには、インポートをダウンロードしてディスクにキャッシュするための特別なアクセス権があります。

Denoは、`DENO_DIR`の環境変数で指定された特別なディレクトリにリモートインポートをキャッシュします。DENO_DIR が指定されていない場合、システムのキャッシュディレクトリがデフォルトになります。次にプログラムを実行するときには、ダウンロードは行われません。プログラムが変更されていなければ、再コンパイルもされません。デフォルトのディレクトリは次のとおりです。
- Linux/Redox: `$XDG_CACHE_HOME/deno` or `$HOME/.cache/deno`
- Windows: `%LOCALAPPDATA%/deno` (`%LOCALAPPDATA%` = `FOLDERID_LocalAppData`)
- macOS: `$HOME/Library/Caches/deno`
- 何かが失敗した場合、`$HOME/.deno`にフォールバックします。

## FAQ

### モジュールの特定のバージョンをインポートするにはどうすればよいですか？

URLでバージョンを指定します。例えば、次のように実行中のコードで完全URLで指定します。 `https://unpkg.com/liltest@0.0.5/dist/liltest.js`。

### どこにでもURLをインポートするのは扱いにくいと思います。

> URLの1つが微妙に異なるバージョンのライブラリにリンクしている場合はどうなりますか？  
> 大規模なプロジェクトのどこにでもURLを保持するのは間違いやすいのではないでしょうか？

解決策は、外部のライブラリを`deps.ts`ファイル（Nodeの`package.json`ファイルと同じ目的で機能する）にインポートおよび再エクスポートすることです。例えば、大規模なプロジェクト全体で上記のアサーションライブラリを使用していたとしましょう。 `"https://deno.land/std@0.65.0/testing/asserts.ts"`をどこにでもインポートするのではなく、サードパーティのコードをエクスポートする`deps.ts`ファイルを作成できます。

**deps.ts**

```ts
export {
  assert,
  assertEquals,
  assertStrContains,
} from "https://deno.land/std@0.65.0/testing/asserts.ts";
```

また、同じプロジェクト全体で、`deps.ts`からインポートして、同じURLへの多くの参照を避けることができます。

```ts
import { assertEquals, runTests, test } from "./deps.ts";
```

この設計により、パッケージ管理ソフトウェア、一元化されたコードリポジトリ、および余分なファイル形式によって生成される大量の複雑さが回避されます。

### 変更される可能性のあるURLを信頼するにはどうすればよいですか？

ロックファイル（`--lock`コマンドラインフラグを指定）を使用することで、URLから取得したコードが初期開発時のものと同じであることを確認できます。これについて詳しくは、[こちら](./linking_to_external_code/integrity_checking.md)をご覧ください。

### しかし、URLのホストがダウンした場合はどうなりますか？ソースは利用できません。

これは、上記と同様に、リモートの依存関係システムが直面する問題です。外部サーバーに依存することは、開発には便利ですが、本番環境では脆弱です。本番ソフトウェアは、常にその依存関係を提供する必要があります。 Nodeでは、これは`node_modules`をソース管理にチェックインすることで行われます。  
Denoでは、実行時に`$DENO_DIR`をプロジェクトローカルディレクトリにポイントし、同様にそれをソース管理にチェックインすることでこれを行います。

```shell
# Download the dependencies.
DENO_DIR=./deno_dir deno cache src/deps.ts

# Make sure the variable is set for any command which invokes the cache.
DENO_DIR=./deno_dir deno test src

# Check the directory into source control.
git add -u deno_dir
git commit
```
