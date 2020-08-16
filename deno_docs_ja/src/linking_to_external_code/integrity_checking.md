## 整合性チェックとファイルのロック

### イントロダクション

あなたのモジュールがリモートモジュール`https：//some.url/a.ts`に依存しているとしましょう。モジュールを初めてコンパイルするとき、`a.ts`が取得、コンパイル、およびキャッシュされます。これは、新しいマシンでモジュールを実行する（例えば、本番環境で）か、キャッシュを再ロードする（例えば、`deno cache --reload`を使用）まで、この方法のままです。しかし、リモートURL `https：//some.url/a.ts`のコンテンツが変更された場合はどうなりますか？これにより、ローカルモジュールとは異なる依存関係コードで運用モジュールが実行される可能性があります。これを回避するためのDenoのソリューションは、整合性チェックとロックファイルを使用することです。

### ファイルのキャッシュとロック

Denoは、小さなJSONファイルを使用して、モジュールのサブリソースの整合性を保存およびチェックできます。

`--lock=lock.json`を使用して、ロックファイルのチェックを有効にして指定します。ロックを更新または作成するには、`--lock=lock.json --lock-write`を使用します。`--lock=lock.json`はDenoに使用するロックファイルを教えますが、`--lock-write`は依存関係ハッシュをロックファイルに出力するために使用されます（`--lock-write`は`--lock`と組み合わせて使用​​する必要があります） ）。 

`lock.json`は次のようになり、依存関係に対するファイルのハッシュを保存します。

```json
{
  "https://deno.land/std@v0.65.0/textproto/mod.ts": "3118d7a42c03c242c5a49c2ad91c8396110e14acca1324e7aaefd31a999b71a4",
  "https://deno.land/std@v0.65.0/io/util.ts": "ae133d310a0fdcf298cea7bc09a599c49acb616d34e148e263bcb02976f80dee",
  "https://deno.land/std@v0.65.0/async/delay.ts": "35957d585a6e3dd87706858fb1d6b551cb278271b03f52c5a2cb70e65e00c26a",
   ...
}
```

一般的なワークフローは次のようになります。：

**src/deps.ts**
```ts
// Add a new dependency to "src/deps.ts", used somewhere else.
export { xyz } from "https://unpkg.com/xyz-lib@v0.9.0/lib.ts";
```
それから:
```shell
# # Create/update the lock file "lock.json".
deno cache --lock=lock.json --lock-write src/deps.ts

# Include it when committing to source control.
git add -u lock.json
git commit -m "feat: Add support for xyz using xyz-lib"
git push
```

別のマシンのコラボレーター-新しくクローンされたプロジェクトツリー内：

```shell
# Download the project's dependencies into the machine's cache, integrity
# checking each resource.
deno cache -reload --lock=lock.json src/deps.ts

# Done! You can proceed safely.
deno test --allow-read src
```

### ランタイム検証

上記のキャッシュと同様に、`deno run`サブコマンドの使用中に`--lock=lock.json`オプションを使用して、実行中にロックされたモジュールの整合性を検証することもできます。これは、以前に`lock.json`ファイルに追加された依存関係に対してのみ検証されることに注意してください。新しい依存関係はキャッシュされますが検証されません。

`--cached-only`フラグを使用してリモート依存関係がすでにキャッシュされていることを要求することで、これをさらに一歩進めることができます。

```shell
deno run --lock=lock.json --cached-only mod.ts
```

まだキャッシュされていないmod.tsの依存関係ツリーに依存関係がある場合、これは失敗します。

<!-- TODO - Add detail on dynamic imports -->
