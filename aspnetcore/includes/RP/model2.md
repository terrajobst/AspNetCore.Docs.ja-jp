<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="94802-101">データベース コンテキスト クラスの追加</span><span class="sxs-lookup"><span data-stu-id="94802-101">Add a database context class</span></span>

<span data-ttu-id="94802-102">RazorPagesMovie プロジェクトで、*Data* という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="94802-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="94802-103">次の `RazorPagesMovieContext` クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="94802-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="94802-104">上記のコードによって、エンティティ セットの `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="94802-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="94802-105">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="94802-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="94802-106">このコードは、後の手順で依存関係が追加されるまでコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="94802-106">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="94802-107">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="94802-107">Add a database connection string</span></span>

<span data-ttu-id="94802-108">次の強調表示されたコードに示されているように、*appsettings.json* ファイルに接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="94802-108">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="94802-109">NuGet パッケージと EF ツールを追加します。</span><span class="sxs-lookup"><span data-stu-id="94802-109">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="94802-110">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="94802-110">Register the database context</span></span>

<span data-ttu-id="94802-111">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="94802-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="94802-112">`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="94802-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="94802-113">必要な NuGet パッケージの追加</span><span class="sxs-lookup"><span data-stu-id="94802-113">Add required NuGet packages</span></span>

<span data-ttu-id="94802-114">次の .NET Core CLI コマンドを実行し、SQLite と CodeGeneration.Design をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="94802-114">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<span data-ttu-id="94802-115">スキャフォールディングには `Microsoft.VisualStudio.Web.CodeGeneration.Design` パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="94802-115">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="94802-116">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="94802-116">Register the database context</span></span>

<span data-ttu-id="94802-117">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="94802-117">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="94802-118">`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="94802-118">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="94802-119">エラー チェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="94802-119">Build the project as a check for errors.</span></span>

::: moniker-end
