<span data-ttu-id="bae1d-101">生成された Id データベースコードには[Entity Framework Core 移行](/ef/core/managing-schemas/migrations/)が必要です。</span><span class="sxs-lookup"><span data-stu-id="bae1d-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="bae1d-102">移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="bae1d-102">Create a migration and update the database.</span></span> <span data-ttu-id="bae1d-103">たとえば、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bae1d-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bae1d-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bae1d-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bae1d-105">Visual Studio で**パッケージ マネージャー コンソール**:</span><span class="sxs-lookup"><span data-stu-id="bae1d-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bae1d-106">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bae1d-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="bae1d-107">`Add-Migration`コマンドの "CreateIdentitySchema" name パラメーターは任意です。</span><span class="sxs-lookup"><span data-stu-id="bae1d-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="bae1d-108">`"CreateIdentitySchema"`移行について説明します。</span><span class="sxs-lookup"><span data-stu-id="bae1d-108">`"CreateIdentitySchema"` describes the migration.</span></span>
