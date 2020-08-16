# テスト

Denoには、JavaScriptまたはTypeScriptコードのテストに使用できる組み込みのテストランナーがあります。

## テストを書く

テストを定義するには、テストする名前と関数を指定して`Deno.test`を呼び出す必要があります。

使用できるスタイルは2つあります：

```ts
// Simple name and function, compact form, but not configurable
Deno.test("hello world #1", () => {
  const x = 1 + 2;
  assertEquals(x, 3);
});

// Fully fledged test definition, longer form, but configurable (see below)
Deno.test({
  name: "hello world #2",
  fn: () => {
    const x = 1 + 2;
    assertEquals(x, 3);
  },
});
```


## アサーション

テストを容易にするために、[https://deno.land/std@0.65.0/testing#usage](https://deno.land/std@0.65.0/testing#usage)に有用なアサーションユーティリティがいくつかあります。

```ts
import {
  assertEquals,
  assertArrayContains,
} from "https://deno.land/std@0.65.0/testing/asserts.ts";

Deno.test("hello world", () => {
  const x = 1 + 2;
  assertEquals(x, 3);
  assertArrayContains([1, 2, 3, 4, 5, 6], [3], "Expected 3 to be in the array");
});
```

### 非同期なファンクション

promiseを返すテスト関数を渡すことで、非同期コードをテストすることもできます。このため、関数を定義するときに`async`キーワードを使用できます:

```ts
import { delay } from "https://deno.land/std@0.65.0/async/delay.ts";

Deno.test("async hello world", async () => {
  const x = 1 + 2;

  // await some async task
  await delay(100);

  if (x !== 3) {
    throw Error("x should be equal to 3");
  }
});
```

### リソースおよび非同期なopサニタイザー

Denoの特定のアクションは、リソーステーブルにリソースを作成します（[詳細はこちら]((./contributing/architecture.md))）。これらのリソースは、使い終わったら閉じてください。

各テスト定義について、テストランナーはこのテストで作成されたすべてのリソースが閉じられていることを確認します。これは、リソースの「リーク」を防ぐためです。これはすべてのテストでデフォルトで有効になっていますが、テスト定義で`sanitizeResources`のboolean値をfalseに設定することで無効にできます。

ファイルシステムとの対話などの非同期操作についても同様です。テストランナーは、テストで開始する各操作がテストの終了前に完了することを確認します。これはすべてのテストでデフォルトで有効になっていますが、テスト定義でsanitizeOpsのboolean値をfalseに設定することで無効にできます。

```ts
Deno.test({
  name: "leaky test",
  fn() {
    Deno.open("hello.txt");
  },
  sanitizeResources: false,
  sanitizeOps: false,
});
```

## テストの実行

テストを実行するには、テスト関数を含むファイルを使用して`deno test`を呼び出します。また、ファイル名を省略することもできます。その場合、現在のディレクトリ内でglobが`{*_,*.,}test.{js,mjs,ts,jsx,tsx}`に一致するすべてのテストが（再帰的に）実行されます。ディレクトリを渡すと、このglobに一致するディレクトリ内のすべてのファイルが実行されます。

```shell
# Run all tests in the current directly and all sub-directories
deno test

# Run all tests in the util directory
deno test util/

# Run just my_test.ts
deno test my_test.ts
```

`deno test`は`deno run`と同じアクセス許可モデルを使用するため、例えば、テスト中に`--allow-write`を実行してファイルシステムに書き込む必要があります。

`deno test`のすべてのランタイムオプションを表示するには、コマンドラインのhelpで参照できます。

```shell
deno help test
```

## フィルタリング

実行中のテストをフィルタリングするためのオプションがいくつかあります。

### コマンドラインでフィルタリングする

テストは、コマンドラインの`--filter`オプションを使用して、個別にまたはグループで実行できます。

フィルターフラグは、文字列またはパターンを値として受け入れます。

次のテストを想定しています。

```ts
Deno.test({ name: "my-test", fn: myTest });
Deno.test({ name: "test-1", fn: test1 });
Deno.test({ name: "test2", fn: test2 });
```

これらのテストにはすべて「test」という単語が含まれているため、このコマンドはこれらのテストをすべて実行します。

```shell
deno test --filter "test" tests/
```

反対に、次のコマンドはパターンを使用して、2番目と3番目のテストを実行します：

```shell
deno test --filter "/test-*\d/" tests/
```

_パターンを使用することをDenoに通知するには、REGEXのJavaScript構文シュガーのように、フィルターをスラッシュで囲みます。_

### テスト定義のフィルタリング

テスト自体には、フィルタリングのための2つのオプションがあります。

#### フィルタリング（特定のテストは無視）

ある種の条件に基づくテストを無視したい場合があります（例えば、Windowsでのみテストを実行したい場合など）。このために、テスト定義で`ignore`のboolean値を使用できます。 trueに設定されている場合、テストはスキップされます。

```ts
Deno.test({
  name: "do macOS feature",
  ignore: Deno.build.os !== "darwin",
  fn() {
    doMacOSFeature();
  },
});
```

#### フィルタリング（特定のテストのみを実行）

大きなテストクラス内で問題の真っ只中にいる可能性があり、そのテストのみに焦点を当て、残りは今は無視したい場合があります。このため、`only`のオプションを使用して、テストフレームワークに、trueに設定されたテストのみを実行するように指示できます。複数のテストでこのオプションを設定できます。テストの実行では各テストの成功または失敗が報告されますが、テストで`only`フラグが立てられている場合は、テスト全体が常に失敗します。これは、ほとんどすべてのテストを無効にする一時的な対策に過ぎないためです。

```ts
Deno.test({
  name: "Focus on this test only",
  only: true,
  fn() {
    testComplicatedStuff();
  },
});
```

## 失敗を早める

長時間実行するテストスイートがあり、最初の失敗時にテストスイートを停止したい場合は、スイートの実行時に`--failfast`フラグを指定できます。

```shell
deno test --failfast
```
