次の表で、ASP.NET Core コード ジェネレーターのパラメーターについて詳しく説明します。

| パラメーター               | 説明|
| ----------------- | ------------ |
| -m  | モデルの名前。 |
| -dc  | データ コンテキスト。 |
| -udl | 既定のレイアウトを使用します。 |
| --relativeFolderPath | ファイルを作成するための相対出力フォルダー パス。 |
| --useDefaultLayout | ビューには既定のレイアウトを使用してください。 |
| --referenceScriptLibraries | [編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。 |

次のように、`h` スイッチを使用して、`aspnet-codegenerator controller` コマンドに関するヘルプを取得します。

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

詳細については、「[dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)」を参照してください
