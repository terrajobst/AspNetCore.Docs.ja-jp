<span data-ttu-id="ae9ff-101">Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae9ff-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae9ff-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ae9ff-103">**ソリューション エクスプ ローラー**、プロジェクトを右クリックして >**追加** > **スキャフォールディングされた新しい項目**します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ae9ff-104">左側のウィンドウから、**スキャフォールディングの追加**ダイアログ ボックスで、 **Identity** > **追加**します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="ae9ff-105">**[Id の追加]** ダイアログボックスで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="ae9ff-106">既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="ae9ff-107">たとえば、MVC プロジェクトの Razor Pages `~/Views/Shared/_Layout.cshtml` の `~/Pages/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="ae9ff-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="ae9ff-108">選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="ae9ff-109">選択**追加**します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-109">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ae9ff-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ae9ff-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ae9ff-111">ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="ae9ff-112">必須の NuGet パッケージ参照をプロジェクト (\*.csproj) ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-112">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="ae9ff-113">プロジェクト ディレクトリに、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-113">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="ae9ff-114">Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-114">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="ae9ff-115">プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="ae9ff-116">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ae9ff-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
