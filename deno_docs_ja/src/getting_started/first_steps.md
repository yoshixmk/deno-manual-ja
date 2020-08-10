## ファーストステップ
このページには、Denoの基礎について説明するいくつかの例が含まれています。 

このドキュメントは、JavaScript、特に`async`/`await`に関する予備知識があることを前提としています。 JavaScriptの予備知識がない場合は、Denoから始める前に、[JavaScript](https://developer.mozilla.org/zh-CN/docs/learn/JavaScript)の基本に関するガイドに従うことをお勧めします。

### Hello World

DenoはJavaScript / TypeScriptのランタイムであり、Web互換で、可能な限り最新の機能を使用しようとします。 ブラウザーの互換性は、Denoの`Hello World`プログラムがブラウザーで実行できるプログラムと同じであることを意味します。

```ts
console.log("Welcome to Deno 🦕");
```

プログラムを試してください：

```shell
deno run https://deno.land/std/examples/welcome.ts
```

### HTTPリクエストを行う

多くのプログラムは、HTTP要求を使用してWebサーバーからデータをフェッチします。ファイルをフェッチしてその内容を端末に出力する小さなプログラムを書いてみましょう。 ブラウザーと同様に、Web標準[`fetch`](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)APIを使用してHTTP呼び出しを行うことができます。

```typescript
const url = Deno.args[0];
const res = await fetch(url);

const body = new Uint8Array(await res.arrayBuffer());
await Deno.stdout.write(body);
```

このアプリケーションの機能を見ていきましょう。

1. 最初の引数をアプリケーションに渡して、それを`url`定数に格納します。

2. 指定されたURLにリクエストを送信し、レスポンスを待って、`res`定数に格納します。

3. 応答の本文を[`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)として解析し、応答を待ち、[`Uint8Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)に変換して`body`の定数に格納します。。

4. `body`定数の内容を`stdout`に書き込みます。

やってみよう：

```shell
deno run https://deno.land/std/examples/curl.ts https://example.com
```

このプログラムはネットワークアクセスに関するエラーを返しますが、何が問題だったのでしょうか。はじめに、Denoはデフォルトで安全なランタイムであることを覚えているかもしれません。つまり、ネットワークへのアクセスなど、特定の「特権」アクションを実行する許可をプログラムに明示的に与える必要があります。

正しい許可フラグでもう一度試してください:

```shell
deno run --allow-net=example.com https://deno.land/std/examples/curl.ts https://example.com
```

### ファイルを読み取る

Denoは、Web以外のAPIも提供しています。これらはすべて`Deno`グローバルに含まれています。これらのAPIのドキュメントは、[doc.deno.land](https://doc.deno.land/https/github.com/denoland/deno/releases/latest/download/lib.deno.d.ts)にあります。

たとえばファイルシステムAPIにはWeb標準形式がないため、Denoは独自のAPIを提供します。  

このプログラムでは、各コマンドライン引数はファイル名であると想定され、ファイルが開かれ、標準出力に出力されます。

例：[Unix cat](../examples/unix_cat.md)

{{#include ../examples/unix_cat.md:2:}}

### TCP サーバー

例：[TCP echo](../examples/tcp_echo.md)

{{#include ../examples/tcp_echo.md:2:}}

### 更なる例について

HTTPファイルサーバーなどの例は、[例](../examples.md) の章にあります。
