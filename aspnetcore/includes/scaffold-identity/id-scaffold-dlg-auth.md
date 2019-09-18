<span data-ttu-id="c52bd-101">Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c52bd-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c52bd-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c52bd-103">**ソリューション エクスプ ローラー**、プロジェクトを右クリックして >**追加** > **スキャフォールディングされた新しい項目**します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c52bd-104">**[Add スキャフォールディング]** ダイアログボックスの左ペインで、[ **Identity** > **add**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c52bd-105">**[Id の追加]** ダイアログボックスで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="c52bd-106">既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。</span><span class="sxs-lookup"><span data-stu-id="c52bd-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="c52bd-107">*既存\_の Layout*ファイルが選択されている場合、そのファイルは上書きされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="c52bd-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="c52bd-108">例: `~/Pages/Shared/_Layout.cshtml` MVC プロジェクトの`~/Views/Shared/_Layout.cshtml` Razor Pages の場合</span><span class="sxs-lookup"><span data-stu-id="c52bd-108">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="c52bd-109">既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="c52bd-110">データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c52bd-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="c52bd-111">データコンテキストクラスを選択します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-111">Select your data context class.</span></span>
  * <span data-ttu-id="c52bd-112">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c52bd-112">Select **Add**.</span></span>
* <span data-ttu-id="c52bd-113">新しいユーザーコンテキストを作成し、場合によっては Id のカスタムユーザークラスを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c52bd-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="c52bd-114">選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="c52bd-115">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c52bd-115">Select **Add**.</span></span>

<span data-ttu-id="c52bd-116">メモ:新しいユーザーコンテキストを作成する場合は、上書きするファイルを選択する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c52bd-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c52bd-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c52bd-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c52bd-118">ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。</span><span class="sxs-lookup"><span data-stu-id="c52bd-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c52bd-119">[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクト (\*.csproj) ファイルに追加してください。</span><span class="sxs-lookup"><span data-stu-id="c52bd-119">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="c52bd-120">プロジェクト ディレクトリに、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-120">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="c52bd-121">Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-121">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="c52bd-122">プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="c52bd-123">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="c52bd-124">DB コンテキストの正しい完全修飾名を使用します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-124">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

<span data-ttu-id="c52bd-125">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="c52bd-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c52bd-126">PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="c52bd-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="c52bd-127">例えば:</span><span class="sxs-lookup"><span data-stu-id="c52bd-127">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="c52bd-128">`--files` フラグ`--useDefaultUI`やフラグを指定せずに identity scaffolder を実行すると、使用可能なすべての id UI ページがプロジェクトに作成されます。</span><span class="sxs-lookup"><span data-stu-id="c52bd-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---
