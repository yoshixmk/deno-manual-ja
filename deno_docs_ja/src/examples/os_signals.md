## OSシグナル

> このプログラムは、不安定なDeno機能を利用しています。
> [不安定な機能の詳細](../runtime/stability.md)をご覧ください。

[API リファレンス](https://doc.deno.land/https/raw.githubusercontent.com/denoland/deno/master/cli/dts/lib.deno.unstable.d.ts#Deno.signal)

`Deno.signal()`を使用して、OSシグナルを処理できます。

```ts
for await (const _ of Deno.signal(Deno.Signal.SIGINT)) {
  console.log("interrupted!");
}
```

`Deno.signal()` もpromiseとして機能します。

```ts
await Deno.signal(Deno.Signal.SIGINT);
console.log("interrupted!");
```

シグナルの監視を停止したい場合は、シグナルオブジェクトの`dispose()`メソッドを使用できます。

```ts
const sig = Deno.signal(Deno.Signal.SIGINT);
setTimeout(() => {
  sig.dispose();
}, 5000);

for await (const _ of sig) {
  console.log("interrupted");
}
```

上記のfor-awaitループは、`sig.dispose()`が呼び出されると5秒後に終了します。
