::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="cf3a2-101">*Data* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-101">Create a *Data* folder.</span></span>

<span data-ttu-id="cf3a2-102">次の `MvcMovieContext` クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="cf3a2-103">上記のコードによって、エンティティ セットの `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="cf3a2-104">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="cf3a2-105">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="cf3a2-105">Add a database connection string</span></span>

<span data-ttu-id="cf3a2-106">*appsettings.json* ファイルに接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="cf3a2-107">NuGet パッケージと EF ツールを追加します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-107">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="cf3a2-108">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="cf3a2-108">Register the database context</span></span>

<span data-ttu-id="cf3a2-109">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-109">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="cf3a2-110">`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-110">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="cf3a2-111">コンパイラ エラーのチェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-111">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cf3a2-112">次の `MvcMovieContext` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-112">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="cf3a2-113">上記のコードによって、エンティティ セットの `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-113">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="cf3a2-114">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-114">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="cf3a2-115">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="cf3a2-115">Add a database connection string</span></span>

<span data-ttu-id="cf3a2-116">*appsettings.json* ファイルに接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-116">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="cf3a2-117">必要な NuGet パッケージの追加</span><span class="sxs-lookup"><span data-stu-id="cf3a2-117">Add required NuGet packages</span></span>

<span data-ttu-id="cf3a2-118">次の .NET Core CLI コマンドを実行し、SQLite と CodeGeneration.Design をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="cf3a2-119">スキャフォールディングには `Microsoft.VisualStudio.Web.CodeGeneration.Design` パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="cf3a2-120">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="cf3a2-120">Register the database context</span></span>

<span data-ttu-id="cf3a2-121">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="cf3a2-122">`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="cf3a2-123">エラー チェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="cf3a2-123">Build the project as a check for errors.</span></span>
::: moniker-end