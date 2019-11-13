<a name="dc"></a>

### <a name="add-a-database-context-class"></a>データベース コンテキスト クラスの追加

RazorPagesMovie プロジェクトで、*Data* という名前の新しいフォルダーを作成します。 次の `RazorPagesMovieContext` クラスを *Data* フォルダーに追加します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

上記のコードによって、エンティティ セットの `DbSet` プロパティが作成されます。 Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>データベース接続文字列の追加

次の強調表示されたコードに示されているように、*appsettings.json* ファイルに接続文字列を追加します。

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>NuGet パッケージと EF ツールを追加します。

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>データベース コンテキストの登録

*Startup.cs* の先頭に次の `using` ステートメントを追加します。

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a>必要な NuGet パッケージの追加

次の .NET Core CLI コマンドを実行し、SQLite と CodeGeneration.Design をプロジェクトに追加します。

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

スキャフォールディングには `Microsoft.VisualStudio.Web.CodeGeneration.Design` パッケージが必要です。

<a name="reg"></a>

### <a name="register-the-database-context"></a>データベース コンテキストの登録

*Startup.cs* の先頭に次の `using` ステートメントを追加します。

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

エラー チェックとしてプロジェクトをビルドします。

::: moniker-end
