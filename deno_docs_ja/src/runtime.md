# ランタイム

すべてのランタイム関数（Web API + Denoグローバル）の ドキュメントは、[doc.deno.land](https://doc.deno.land/https/github.com/denoland/deno/releases/latest/download/lib.deno.d.ts)にあります。

## Web API

`fetch` HTTPリクエストのように、Web標準がすでに存在するAPIの場合、Denoは新しい独自のAPIを発明するのではなく、これらを使用します。

実装されたWeb APIの詳細なドキュメントは、[doc.deno.land](https://doc.deno.land/https/raw.githubusercontent.com/denoland/deno/master/cli/dts/lib.deno.shared_globals.d.ts)にあります。さらに、Denoが実装するWeb APIの完全なリストは、[リポジトリ](https://github.com/denoland/deno/blob/master/cli/rt/README.md)にもあります。

実装されたWeb APIのTypeScript定義は、
[lib.deno.shared_globals.d.ts](https://github.com/denoland/deno/blob/master/cli/dts/lib.deno.shared_globals.d.ts) および
[lib.deno.window.d.ts](https://github.com/denoland/deno/blob/master/cli/dts/lib.deno.window.d.ts)ファイルにあります。

ワーカーに固有の定義は[lib.deno.worker.d.ts](https://github.com/denoland/deno/blob/master/cli/dts/lib.deno.worker.d.ts)ファイルにあります。

## `Deno` グローバル名前空間

Web標準ではないすべてのAPIは、グローバル`Deno`名前空間に含まれています。ファイルからの読み取り、TCPソケットのオープン、サブプロセスの実行などのためのAPIを備えています。

Deno名前空間のTypeScript定義は
[lib.deno.ns.d.ts](https://github.com/denoland/deno/blob/master/cli/dts/lib.deno.ns.d.ts)ファイルにあります。

Deno固有のすべてのAPIのドキュメントは、
[doc.deno.land](https://doc.deno.land/https/raw.githubusercontent.com/denoland/deno/master/cli/dts/lib.deno.ns.d.ts)にあります。
