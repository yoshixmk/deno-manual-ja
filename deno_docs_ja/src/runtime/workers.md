## Workers

Denoは[Web Worker API](https://developer.mozilla.org/ja/docs/Web/API/Worker/Worker)をサポートしています。

Workerを使用して、複数のスレッドでコードを実行できます。 `Worker`の各インスタンスは、そのWorker専用の個別のスレッドで実行されます。

現在、Denoは`モジュール`タイプのWorkerのみをサポートしています。したがって、新しいWorkerを作成するときは、`type： "module"`オプションを渡すことが不可欠です。

 現在のところ、相対モジュール指定子は[サポートされていません](https://github.com/denoland/deno/issues/5216)。代わりに、`URL`コンストラクターと`import.meta.url`を使用して、近くにあるスクリプトの指定子を簡単に作成できます。

```ts
// Good
new Worker(new URL("worker.js", import.meta.url).href, { type: "module" });

// Bad
new Worker(new URL("worker.js", import.meta.url).href);
new Worker(new URL("worker.js", import.meta.url).href, { type: "classic" });
new Worker("./worker.js", { type: "module" });
```

### パーミッション

新しい`Worker`インスタンスの作成は、動的インポートに似ています。したがって、Denoはこのアクションに適切な許可を必要とします。

ローカルモジュールを使用するWorker向け。 `--allow-read`パーミッションが必要です。

**main.ts**

```ts
new Worker(new URL("worker.ts", import.meta.url).href, { type: "module" });
```

**worker.ts**

```ts
console.log("hello world");
self.close();
```

```shell
$ deno run main.ts
error: Uncaught PermissionDenied: read access to "./worker.ts", run again with the --allow-read flag

$ deno run --allow-read main.ts
hello world
```

リモートモジュールを使用するWorker向け。`--allow-net`パーミッションが必要です:

**main.ts**

```ts
new Worker("https://example.com/worker.ts", { type: "module" });
```

**worker.ts**

```ts
console.log("hello world");
self.close();
```

```shell
$ deno run main.ts
error: Uncaught PermissionDenied: net access to "https://example.com/worker.ts", run again with the --allow-net flag

$ deno run --allow-net main.ts
hello world
```

### WorkerでのDenoの使用

> これは不安定なDeno機能です。[不安定な機能の詳細](stability.md)をご覧ください。

デフォルトでは、`Deno`名前空間はWorkerスコープでは使用できません。 

新しいWorkerを作成するときに`Deno`名前空間に渡すには`deno：true`オプションを追加します：

**main.js**

```ts
const worker = new Worker(new URL("worker.js", import.meta.url).href, {
  type: "module",
  deno: true,
});
worker.postMessage({ filename: "./log.txt" });
```

**worker.js**

```ts
self.onmessage = async (e) => {
  const { filename } = e.data;
  const text = await Deno.readTextFile(filename);
  console.log(text);
  self.close();
};
```

**log.txt**

```
hello world
```

```shell
$ deno run --allow-read --unstable main.js
hello world
```
`Deno`名前空間がWorkerスコープで使用可能な場合、Workerは親プロセスのパーミッション（`--allow-*`フラグを使用して指定されたもの）を継承します。

パーミッションをWorkerに対して構成可能にする予定です。
