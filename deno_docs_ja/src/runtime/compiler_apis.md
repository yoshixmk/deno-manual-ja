## コンパイラAPI

> これは不安定なDeno機能です。[不安定な機能の詳細](stability.md)をご覧ください。

Denoは、組み込みのTypeScriptコンパイラへのランタイムアクセスをサポートしています。 `Deno`名前空間には、このアクセスを提供する3つのメソッドがあります。

### `Deno.compile()`

これは`deno cache`と同様に機能し、コードのフェッチ、キャッシュ、コンパイルはできますが、実行はできません。これは、最大3つの引数で、`rootName`と、オプションで`sources`、`options`を取ります。 `rootName`は、結果のプログラムを生成するために使用されるルートモジュールです。これは、`deno run --reload example.ts`のコマンドラインで渡すモジュール名に似ています。`sources`はハッシュであり、キーは完全修飾モジュール名であり、値はモジュールのテキストソースです。`sources`が渡された場合、Denoはそのハッシュ内からすべてのモジュールを解決し、Denoの外部では解決を試みません。ソースが提供されない場合、Denoは、ルートモジュールがコマンドラインで渡されたかのようにモジュールを解決します。 Denoはこれらのリソースもキャッシュします。解決されたすべてのリソースは動的インポートとして扱われ、ローカルかリモートかに応じて読み取りまたはネットへのパーミッションが必要です。 `options`引数は、`Deno.CompilerOptions`タイプのオプションのセットです。これは、Denoでサポートされているオプションを含むTypeScriptコンパイラオプションのサブセットです。

メソッドはタプルで解決されます。最初の引数には、コードに関連するすべての診断（構文または型エラー）が含まれます。 2番目の引数は、キーが出力ファイル名で値がコンテンツであるマップです。

ソースを提供する例：

```ts
const [diagnostics, emitMap] = await Deno.compile("/foo.ts", {
  "/foo.ts": `import * as bar from "./bar.ts";\nconsole.log(bar);\n`,
  "/bar.ts": `export const bar = "bar";\n`,
});

assert(diagnostics == null); // ensuring no diagnostics are returned
console.log(emitMap);
```

mapには、`/foo.js.map`、`/foo.js`、`/bar.js.map`、`/bar.js`という名前の4つの「ファイル」が含まれていると予想されます。

リソースを提供しない場合は、コマンドラインで行うのと同じように、ローカルモジュールまたはリモートモジュールを使用できます。だからあなたはこのようなことをすることができます：

```ts
const [diagnostics, emitMap] = await Deno.compile(
  "https://deno.land/std@0.65.0/examples/welcome.ts",
);
```

この場合、`emitMap`には`console.log()`ステートメントが含まれます。

### `Deno.bundle()`

これは、コマンドラインで`deno bundle`が行うのとよく似ています。これも`Deno.compile()`に似ていますが、ファイルのマップを返す代わりに、提供または解決されたすべてのコードとエクスポートを含む自己完結型のJavaScript ESモジュールである単一の文字列を返します。提供されたルートモジュールのすべてのエクスポートのこれは、最大3つの引数で、`rootName`、オプションで`sources`、`options`を取ります。 `rootName`は、結果のプログラムを生成するために使用されるルートモジュールです。これは、`deno bundle example.ts`のコマンドラインで渡すモジュール名に似ています。`sources`はハッシュで、キーは完全修飾モジュール名であり、値はモジュールのテキストソースです。`sources`が渡された場合、Denoはそのハッシュ内からすべてのモジュールを解決し、Denoの外部では解決を試みません。`sources`が提供されない場合、Denoは、ルートモジュールがコマンドラインで渡されたかのようにモジュールを解決します。解決されたすべてのリソースは動的インポートとして扱われ、ローカルかリモートかに応じて読み取りまたはネットへのパーミッションが必要です。 Denoはこれらのリソースもキャッシュします。 `options`引数は、`Deno.CompilerOptions`タイプのオプションのセットです。これは、Denoでサポートされているオプションを含むTypeScriptコンパイラオプションのサブセットです。

ソースを提供する例:

```ts
const [diagnostics, emit] = await Deno.bundle("/foo.ts", {
  "/foo.ts": `import * as bar from "./bar.ts";\nconsole.log(bar);\n`,
  "/bar.ts": `export const bar = "bar";\n`,
});

assert(diagnostics == null); // ensuring no diagnostics are returned
console.log(emit);
```

両方のモジュールの出力ソースを含め、`emit`がESモジュールのテキストであることを期待します。

リソースを提供しない場合は、コマンドラインで行うのと同じように、ローカルモジュールまたはリモートモジュールを使用できます。だからあなたはこのようなことをすることができます:

```ts
const [diagnostics, emit] = await Deno.bundle(
  "https://deno.land/std@0.65.0/http/server.ts",
);
```

この場合、`emit`は、すべての依存関係が解決され、ソースモジュールと同じエクスポートを出力する、自身を含むJavaScript ESモジュールになります。

### `Deno.transpileOnly()`

これは、TypeScript関数`transpileModule()`に基づいています。これが行うことは、モジュールからすべての型を「消去」し、JavaScriptを出力します。型チェックや依存関係の解決はありません。最大2つの引数を受け入れます。最初の引数は、キーがモジュール名で値がコンテンツであるハッシュです。モジュール名の唯一の目的は、ソースファイル名とは何か、ソースマップに情報を入れるときです。 2番目の引数には、`Deno.CompilerOptions`型の`options`オプションが含まれます。関数はmapで解決されます。キーは指定されたソースモジュール名であり、値は`source`と必要に応じて`map`のプロパティを持つオブジェクトです。 1つ目は、モジュールの出力内容です。 `map`プロパティはソースマップです。ソースマップはデフォルトで提供されますが、`options`引数を介してオフにすることができます。

例：

```ts
const result = await Deno.transpileOnly({
  "/foo.ts": `enum Foo { Foo, Bar, Baz };\n`,
});

console.log(result["/foo.ts"].source);
console.log(result["/foo.ts"].map);
```

`enum`は、列挙可能な構成のIIFEに書き換えられ、マップが定義されることを期待します。

### TypeScriptライブラリファイルの参照

`deno run`を使用する時、またはTypeScriptを型チェックする他のDenoコマンドを使用した時、そのコードはDenoがサポートする環境を記述するカスタムライブラリに対して評価されます。デフォルトでは、TypeScriptを型チェックするコンパイラランタイムAPIもこれらのライブラリ（`Deno.compile()`および`Deno.bundle()`）を使用します。

ただし、他のランタイム用にTypeScriptをコンパイルまたはバンドルする場合は、デフォルトのライブラリをオーバーライドすることができます。これを行うために、ランタイムAPIはコンパイラオプションの`lib`プロパティをサポートしています。例えば、ブラウザを宛先とするTypeScriptコードがある場合、TypeScriptの`"dom"`ライブラリを使用します。:

```ts
const [errors, emitted] = await Deno.compile(
  "main.ts",
  {
    "main.ts": `document.getElementById("foo");\n`,
  },
  {
    lib: ["dom", "esnext"],
  },
);
```

TypeScriptがサポートするすべてのライブラリのリストについては、[`lib` コンパイラオプション](https://www.typescriptlang.org/docs/handbook/compiler-options.html)のドキュメントを参照してください。

**JavaScriptライブラリを含めることを忘れないでください**

`tsc`と同様に、`lib`コンパイラオプションを指定すると、デフォルトのオプションが上書きされるため、基本的なJavaScriptライブラリは、対象のランタイムを最もよく表すものを含める必要があります（例えば、`es5`，`es2015`，`es2016`，`es2017`，`es2018`，`es2019`，`es2020`, `esnext`）。

#### Deno名前空間を含める

TypeScriptによって提供されるライブラリーに加えて、参照可能なDenoに組み込まれている4つのライブラリーがあります:

- `deno.ns` - `Deno`の名前空間を提供します
- `deno.shared_globals` - Denoが使用するグローバルインターフェイスと変数を提供します
- `deno.window` - グローバル変数とDeno名前空間を公開します。
- `deno.worker` - Denoの下のワーカーで使用可能なグローバル変数を公開します。

したがって、Deno名前空間をコンパイルに追加するには、`deno.ns` libを配列に含めます。例えば：

```ts
const [errors, emitted] = await Deno.compile(
  "main.ts",
  {
    "main.ts": `document.getElementById("foo");\n`,
  },
  {
    lib: ["dom", "esnext", "deno.ns"],
  },
);
```

**注意** Deno名前空間は、少なくともES2018以降のランタイム環境を想定していること。つまり、ES2018より「低い」libを使用すると、コンパイルの一部としてエラーがログに記録されます。

#### トリプルスラッシュリファレンスの使用

コンパイラオプションで`lib`を指定する必要はありません。 Denoは、[libへのトリプルスラッシュ参照](https://www.typescriptlang.org/docs/handbook/triple-slash-directives.html#-reference-lib-)もサポートしています。ファイルのコンテンツに埋め込むことができます。例えば、次のような`main.ts`があるとします。

```ts
/// <reference lib="dom" />

document.getElementById("foo");
```

次のようなエラーなしでコンパイルされます:

```ts
const [errors, emitted] = await Deno.compile("./main.ts", undefined, {
  lib: ["esnext"],
});
```

**注意** `dom`ライブラリは、Denoのデフォルトタイプライブラリで定義されているデフォルトグローバルの一部と競合することに注意してください。これを回避するには、ランタイムコンパイラAPIのコンパイラオプションで`lib`オプションを指定する必要があります。
