## TCP echo server

これは、ポート8080で接続を受け入れ、送信したものをクライアントに返すサーバーの例です。

```ts
const listener = Deno.listen({ port: 8080 });
console.log("listening on 0.0.0.0:8080");
for await (const conn of listener) {
  Deno.copy(conn, conn);
}
```

このプログラムを起動すると、PermissionDeniedエラーがスローされます。

```shell
$ deno run https://deno.land/std@$STD_VERSION/examples/echo_server.ts
error: Uncaught PermissionDenied: network access to "0.0.0.0:8080", run again with the --allow-net flag
► $deno$/dispatch_json.ts:40:11
    at DenoError ($deno$/errors.ts:20:5)
    ...
```

セキュリティ上の理由から、Denoは明示的な許可なしにプログラムがネットワークにアクセスすることを許可していません。ネットワークへのアクセスを許可するには、コマンドラインフラグを使用します：

```shell
deno run --allow-net https://deno.land/std@$STD_VERSION/examples/echo_server.ts
```

テストするには、netcatを使用してデータを送信してみてください。

```shell
$ nc localhost 8080
hello world
hello world
```

`cat.ts`の例と同様に、ここでの`copy()`関数も不要なメモリコピーを作成しません。それはカーネルからパケットを受信し、さらに複雑にすることなく送り返します。
