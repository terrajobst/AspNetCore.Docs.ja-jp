::: moniker range=">= aspnetcore-3.0"

Id scaffolder を実行します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューションエクスプローラー**で、プロジェクトを右クリックして >**新しいスキャフォールディング項目**を**追加**> ます。
* **[Add スキャフォールディング]** ダイアログボックスの左ペインで、[> **Identity** ] を選択して **[add]** を選択します。
* **[Id の追加]** ダイアログボックスで、必要なオプションを選択します。
  * 既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。 既存の *\_Layout*ファイルが選択されている場合、そのファイルは上書きされ**ません**。

 例: MVC プロジェクトの Razor Pages `~/Views/Shared/_Layout.cshtml` の `~/Pages/Shared/_Layout.cshtml`
* 既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。 データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。
  * データコンテキストクラスを選択します。
  * **[追加]** を選びます。
* 新しいユーザーコンテキストを作成し、場合によっては Id のカスタムユーザークラスを作成するには、次のようにします。
  * 選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。
  * **[追加]** を選びます。

注: 新しいユーザーコンテキストを作成している場合は、上書きするファイルを選択する必要はありません。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

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

プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。 たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。 DB コンテキストの正しい完全修飾名を使用します。

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell では、コマンドの区切り記号としてセミコロンを使用します。 PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。 例:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

`--files` フラグまたは `--useDefaultUI` フラグを指定せずに Identity scaffolder を実行すると、使用可能なすべての Id UI ページがプロジェクト内に作成されます。

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Id scaffolder を実行します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューションエクスプローラー**で、プロジェクトを右クリックして >**新しいスキャフォールディング項目**を**追加**> ます。
* **[Add スキャフォールディング]** ダイアログボックスの左ペインで、[> **Identity** ] を選択して **[add]** を選択します。
* **[Id の追加]** ダイアログボックスで、必要なオプションを選択します。
  * 既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。 既存の *\_Layout*ファイルが選択されている場合、そのファイルは上書きされ**ません**。

 例: MVC プロジェクトの Razor Pages `~/Views/Shared/_Layout.cshtml` の `~/Pages/Shared/_Layout.cshtml`
* 既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。 データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。
  * データコンテキストクラスを選択します。
  * **[追加]** を選びます。
* 新しいユーザーコンテキストを作成し、場合によっては Id のカスタムユーザークラスを作成するには、次のようにします。
  * 選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。
  * **[追加]** を選びます。

注: 新しいユーザーコンテキストを作成している場合は、上書きするファイルを選択する必要はありません。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクト (\*.csproj) ファイルに追加してください。 プロジェクト ディレクトリに、次のコマンドを実行します。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。 たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。 DB コンテキストの正しい完全修飾名を使用します。

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell では、コマンドの区切り記号としてセミコロンを使用します。 PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。 例:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

`--files` フラグまたは `--useDefaultUI` フラグを指定せずに Identity scaffolder を実行すると、使用可能なすべての Id UI ページがプロジェクト内に作成されます。

---

::: moniker-end