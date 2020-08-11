## サブプロセスを実行する

[API リファレンス](https://doc.deno.land/https/github.com/denoland/deno/releases/latest/download/lib.deno.d.ts#Deno.run)

例：

```ts
// create subprocess
const p = Deno.run({
  cmd: ["echo", "hello"],
});

// await its completion
await p.status();
```

実行:

```shell
$ deno run --allow-run ./subprocess_simple.ts
hello
```

ここでは、`window.onload`に関数が割り当てられています。この関数は、メインスクリプトが読み込まれた後に呼び出されます。これはブラウザの[onload](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onload)と同じであり、メインのエントリポイントとして使用できます。

デフォルトでは、`Deno.run()`を使用すると、サブプロセスは親プロセスのstdin、stdout、およびstderrを継承します。開始されたサブプロセスと通信したい場合は、`"piped"`オプションを使用できます。

```ts
const fileNames = Deno.args;

const p = Deno.run({
  cmd: [
    "deno",
    "run",
    "--allow-read",
    "https://deno.land/std@$STD_VERSION/examples/cat.ts",
    ...fileNames,
  ],
  stdout: "piped",
  stderr: "piped",
});

const { code } = await p.status();

if (code === 0) {
  const rawOutput = await p.output();
  await Deno.stdout.write(rawOutput);
} else {
  const rawError = await p.stderrOutput();
  const errorString = new TextDecoder().decode(rawError);
  console.log(errorString);
}

Deno.exit(code);
```

実行:

```shell
$ deno run --allow-run ./subprocess.ts <somefile>
[file content]

$ deno run --allow-run ./subprocess.ts non_existent_file.md

Uncaught NotFound: No such file or directory (os error 2)
    at DenoError (deno/js/errors.ts:22:5)
    at maybeError (deno/js/errors.ts:41:12)
    at handleAsyncMsgFromRust (deno/js/dispatch.ts:27:17)
```
