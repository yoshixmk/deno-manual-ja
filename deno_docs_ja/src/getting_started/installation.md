## インストール方法

DenoはmacOS、Linux、およびWindowsで動作します。 Denoは単一のバイナリ実行ファイルです。外部依存関係はありません。

### ダウンロードとインストール

[deno_install](https://github.com/denoland/deno_install)は、バイナリをダウンロードしてインストールするための便利なスクリプトを提供します。

Shellの場合 (macOS and Linux):
```shell
curl -fsSL https://deno.land/x/install/install.sh | sh
```

PowerShellの場合 (Windows):
```shell
iwr https://deno.land/x/install/install.ps1 -useb | iex
```

[Scoop](https://scoop.sh/)の場合 (Windows):
```shell
scoop install deno
```

[Chocolatey](https://chocolatey.org/packages/deno)の場合 (Windows):
```shell
choco install deno
```

[Homebrew](https://formulae.brew.sh/formula/deno)の場合 (macOS):
```shell
brew install deno
```

[Cargo](https://crates.io/crates/deno)の場合 (Windows, macOS, Linux):
```shell
cargo install deno
```

Denoのバイナリは
[github.com/denoland/deno/releases](https://github.com/denoland/deno/releases)
からZipファイルをダウンロードして、手動でインストールすることもできます。パッケージには、実行ファイルが1つだけ含まれています。macOSとLinuxは実行可能な成果物を選び設定する必要があります。

### インストールのテスト

インストールをテストするには、`deno --version`を実行します。もしDenoバージョンがコンソールに出力されれば、インストールは成功しています。

`deno help`を使用すると、Denoのオプションのフラグと利用方法を確認できます。
CLIの詳細については、[こちら](./command_line_interface.md)をご確認ください。

### アップデート

以前にインストールしたDenoのバージョンを更新するには、次のコマンドを実行します:
```shell
deno upgrade
```

これにより、[github.com/denoland/deno/releases](https://github.com/denoland/deno/releases)から、最新のリリースを取得し、解凍され、現在の実行ファイルと置き換えられます。

次のユーティリティを使用して、特定のバージョンのDenoをインストールすることもできます:
```shell
deno upgrade --version 1.3.0
```

### ソースからビルドする
ソースからビルドする方法についての情報は、`貢献`の章にあります。
