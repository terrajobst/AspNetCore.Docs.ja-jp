生成された Id データベースコードには[Entity Framework Core 移行](/ef/core/managing-schemas/migrations/)が必要です。 移行を作成し、データベースを更新します。 たとえば、次のコマンドを実行します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio**パッケージマネージャーコンソール**で次のようにします。

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

`Add-Migration` コマンドの "CreateIdentitySchema" name パラメーターは任意です。 移行について `"CreateIdentitySchema"` 説明します。
