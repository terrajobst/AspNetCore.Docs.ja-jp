<span data-ttu-id="a3ab0-101">Identity scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a3ab0-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3ab0-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a3ab0-103">**ソリューション エクスプ ローラー**、プロジェクトを右クリックして >**追加** > **スキャフォールディングされた新しい項目**します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="a3ab0-104">左側のウィンドウから、**スキャフォールディングの追加**ダイアログ ボックスで、 **Identity** > **追加**します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="a3ab0-105">**ADD アイデンティティ**ダイアログ ボックスで、オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="a3ab0-106">既存のレイアウト ページを選択するか、不適切なマークアップで、レイアウト ファイルが上書きされます。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="a3ab0-107">既存 *\_Layout.cshtml*ファイルが選択されている場合は**いない**上書きされます。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="a3ab0-108">たとえば`~/Pages/Shared/_Layout.cshtml`Razor ページの`~/Views/Shared/_Layout.cshtml`MVC プロジェクト</span><span class="sxs-lookup"><span data-stu-id="a3ab0-108">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="a3ab0-109">既存のデータ コンテキストを使用するには、少なくとも 1 つのファイルを上書きするを選択します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="a3ab0-110">データ コンテキストを追加するには少なくとも 1 つのファイルを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="a3ab0-111">データ コンテキスト クラスを選択します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-111">Select your data context class.</span></span>
  * <span data-ttu-id="a3ab0-112">選択**追加**します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-112">Select **ADD**.</span></span>
* <span data-ttu-id="a3ab0-113">新しいユーザー コンテキストを作成し、場合によって Id のカスタム ユーザー クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="a3ab0-114">選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="a3ab0-115">選択**追加**します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-115">Select **ADD**.</span></span>

<span data-ttu-id="a3ab0-116">メモ:新しいユーザー コンテキストを作成する場合は、オーバーライドするファイルを選択する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a3ab0-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a3ab0-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a3ab0-118">ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="a3ab0-119">パッケージ参照を追加[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)プロジェクト (\*.csproj) ファイル。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-119">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="a3ab0-120">プロジェクト ディレクトリに、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-120">Run the following command in the project directory:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="a3ab0-121">Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-121">Run the following command to list the Identity scaffolder options:</span></span>

```console
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="a3ab0-122">プロジェクト フォルダー内には、必要なオプションで Identity scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="a3ab0-123">たとえば、id、既定値は、UI とファイルの最小数を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="a3ab0-124">DB コンテキストの正しい完全修飾名を使用します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-124">Use the correct fully qualified name for your DB context:</span></span>

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

<span data-ttu-id="a3ab0-125">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="a3ab0-126">PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープまたは二重引用符で囲まれたファイルの一覧を配置します。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="a3ab0-127">例えば:</span><span class="sxs-lookup"><span data-stu-id="a3ab0-127">For example:</span></span>

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="a3ab0-128">Identity scaffolder を指定せずに実行する場合、`--files`フラグまたは`--useDefaultUI`プロジェクトで使用可能なの Identity の UI ページが作成されるすべてのフラグします。</span><span class="sxs-lookup"><span data-stu-id="a3ab0-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---
