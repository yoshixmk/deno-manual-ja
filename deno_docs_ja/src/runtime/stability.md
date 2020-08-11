## 安定

Deno 1.0.0以降、`Deno`名前空間APIは安定しています。つまり、1.0.0で動作するコードが今後のバージョンでも引き続き動作するように努めます。

ただし、Denoの機能のすべてがまだプロダクションの準備ができているわけではありません。まだドラフト段階にあるため準備ができていない機能は、`--unstable`コマンドラインフラグの背後にロックされています。

```shell
deno run --unstable mod_which_uses_unstable_stuff.ts
```
このフラグを渡すと、いくつかのことが行われます。

- 実行時に不安定なAPIを使用できるようにします。
- [`lib.deno.unstable.d.ts`](https://doc.deno.land/https/raw.githubusercontent.com/denoland/deno/master/cli/dts/lib.deno.unstable.d.ts)型チェックに使用されるTypeScript定義のリストにファイルを追加します。`deno types`にはこの出力が含まれます。

多くの不安定なAPIは**セキュリティレビューを受けておらず**、将来APIに重大な変更が加えられる可能性が あり、**本稼働の準備ができていない**ことに注意してください。

### 標準モジュール

Denoの標準モジュール（[https://deno.land/std/](https://deno.land/std/)）はまだ安定していません。現在、これを反映するために、CLIとは異なる方法で標準モジュールをバージョン管理しています。`Deno`名前空間とは異なり、標準モジュールの使用には`--unstable`フラグが不要であることに注意してください（標準モジュール自体が不安定なDeno機能を使用しない限り）。
