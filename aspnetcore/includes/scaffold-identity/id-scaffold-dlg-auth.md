::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2c50-101">Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c2c50-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2c50-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c2c50-103">**ソリューションエクスプローラー**で、プロジェクトを右クリックして >**新しいスキャフォールディング項目**を**追加**> ます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c2c50-104">**[Add スキャフォールディング]** ダイアログボックスの左ペインで、[> **Identity** ] を選択して **[add]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c2c50-105">**[Id の追加]** ダイアログボックスで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="c2c50-106">既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="c2c50-107">既存の *\_Layout*ファイルが選択されている場合、そのファイルは上書きされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="c2c50-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="c2c50-108">例: MVC プロジェクトの Razor Pages `~/Views/Shared/_Layout.cshtml` の `~/Pages/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="c2c50-108">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="c2c50-109">既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="c2c50-110">データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2c50-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="c2c50-111">データコンテキストクラスを選択します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-111">Select your data context class.</span></span>
  * <span data-ttu-id="c2c50-112">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-112">Select **Add**.</span></span>
* <span data-ttu-id="c2c50-113">新しいユーザーコンテキストを作成し、場合によっては Id のカスタムユーザークラスを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c2c50-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="c2c50-114">選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="c2c50-115">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-115">Select **Add**.</span></span>

<span data-ttu-id="c2c50-116">注: 新しいユーザーコンテキストを作成している場合は、上書きするファイルを選択する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c2c50-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c2c50-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c2c50-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c2c50-118">ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。</span><span class="sxs-lookup"><span data-stu-id="c2c50-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c2c50-119">必須の NuGet パッケージ参照をプロジェクト (\*.csproj) ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-119">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="c2c50-120">プロジェクト ディレクトリに、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-120">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="c2c50-121">Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-121">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="c2c50-122">プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="c2c50-123">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="c2c50-124">DB コンテキストの正しい完全修飾名を使用します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-124">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="c2c50-125">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c2c50-126">PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="c2c50-127">例:</span><span class="sxs-lookup"><span data-stu-id="c2c50-127">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="c2c50-128">`--files` フラグまたは `--useDefaultUI` フラグを指定せずに Identity scaffolder を実行すると、使用可能なすべての Id UI ページがプロジェクト内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c2c50-129">Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-129">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c2c50-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2c50-130">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c2c50-131">**ソリューションエクスプローラー**で、プロジェクトを右クリックして >**新しいスキャフォールディング項目**を**追加**> ます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-131">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c2c50-132">**[Add スキャフォールディング]** ダイアログボックスの左ペインで、[> **Identity** ] を選択して **[add]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-132">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c2c50-133">**[Id の追加]** ダイアログボックスで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-133">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="c2c50-134">既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-134">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="c2c50-135">既存の *\_Layout*ファイルが選択されている場合、そのファイルは上書きされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="c2c50-135">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="c2c50-136">例: MVC プロジェクトの Razor Pages `~/Views/Shared/_Layout.cshtml` の `~/Pages/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="c2c50-136">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="c2c50-137">既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-137">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="c2c50-138">データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c2c50-138">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="c2c50-139">データコンテキストクラスを選択します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-139">Select your data context class.</span></span>
  * <span data-ttu-id="c2c50-140">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-140">Select **Add**.</span></span>
* <span data-ttu-id="c2c50-141">新しいユーザーコンテキストを作成し、場合によっては Id のカスタムユーザークラスを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c2c50-141">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="c2c50-142">選択、 **+** 新たに作成するボタン**データ コンテキスト クラス**します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-142">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="c2c50-143">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-143">Select **Add**.</span></span>

<span data-ttu-id="c2c50-144">注: 新しいユーザーコンテキストを作成している場合は、上書きするファイルを選択する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c2c50-144">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c2c50-145">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c2c50-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c2c50-146">ASP.NET Core scaffolder を以前インストールしていない場合は、今すぐインストールします。</span><span class="sxs-lookup"><span data-stu-id="c2c50-146">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c2c50-147">[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクト (\*.csproj) ファイルに追加してください。</span><span class="sxs-lookup"><span data-stu-id="c2c50-147">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="c2c50-148">プロジェクト ディレクトリに、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-148">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="c2c50-149">Identity scaffolder オプションを一覧表示するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-149">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="c2c50-150">プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-150">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="c2c50-151">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-151">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="c2c50-152">DB コンテキストの正しい完全修飾名を使用します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-152">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="c2c50-153">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="c2c50-153">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c2c50-154">PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-154">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="c2c50-155">例:</span><span class="sxs-lookup"><span data-stu-id="c2c50-155">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="c2c50-156">`--files` フラグまたは `--useDefaultUI` フラグを指定せずに Identity scaffolder を実行すると、使用可能なすべての Id UI ページがプロジェクト内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="c2c50-156">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end