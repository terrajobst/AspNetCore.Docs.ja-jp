<span data-ttu-id="67d96-101">Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="67d96-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="67d96-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67d96-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="67d96-103">**ソリューションエクスプローラー**で、プロジェクトを右クリックして > **新しいスキャフォールディング項目**を**追加**> ます。</span><span class="sxs-lookup"><span data-stu-id="67d96-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="67d96-104">**[Add スキャフォールディング]** ダイアログボックスの左ペインで、[ > **Identity** ] を選択して **[add]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="67d96-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="67d96-105">**[Id の追加]** ダイアログボックスで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="67d96-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="67d96-106">既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。</span><span class="sxs-lookup"><span data-stu-id="67d96-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="67d96-107">既存の *\_Layout*ファイルが選択されている場合、そのファイルは上書きされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="67d96-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="67d96-108">例: MVC プロジェクトの Razor Pages `~/Views/Shared/_Layout.cshtml` の `~/Pages/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="67d96-108">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="67d96-109">既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。</span><span class="sxs-lookup"><span data-stu-id="67d96-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="67d96-110">データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="67d96-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="67d96-111">データコンテキストクラスを選択します。</span><span class="sxs-lookup"><span data-stu-id="67d96-111">Select your data context class.</span></span>
  * <span data-ttu-id="67d96-112">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="67d96-112">Select **Add**.</span></span>
* <span data-ttu-id="67d96-113">新しいユーザーコンテキストを作成し、場合によっては Id のカスタムユーザークラスを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="67d96-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="67d96-114">[ **+** ] ボタンを選択して、新しい**データコンテキストクラス**を作成します。</span><span class="sxs-lookup"><span data-stu-id="67d96-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="67d96-115">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="67d96-115">Select **Add**.</span></span>

<span data-ttu-id="67d96-116">注: 新しいユーザーコンテキストを作成している場合は、上書きするファイルを選択する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="67d96-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="67d96-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="67d96-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="67d96-118">ASP.NET Core scaffolder をまだインストールしていない場合は、ここでインストールします。</span><span class="sxs-lookup"><span data-stu-id="67d96-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="67d96-119">[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクト (\*.csproj) ファイルに追加してください。</span><span class="sxs-lookup"><span data-stu-id="67d96-119">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="67d96-120">プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="67d96-120">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="67d96-121">次のコマンドを実行して、Identity scaffolder オプションの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="67d96-121">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="67d96-122">プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="67d96-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="67d96-123">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="67d96-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="67d96-124">DB コンテキストの正しい完全修飾名を使用します。</span><span class="sxs-lookup"><span data-stu-id="67d96-124">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="67d96-125">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="67d96-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="67d96-126">PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="67d96-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="67d96-127">(例:</span><span class="sxs-lookup"><span data-stu-id="67d96-127">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="67d96-128">`--files` フラグまたは `--useDefaultUI` フラグを指定せずに Identity scaffolder を実行すると、使用可能なすべての Id UI ページがプロジェクト内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="67d96-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---
