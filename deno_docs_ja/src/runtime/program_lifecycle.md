## ライフサイクル

Denoは、ブラウザの互換性のあるライフサイクルイベントをサポートしています`load`と`unload`。これらのイベントを使用して、プログラムにセットアップコードとクリーンアップコードを提供できます。

`load`イベントのリスナーは非同期であり、待機します。`unload`イベントのリスナーは同期している必要があります。どちらのイベントもキャンセルできません。

例：

**main.ts**

```ts
import "./imported.ts";

const handler = (e: Event): void => {
  console.log(`got ${e.type} event in event handler (main)`);
};

window.addEventListener("load", handler);

window.addEventListener("unload", handler);

window.onload = (e: Event): void => {
  console.log(`got ${e.type} event in onload function (main)`);
};

window.onunload = (e: Event): void => {
  console.log(`got ${e.type} event in onunload function (main)`);
};

console.log("log from main script");
```

**imported.ts**

```ts
const handler = (e: Event): void => {
  console.log(`got ${e.type} event in event handler (imported)`);
};

window.addEventListener("load", handler);
window.addEventListener("unload", handler);

window.onload = (e: Event): void => {
  console.log(`got ${e.type} event in onload function (imported)`);
};

window.onunload = (e: Event): void => {
  console.log(`got ${e.type} event in onunload function (imported)`);
};

console.log("log from imported script");
```

注意: `window.addEventListener`と`window.onload` / `window.onunload`の両方を使用して、イベントのハンドラーを定義できることに注意してください。それらの間には大きな違いがあります。例を実行してみましょう:

```shell
$ deno run main.ts
log from imported script
log from main script
got load event in onload function (main)
got load event in event handler (imported)
got load event in event handler (main)
got unload event in onunload function (main)
got unload event in event handler (imported)
got unload event in event handler (main)
```

`window.addEventListener`を使用して追加されたすべてのリスナーが実行されましたが、`main.ts`で定義された`window.onload`および`window.onunload`は、`imported.ts`で定義されたハンドラーをオーバーライドしました。

つまり、複数の`window.addEventListener` `"load"`または `"unload"`イベントを登録できますが、最後に読み込まれた`window.onload`または`window.onunload`イベントのみが実行されます。
