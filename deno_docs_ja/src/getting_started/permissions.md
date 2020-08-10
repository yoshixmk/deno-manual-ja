## パーミッション

Denoはデフォルトで安全です。したがって、特に有効にしない限り、denoモジュールには、ファイル、ネットワーク、環境などのアクセス権がありません。セキュリティ上重要な領域や機能にアクセスするには、コマンドラインでdenoプロセスに許可を与える必要があります。

次の例では、`mod.ts`にファイルシステムへの読み取り専用アクセスが許可されています。それに書き込むことも、その他の機密性の高い機能を実行することもできません。

```shell
deno run --allow-read mod.ts
```

### パーミッションのリスト

次のパーミッションを使用できます：

- **-A, --allow-all** すべての権限を許可します。これにより、すべてのセキュリティが無効になります。
- **--allow-env** 環境変数の取得や設定などの環境アクセスを許可します。
- **--allow-hrtime** 高分解能の時間測定を可能にします。高解像度の時間は、タイミング攻撃やフィンガープリントに使用できます。
- **--allow-net=\<allow-net\>** ネットワークアクセスを許可します。オプションのドメインのコンマ区切りリストを指定して、許可されたドメインの許可リストを提供できます。
- **--allow-plugin** プラグインの読み込みを許可します。 --allow-pluginは不安定な機能であることに注意してください。
- **--allow-read=\<allow-read\>** ファイルシステムの読み取りアクセスを許可します。オプションのコンマ区切りのディレクトリまたはファイルのリストを指定して、許可されたファイルシステムアクセスの許可リストを提供できます。
- **--allow-run** サブプロセスの実行を許可します。サブプロセスはサンドボックスで実行されないため、denoプロセスと同じセキュリティ制限がないことに注意してください。したがって、注意して使用してください。
- **--allow-write=\<allow-write\>** ファイルシステムの書き込みアクセスを許可します。オプションのコンマ区切りのディレクトリまたはファイルのリストを指定して、許可されたファイルシステムアクセスの許可リストを提供できます。

### パーミッション許可リスト

Denoでは、許可リストを使用して一部の権限の細かさを制御することもできます。

この例では、`/usr`ディレクトリのみを許可リストすることによってファイルシステムアクセスを制限していますが、プロセスが`/etc`ディレクトリ内のファイルにアクセスしようとしたため、実行は失敗します。

```shell
$ deno run --allow-read=/usr https://deno.land/std/examples/cat.ts /etc/passwd
error: Uncaught PermissionDenied: read access to "/etc/passwd", run again with the --allow-read flag
► $deno$/dispatch_json.ts:40:11
    at DenoError ($deno$/errors.ts:20:5)
    ...
```

代わりに`/etc`を許可リストにして、正しい権限でもう一度試してください:

```shell
deno run --allow-read=/etc https://deno.land/std/examples/cat.ts /etc/passwd
```

`--allow-write` は`--allow-read`と同じように機能します

### ネットワークアクセス

_fetch.ts_:

```ts
const result = await fetch("https://deno.land/");
```

これは、ホスト/ URLを許可するリストの例です：

```shell
deno run --allow-net=github.com,deno.land fetch.ts
```

もし`fetch.ts`が他のドメインへのネットワーク接続を確立しようとすると、プロセスは失敗します。  

すべてのホスト/ URLへの呼び出しを許可：

```shell
deno run --allow-net fetch.ts
```
