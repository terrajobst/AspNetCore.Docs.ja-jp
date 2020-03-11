Id scaffolder を実行します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューションエクスプローラー**で、プロジェクトを右クリックして > **新しいスキャフォールディング項目**を**追加**> ます。
* **[Add スキャフォールディング]** ダイアログボックスの左ペインで、[ > **Identity** ] を選択して **[add]** を選択します。
* **[Id の追加]** ダイアログボックスで、必要なオプションを選択します。
  * 既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。 たとえば、MVC プロジェクトの Razor Pages `~/Views/Shared/_Layout.cshtml` の `~/Pages/Shared/_Layout.cshtml`
  * [ **+** ] ボタンを選択して、新しい**データコンテキストクラス**を作成します。
* **[追加]** を選択します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

必須の NuGet パッケージ参照をプロジェクト (\*.csproj) ファイルに追加します。 プロジェクト ディレクトリに、次のコマンドを実行します。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。 たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
