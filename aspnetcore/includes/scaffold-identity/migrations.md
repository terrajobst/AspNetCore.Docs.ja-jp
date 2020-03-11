<span data-ttu-id="992cc-101">生成された Id データベースコードには[Entity Framework Core 移行](/ef/core/managing-schemas/migrations/)が必要です。</span><span class="sxs-lookup"><span data-stu-id="992cc-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="992cc-102">移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="992cc-102">Create a migration and update the database.</span></span> <span data-ttu-id="992cc-103">たとえば、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="992cc-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="992cc-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="992cc-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="992cc-105">Visual Studio**パッケージマネージャーコンソール**で次のようにします。</span><span class="sxs-lookup"><span data-stu-id="992cc-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="992cc-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="992cc-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="992cc-107">`Add-Migration` コマンドの "CreateIdentitySchema" name パラメーターは任意です。</span><span class="sxs-lookup"><span data-stu-id="992cc-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="992cc-108">移行について `"CreateIdentitySchema"` 説明します。</span><span class="sxs-lookup"><span data-stu-id="992cc-108">`"CreateIdentitySchema"` describes the migration.</span></span>
