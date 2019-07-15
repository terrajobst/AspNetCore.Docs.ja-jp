Identity scaffolder を実行します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューション エクスプ ローラー**、プロジェクトを右クリックして >**追加** > **スキャフォールディングされた新しい項目**します。
* 左側のウィンドウから、**スキャフォールディングの追加**ダイアログ ボックスで、 **Identity** > **追加**します。
* **ADD アイデンティティ**ダイアログ ボックスで、オプションを選択します。
  * 既存のレイアウト ページを選択するか、不適切なマークアップで、レイアウト ファイルが上書きされます。 既存 *\_Layout.cshtml*ファイルが選択されている場合は**いない**上書きされます。

 たとえば`~/Pages/Shared/_Layout.cshtml`Razor ページの`~/Views/Shared/_Layout.cshtml`MVC プロジェクト
* 既存のデータ コンテキストを使用するには、少なくとも 1 つのファイルを上書きするを選択します。 データ コンテキストを追加するには少なくとも 1 つのファイルを選択する必要があります。
  * データ コンテキスト クラスを選択します。
  * 選択**追加**します。
* 新しいユーザー コンテキストを作成し、場合によって Id のカスタム ユーザー クラスを作成します。
  * 選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。
  * 選択**追加**します。

メモ:新しいユーザー コンテキストを作成する場合は、オーバーライドするファイルを選択する必要はありません。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

パッケージ参照を追加[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)プロジェクト (\*.csproj) ファイル。 プロジェクト ディレクトリに、次のコマンドを実行します。

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。

```console
dotnet aspnet-codegenerator identity -h
```

プロジェクト フォルダー内には、必要なオプションで Identity scaffolder を実行します。 たとえば、id、既定値は、UI とファイルの最小数を設定するには、次のコマンドを実行します。 DB コンテキストの正しい完全修飾名を使用します。

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

PowerShell では、コマンドの区切り記号としてセミコロンを使用します。 PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープまたは二重引用符で囲まれたファイルの一覧を配置します。 例えば:

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Identity scaffolder を指定せずに実行する場合、`--files`フラグまたは`--useDefaultUI`プロジェクトで使用可能なの Identity の UI ページが作成されるすべてのフラグします。

---
