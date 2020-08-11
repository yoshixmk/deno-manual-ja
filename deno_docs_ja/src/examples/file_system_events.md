### ファイルシステムイベント

ファイルシステムイベントをポーリングするには：

```ts
const watcher = Deno.watchFs("/");
for await (const event of watcher) {
  console.log(">>>> event", event);
  // { kind: "create", paths: [ "/foo.txt" ] }
}
```

イベントの正確な順序は、オペレーティングシステムによって異なる場合があることに注意してください。この機能は、プラットフォームに応じて異なるsyscallを使用します：

- Linux: inotify
- macOS: FSEvents
- Windows: ReadDirectoryChangesW
