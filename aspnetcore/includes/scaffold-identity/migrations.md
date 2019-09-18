生成された Id データベースコードには[Entity Framework Core 移行](/ef/core/managing-schemas/migrations/)が必要です。 移行を作成し、データベースを更新します。 たとえば、次のコマンドを実行します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio で**パッケージ マネージャー コンソール**:

```powershell
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

`Add-Migration`コマンドの "CreateIdentitySchema" name パラメーターは任意です。 `"CreateIdentitySchema"`移行について説明します。
