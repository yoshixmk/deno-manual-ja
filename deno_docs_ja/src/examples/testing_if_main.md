## 現在のファイルがメインプログラムかどうかのテスト

現在のスクリプトがプログラムへのメイン入力として実行されたかどうかをテストするには、`import.meta.main`を確認します。

```ts
if (import.meta.main) {
  console.log("main");
}
```
