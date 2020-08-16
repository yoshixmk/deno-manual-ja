## パーミッションの調査と取り消し

> このプログラムは、不安定なDeno機能を利用しています。
> [不安定な機能の詳細](../runtime/stability.md)をご覧ください。

プログラムによっては、以前に付与されたアクセス許可を取り消すことが必要な場合があります。後の段階でプログラムがこれらのパーミッションを必要とする場合、プログラムは失敗します。

```ts
// lookup a permission
const status = await Deno.permissions.query({ name: "write" });
if (status.state !== "granted") {
  throw new Error("need write permission");
}

const log = await Deno.open("request.log", { write: true, append: true });

// revoke some permissions
await Deno.permissions.revoke({ name: "read" });
await Deno.permissions.revoke({ name: "write" });

// use the log file
const encoder = new TextEncoder();
await log.write(encoder.encode("hello\n"));

// this will fail.
await Deno.remove("request.log");
```
