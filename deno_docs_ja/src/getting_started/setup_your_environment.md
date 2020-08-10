## 環境セットアップ

Denoを生産的に使用するには、環境をセットアップする必要があります。つまり、シェルのオートコンプリート、環境変数、選択したエディターまたはIDEを設定します。

### 環境変数

Denoの動作を制御するいくつかの環境変数があります。：

`DENO_DIR`のデフォルトは `$HOME/.cache/deno`で、任意のパスに設定できますが、生成およびキャッシュされたソースコードの書き込みと読み取りを制御します。  

もし`NO_COLOR`を設定すれば、カラー出力がオフになります。 <https://no-color.org/>を参照してください。`NO_COLOR`を使用することで`--allow-env`を使うことなく、ソースコードでboolean定数の`Deno.noColor`を使用して、設定されているかどうかをテストできます。

### シェルのオートコンプリート

`deno completions <shell>`を使用して、 シェルのオートコンプリート用のスクリプトを生成できます 。コマンドはstdoutに出力するため、適切なファイルにリダイレクトする必要があります。

Deno がサポートしているシェルは次の通り：

- zsh
- bash
- fish
- powershell
- elvish

例 (bash)：

```shell
deno completions bash > /usr/local/etc/bash_completion.d/deno.bash
source /usr/local/etc/bash_completion.d/deno.bash
```

例 (zsh):

```shell
mkdir ~/.oh-my-zsh/custom/plugins/deno
deno completions zsh > ~/.oh-my-zsh/custom/plugins/deno/_deno
```

在此之后，在 `~/.zshrc` 文件中的 plugins 标签下增加 `deno` 插件。

### エディターとIDE

Denoはモジュールのインポートにファイル拡張子を使用する必要があり、さらにはhttpのインポートを許可が必要で、現在ほとんどのエディターと言語サーバーはこれをネイティブでサポートしていないため、多くのエディターは不要なファイル拡張子を持つファイルまたはインポートを見つけられないというエラーをスローします。

コミュニティは、一部の編集者がこれらの問題を解決するための拡張機能を開発しています:

#### VS Code

ベータバージョンの [vscode_deno](https://github.com/denoland/vscode_deno)が[Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=denoland.vscode-deno)に公開されました。問題があればIssuesをください。

#### JetBrains IDE

JetBrains IDEのサポートは、[the Deno plugin](https://plugins.jetbrains.com/plugin/14382-deno)を通じて利用できます。

JetBrains IDEをDeno用に設定する方法の詳細については、YouTrackの[このコメント](https://youtrack.jetbrains.com/issue/WEB-41607#focus=streamItem-27-4160152.0-0)を参照してください。

### Vim と NeoVim

もし[CoC](https://github.com/neoclide/coc.nvim)（intellisense engine and language server protocol）をインストールすれば、Vim は Deno/TypeScript に対してかなりうまく機能します。

当安装完 CoC 后，可以在 Vim 内部运行 `:CocInstall coc-deno`。你会发现，诸如 `gd`（转到定义）和 `gr`（转到/查找引用）之类的东西可以正常工作了。

CoCをインストールした後、Vim内からおよび`：CocInstall coc-deno`と`:CocInstall coc-deno`を実行します。 Denoタイプ定義でオートコンプリートを機能させるには、`：CocCommand deno.types`を実行します。必要に応じて、`：CocRestart`でCoCサーバーを再起動します。これからは、`gd`（定義に移動）や`gr`（goto / find参照）などが機能するようになります。

#### Emacs


Emacsは、Emacs内でTypeScriptを使用する標準的な方法である[tide](https://github.com/ananthakumaran/tide)と、Denoの[公式VSCode拡張機能](https://github.com/denoland/vscode_deno)で使用される[typescript-deno-plugin](https://github.com/justjavac/typescript-deno-plugin)の組み合わせを使用することにより、DenoをターゲットとするTypeScriptプロジェクトでかなりうまく機能します。

これを使用するには、まずEmacsのインスタンスに`tide`が設定されていることを確認してください。次に、 [typescript-deno-plugin](https://github.com/justjavac/typescript-deno-plugin) ページで指示されているように、最初に `npm install --save-dev typescript-deno-plugin typescript` (`npm init -y`が必須)を行い、次に下記のブロックを`tsconfig.json`に追加すれば準備完了です！

```json
{
  "compilerOptions": {
    "plugins": [
      {
        "name": "typescript-deno-plugin",
        "enable": true, // default is `true`
        "importmap": "import_map.json"
      }
    ]
  }
}
```

このリストにお気に入りのIDEがない場合は、拡張機能を開発するかもしれません。私たちの[コミュニティのDiscordグループ](https://discord.gg/deno)で、どこから始めればよいかについて、いくつか指針を差し上げることができます。
