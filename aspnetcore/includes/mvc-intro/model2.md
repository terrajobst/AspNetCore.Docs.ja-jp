<a name="dc"></a>

<span data-ttu-id="25ca4-101">次の `MvcMovieContext` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="25ca4-101">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="25ca4-102">上記のコードによって、エンティティ セットの `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="25ca4-102">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="25ca4-103">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="25ca4-103">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="25ca4-104">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="25ca4-104">Add a database connection string</span></span>

<span data-ttu-id="25ca4-105">*appsettings.json* ファイルに接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="25ca4-105">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="25ca4-106">必要な NuGet パッケージの追加</span><span class="sxs-lookup"><span data-stu-id="25ca4-106">Add required NuGet packages</span></span>

<span data-ttu-id="25ca4-107">次の .NET Core CLI コマンドを実行し、SQLite と CodeGeneration.Design をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="25ca4-107">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="25ca4-108">スキャフォールディングには `Microsoft.VisualStudio.Web.CodeGeneration.Design` パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="25ca4-108">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="25ca4-109">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="25ca4-109">Register the database context</span></span>

<span data-ttu-id="25ca4-110">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="25ca4-110">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="25ca4-111">`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="25ca4-111">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="25ca4-112">エラー チェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="25ca4-112">Build the project as a check for errors.</span></span>