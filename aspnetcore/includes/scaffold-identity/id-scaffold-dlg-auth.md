Id scaffolder を実行します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューション エクスプ ローラー**、プロジェクトを右クリックして >**追加** > **スキャフォールディングされた新しい項目**します。
* **[Add スキャフォールディング]** ダイアログボックスの左ペインで、[ **Identity** > **add**] を選択します。
* **[Id の追加]** ダイアログボックスで、必要なオプションを選択します。
  * 既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。 *既存\_の Layout*ファイルが選択されている場合、そのファイルは上書きされ**ません**。

 例: `~/Pages/Shared/_Layout.cshtml` MVC プロジェクトの`~/Views/Shared/_Layout.cshtml` Razor Pages の場合
* 既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。 データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。
  * データコンテキストクラスを選択します。
  * **[追加]** を選びます。
* 新しいユーザーコンテキストを作成し、場合によっては Id のカスタムユーザークラスを作成するには、次のようにします。
  * 選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。
  * **[追加]** を選びます。

メモ:新しいユーザーコンテキストを作成する場合は、上書きするファイルを選択する必要はありません。

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

プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。 たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。 DB コンテキストの正しい完全修飾名を使用します。

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

PowerShell では、コマンドの区切り記号としてセミコロンを使用します。 PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。 例えば:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

`--files` フラグ`--useDefaultUI`やフラグを指定せずに identity scaffolder を実行すると、使用可能なすべての id UI ページがプロジェクトに作成されます。

---
