Id scaffolder を実行します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューション エクスプ ローラー**、プロジェクトを右クリックして >**追加** > **スキャフォールディングされた新しい項目**します。
* 左側のウィンドウから、**スキャフォールディングの追加**ダイアログ ボックスで、 **Identity** > **追加**します。
* **[Id の追加]** ダイアログボックスで、必要なオプションを選択します。
  * 既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。 たとえば、MVC プロジェクト`~/Views/Shared/_Layout.cshtml`の Razor Pages の場合`~/Pages/Shared/_Layout.cshtml`
  * 選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。
* 選択**追加**します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクト (\*.csproj) ファイルに追加してください。 プロジェクト ディレクトリに、次のコマンドを実行します。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。 たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
