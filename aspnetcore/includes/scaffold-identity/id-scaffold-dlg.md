<span data-ttu-id="821ce-101">Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="821ce-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="821ce-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="821ce-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="821ce-103">**ソリューションエクスプローラー**で、プロジェクトを右クリックして > **新しいスキャフォールディング項目**を**追加**> ます。</span><span class="sxs-lookup"><span data-stu-id="821ce-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="821ce-104">**[Add スキャフォールディング]** ダイアログボックスの左ペインで、[ > **Identity** ] を選択して **[add]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="821ce-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="821ce-105">**[Id の追加]** ダイアログボックスで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="821ce-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="821ce-106">既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。</span><span class="sxs-lookup"><span data-stu-id="821ce-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="821ce-107">たとえば、MVC プロジェクトの Razor Pages `~/Views/Shared/_Layout.cshtml` の `~/Pages/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="821ce-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="821ce-108">[ **+** ] ボタンを選択して、新しい**データコンテキストクラス**を作成します。</span><span class="sxs-lookup"><span data-stu-id="821ce-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="821ce-109">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="821ce-109">Select **ADD**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="821ce-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="821ce-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="821ce-111">ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。</span><span class="sxs-lookup"><span data-stu-id="821ce-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="821ce-112">必須の NuGet パッケージ参照をプロジェクト (\*.csproj) ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="821ce-112">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="821ce-113">プロジェクト ディレクトリに、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="821ce-113">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="821ce-114">Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="821ce-114">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="821ce-115">プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="821ce-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="821ce-116">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="821ce-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
