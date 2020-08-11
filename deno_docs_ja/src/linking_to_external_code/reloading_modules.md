## モジュールの再読み込み

デフォルトでは、キャッシュ内のモジュールは、フェッチまたは再コンパイルせずに再利用されます。これは望ましくない場合があり、denoにモジュールを再フェッチしてキャッシュに再コンパイルさせることができます。 `deno cache`サブコマンドの`--reload`フラグを使用して、ローカルの`DENO_DIR`キャッシュを無効にすることができます。

使い方は以下の通りです:

### すべてをリロードする

```ts
deno cache --reload my_module.ts
```

### 特定のモジュールをリロードする

一部のモジュールのみをアップグレードしたい場合があります。 `--reload`フラグに引数を渡すことで制御できます。

全ての 0.64.0 標準モジュールをリロードする：

```ts
deno cache --reload=https://deno.land/std@v0.64.0 my_module.ts
```

特定のモジュール（この例では、色とファイルシステムのコピー）を再ロードするには、コンマを使用してURLを区切ります：

```ts
deno cache --reload=https://deno.land/std@0.64.0/fs/copy.ts,https://deno.land/std@0.64.0/fmt/colors.ts my_module.ts
```

<!-- Should this be part of examples? -->
