## Unixの"cat"プログラムの実装

このプログラムでは、各コマンドライン引数はファイル名であると想定され、ファイルが開かれ、標準出力に出力されます。

```ts
const filenames = Deno.args;
for (const filename of filenames) {
  const file = await Deno.open(filename);
  await Deno.copy(file, Deno.stdout);
  file.close();
}
```

ここの`copy()`関数は、実際には必要なカーネル->ユーザースペース->カーネルのコピーしか作成しません。つまり、データがファイルから読み取られるのと同じメモリがstdoutに書き込まれます。これは、Denoの I/Oストリームの一般的な設計目標を示しています。

プログラムを試してください：

```shell
deno run --allow-read https://deno.land/std@0.65.0/examples/cat.ts /etc/passwd
```
