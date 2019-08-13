<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="2e7ab-101">データベース コンテキスト クラスの追加</span><span class="sxs-lookup"><span data-stu-id="2e7ab-101">Add a database context class</span></span>

<span data-ttu-id="2e7ab-102">RazorPagesMovie プロジェクトで、*Data* という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="2e7ab-103">次の `RazorPagesMovieContext` クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2e7ab-104">上記のコードによって、エンティティ セットの `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="2e7ab-105">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="2e7ab-106">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="2e7ab-106">Add a database connection string</span></span>

<span data-ttu-id="2e7ab-107">次の強調表示されたコードに示されているように、*appsettings.json* ファイルに接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-107">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="2e7ab-108">NuGet パッケージと EF ツールを追加します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-108">Add NuGet packages and EF tools</span></span>

<span data-ttu-id="2e7ab-109">RazorPagesMovie プロジェクトのターミナルを開きます。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-109">Open a terminal for the RazorPagesMovie project.</span></span>  <span data-ttu-id="2e7ab-110">デザイン/レイアウト バーでプロジェクト名を右クリックし、ターミナルで **[ツール]、[開く]** の順に進みます。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-110">Right click the project name in the design/layout bar and go to **Tools > Open** in Terminal.</span></span> <span data-ttu-id="2e7ab-111">ターミナルで次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-111">Run the following .NET Core CLI commands in the Termial:</span></span>

```console
dotnet tool install --global dotnet-ef --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

<span data-ttu-id="2e7ab-112">上記のコマンドでは、.NET CLI 用の Entity Framework Core ツールと複数のパッケージがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-112">The preceding commands add Entity Framework Core Tools for the .NET CLI and several packages to the project.</span></span> <span data-ttu-id="2e7ab-113">スキャフォールディングには `Microsoft.VisualStudio.Web.CodeGeneration.Design` パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-113">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="2e7ab-114">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="2e7ab-114">Register the database context</span></span>

<span data-ttu-id="2e7ab-115">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-115">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="2e7ab-116">`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-116">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="2e7ab-117">必要な NuGet パッケージの追加</span><span class="sxs-lookup"><span data-stu-id="2e7ab-117">Add required NuGet packages</span></span>

<span data-ttu-id="2e7ab-118">次の .NET Core CLI コマンドを実行し、SQLite と CodeGeneration.Design をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design

```

<span data-ttu-id="2e7ab-119">スキャフォールディングには `Microsoft.VisualStudio.Web.CodeGeneration.Design` パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="2e7ab-120">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="2e7ab-120">Register the database context</span></span>

<span data-ttu-id="2e7ab-121">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="2e7ab-122">`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="2e7ab-123">エラー チェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="2e7ab-123">Build the project as a check for errors.</span></span>
::: moniker-end
