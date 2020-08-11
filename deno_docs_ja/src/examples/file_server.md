## ファイルサーバー

これはHTTPでローカルディレクトリを提供します。

```shell
deno install --allow-net --allow-read https://deno.land/std@$STD_VERSION/http/file_server.ts
```

それを実行します：

```shell
$ file_server .
Downloading https://deno.land/std@$STD_VERSION/http/file_server.ts...
[...]
HTTP server listening on http://0.0.0.0:4500/
```

また、最新の公開バージョンにアップグレードしたい場合は、次のようにします：

```shell
file_server --reload
```
