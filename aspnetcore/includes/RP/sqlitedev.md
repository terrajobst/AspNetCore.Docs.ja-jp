### <a name="use-sqlite-for-development-sql-server-for-production"></a>開発用に SQLite を、運用環境に SQL Server を使用する

SQLite が選択されている場合、テンプレートで生成されたコードは開発の準備ができています。 次のコードでは、スタートアップに <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を挿入する方法を示します。 `IWebHostEnvironment` が挿入されているので、`ConfigureServices` では開発用に SQLite を、運用環境に SQL Server を使用できます。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]
