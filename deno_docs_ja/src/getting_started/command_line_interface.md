## コマンドラインインターフェース(CLI)

Denoはコマンドラインプログラムです。これまでの例に従ってきたいくつかの簡単なコマンドに精通し、シェルの使用法の基本をすでに理解している必要があります。  

メインのヘルプテキストを表示する方法はいくつかあります。

```shell
# Using the subcommand.
deno help

# Using the short flag -- outputs the same as above.
deno -h

# Using the long flag -- outputs more detailed help text where available.
deno --help
```

DenoのCLIはサブコマンドベースです。上記のコマンドは、`deno bundle`など、サポートされているもののリストを表示します。 bundleのサブコマンド固有のヘルプを表示するには、同様に次のいずれかを実行できます。

```shell
deno help bundle
deno bundle -h
deno bundle --help
```

各サブコマンドの詳細なガイドは[这里](../tools.md)にあります。

### スクリプト

Denoは、複数のソース、ファイル名、URL、および「-」からスクリプトを取得して、stdinからファイルを読み取ることができます。最後は他のアプリケーションとの統合に役立ちます。

```shell
deno run main.ts
deno run https://mydomain.com/main.ts
cat main.ts | deno run -
```

### スクリプト引数

Denoランタイムフラグとは別に、スクリプト名の後にユーザースペース引数を指定して、実行中のスクリプトに渡すことができます。

```shell
deno run main.ts a b -c --quiet
```

```ts
// main.ts
console.log(Deno.args); // [ "a", "b", "-c", "--quiet" ]
```

**スクリプト名の後に渡されたものはすべてスクリプト引数として渡され、Denoランタイムフラグとしては消費されないことに注意してください。**

これは、次の落とし穴につながります:

```shell
# Good. We grant net permission to net_client.ts.
deno run --allow-net net_client.ts

# Bad! --allow-net was passed to Deno.args, throws a net permission error.
deno run net_client.ts --allow-net
```

一部の人は、それを型破りなものと感じるかもしれません：

> 非定位置フラグは、その位置に応じて異なる方法で解析されます。

しかしながら：

1. これは、ランタイムフラグとスクリプト引数を区別する最も論理的な方法です。
2. これは、ランタイムフラグとスクリプト引数を区別する最も人間工学的な方法です。
3. これは実際には、他の一般的なランタイムと同じ動作です。
5. 实际上，这和其他流行的运行时具有相同的行为。
    -  `node -c index.js` と `node index.js -c`を試してみます。すると1つ目は、ノードの`-c`フラグに従って、`index.js`の構文チェックのみを実行します。2番目は、`require("process").argv`に`-c`を渡して`index.js`を実行します。

---

関連するサブコマンド間で共有されるフラグの論理グループが存在します。これらについては以下で説明します。

### 整合性フラグ

リソースをキャッシュにダウンロードできるコマンドに影響を与えます：`deno cache`、`deno run`、`deno test`。

```
--lock <FILE>    Check the specified lock file
--lock-write     Write lock file. Use with --lock.
```

これらの詳細については、[这里](../linking_to_external_code/integrity_checking.md)をご覧ください。

### キャッシュとコンパイルのフラグ

キャッシュにデータを入力できるコマンドに影響を与えます：`deno cache`、`deno run`、`deno test`。上記のフラグに加えて、これにはモジュールの解決、コンパイル構成などに影響するフラグが含まれます。

```
--config <FILE>               Load tsconfig.json configuration file
--importmap <FILE>            UNSTABLE: Load import map file
--no-remote                   Do not resolve remote modules
--reload=<CACHE_BLOCKLIST>    Reload source code cache (recompile TypeScript)
--unstable                    Enable unstable APIs
```

### ランタイムフラグ

ユーザーコードを実行するコマンドに影響を与えます：`deno run`および`deno test`。これらには、上記のすべてと次のものが含まれます。

#### パーミッションフラグ

これらは[こちら](./permissions.md)にリストされています。

#### その他のランタイムフラグ

実行環境に影響を与えるその他のフラグ：

```
--cached-only                Require that remote dependencies are already cached
--inspect=<HOST:PORT>        activate inspector on host:port ...
--inspect-brk=<HOST:PORT>    activate inspector on host:port and break at ...
--seed <NUMBER>              Seed Math.random()
--v8-flags=<v8-flags>        Set V8 command line options. For help: ...
```
