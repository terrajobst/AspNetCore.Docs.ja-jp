---
title: ASP.NETコア プロジェクトの Identity にユーザー データを追加、ダウンロード、および削除する
author: rick-anderson
description: ASP.NET Core プロジェクトでカスタム ユーザー データを Identity に追加する方法について説明します。 GDPR ごとにデータを削除します。
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501230"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>ASP.NETコア プロジェクトの Identity にカスタム ユーザー データを追加、ダウンロード、および削除する

著者 [Rick Anderson](https://twitter.com/RickAndMSFT)

この記事では、次の方法について説明します。

* ASP.NETコア Web アプリにカスタム ユーザー データを追加します。
* カスタム ユーザー データ モデルに<xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute>属性をマークし、自動的にダウンロードおよび削除できるようにします。 データをダウンロードして削除できるようにすることで[、GDPR](xref:security/gdpr)要件を満たすことができます。

プロジェクト サンプルは Razor ページ Web アプリから作成されますが、ASP.NET Core MVC Web アプリの手順は似ています。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>前提条件

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Razor Web アプリの作成

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Visual Studio の **[ファイル]** メニューから、**[新規作成]** > **[プロジェクト]** の順に選択します。 [ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致する場合は、プロジェクト**WebApp1**に名前を付けます。
* **コア Web アプリケーション**>ASP.NET**選択OK**
* ドロップダウン**でASP.NET Core 3.0**を選択します。
* **Web アプリケーションの**>選択**OK**
* プロジェクトをビルドして実行します。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Visual Studio の **[ファイル]** メニューから、**[新規作成]** > **[プロジェクト]** の順に選択します。 [ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)コードの名前空間と一致する場合は、プロジェクト**WebApp1**に名前を付けます。
* **コア Web アプリケーション**>ASP.NET**選択OK**
* ドロップダウン**ASP.NET Core 2.2**を選択します。
* **Web アプリケーションの**>選択**OK**
* プロジェクトをビルドして実行します。

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>ID スキャフフォルダーを実行する

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューション エクスプローラ**で、プロジェクトを右クリック>**新しいスキャフォールディング項目の****追加** > ] をクリックします。
* [**スキャフォールディングの追加**] ダイアログの左ペインで、[**識別情報** > **の追加**] を選択します。
* [ID の**追加**] ダイアログで、次のオプションを選択します。
  * 既存のレイアウト ファイル*を選択する ~/ページ/共有/_Layout.cshtml*
  * 上書きする次のファイルを選択します。
    * **アカウント/登録**
    * **アカウント/管理/インデックス**
  * ボタンを**+** 選択して、新しい**Data コンテキスト クラス**を作成します。 プロジェクトが WebApp1 という名前の場合は、型を受け入れます (**WebApp1.Models.WebApp1Context** )。 **WebApp1**
  * ボタンを**+** 選択して新しい**User クラス**を作成します。 プロジェクト名が**WebApp1**の場合は、タイプ ( **WebApp1User**) を受け入>**追加**します。
* **[追加]** を選択します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core scaffolder を以前にインストールしていない場合は、ここでインストールします。

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

プロジェクト (.csproj)[ファイルにパッケージ](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)参照を追加します。 プロジェクト ディレクトリで次のコマンドを実行します。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

次のコマンドを実行して、ID スキャフフォルダーオプションを一覧表示します。

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

プロジェクト フォルダーで、ID スキャフフォルダーを実行します。

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

[移行、UseAuthentication、およびレイアウト](xref:security/authentication/scaffold-identity#efm)の指示に従って、次の手順を実行します。

* 移行を作成し、データベースを更新します。
* `UseAuthentication` を `Startup.Configure` に追加します。
* レイアウト`<partial name="_LoginPartial" />`ファイルに追加します。
* アプリをテストします。
  * ユーザーを登録する
  * 新しいユーザー名を選択します (**ログアウト**リンクの横)。 ユーザー名やその他のリンクを表示するには、ウィンドウを展開するか、ナビゲーション バーのアイコンを選択する必要があります。
  * [**個人データ**] タブを選択します。
  * [**ダウンロード**] ボタンを選択し、*パーソナルデータ.json*ファイルを調べます。
  * [**削除**] ボタンをテストします。

## <a name="add-custom-user-data-to-the-identity-db"></a>カスタム ユーザー データを ID DB に追加する

派生クラス`IdentityUser`をカスタム プロパティで更新します。 プロジェクト WebApp1 という名前のファイルは、*エリア/ID/データ/WebApp1User.cs*という名前になります。 次のコードでファイルを更新します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

[パーソナルデータ](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute)属性を持つプロパティは次のとおりです。

* *エリア/アイデンティティ/ページ/アカウント/管理/削除パーソナルデータ.cshtml*カミソリページが呼び`UserManager.Delete`出されたときに削除されます。
* *エリア/アイデンティティ/ページ/アカウント/管理/ダウンロードパーソナルデータ.cshtml*カミソリページによってダウンロードされたデータに含まれています。

### <a name="update-the-accountmanageindexcshtml-page"></a>アカウント/管理/インデックス.cshtml ページを更新します。

次の`InputModel`強調表示されたコードを使用して *、領域/ID/ページ/アカウント/管理/Index.cshtml.cs*を更新します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

次の強調表示されたマークアップで*領域/ID/ページ/アカウント/管理/Index.cshtml*を更新します。

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

次の強調表示されたマークアップで*領域/ID/ページ/アカウント/管理/Index.cshtml*を更新します。

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>アカウント/レジスタ.cshtmlページを更新します。

次の`InputModel`強調表示されたコードを使用して *、領域/ID/ページ/アカウント/Register.cshtml.cs*を更新します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

次の強調表示されたマークアップで*領域/ID/ページ/アカウント/Register.cshtml*を更新します。

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

次の強調表示されたマークアップで*領域/ID/ページ/アカウント/Register.cshtml*を更新します。

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


プロジェクトをビルドします。

### <a name="add-a-migration-for-the-custom-user-data"></a>カスタム ユーザー データの移行を追加する

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio**パッケージ マネージャー コンソールで次の手順を実行します**。

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>カスタム ユーザー データの作成、表示、ダウンロード、削除をテストする

アプリをテストします。

* 新しいユーザーを登録します。
* ページ上のカスタム ユーザー`/Identity/Account/Manage`データを表示します。
* ページからユーザーの個人データをダウンロードして`/Identity/Account/Manage/PersonalData`表示します。

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Id にクレームを追加します。<ApplicationUser>

インターフェイスを使用して、ASP.NETコア ID にクレーム`IUserClaimsPrincipalFactory<T>`を追加できます。 このクラスは、メソッド内のアプリに`Startup.ConfigureServices`追加できます。 次のようにクラスのカスタム実装を追加します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

デモ コードでは、`ApplicationUser`クラスを使用します。 このクラスは、`IsAdmin`追加のクレームを追加するために使用されるプロパティを追加します。

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

`AdditionalUserClaimsPrincipalFactory` は、`UserClaimsPrincipalFactory` インターフェイスを実装します。 新しいロール要求が に追加`ClaimsPrincipal`されます。

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

その後、追加の要求をアプリで使用できます。 Razor ページでは、インスタンス`IAuthorizationService`を使用して要求値にアクセスできます。

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
