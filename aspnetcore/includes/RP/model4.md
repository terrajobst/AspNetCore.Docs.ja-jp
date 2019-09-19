<a name="codegenerator"></a> 次の表で、ASP.NET Core コード ジェネレーターのパラメーターについて詳しく説明します。

| パラメーター               | 説明|
| ----------------- | ------------ |
| -m  | モデルの名前。 |
| -dc  | 使用する `DbContext` クラス。 |
| -udl | 既定のレイアウトを使用します。 |
| -outDir | ビューを作成するための相対出力フォルダー パス。 |
| --referenceScriptLibraries | [編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。 |

次のように、`h` スイッチを使用して、`aspnet-codegenerator razorpage` コマンドに関するヘルプを取得します。

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

詳細については、「[dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)」を参照してください 