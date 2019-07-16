---
title: ASP.NET アプリ間での認証 cookie を共有します。
author: rick-anderson
description: ASP.NET 認証 cookie を共有する方法について説明します 4.x および ASP.NET Core アプリ。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2019
uid: security/cookie-sharing
ms.openlocfilehash: b2f906ac97fe79b2a66a5ab709bcbcb03ab8cc39
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223923"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>ASP.NET アプリ間での認証 cookie を共有します。

によって[Rick Anderson](https://twitter.com/RickAndMSFT)と[Luke Latham](https://github.com/guardrex)

Web サイトは多くの場合、連携して、個々 の web アプリで構成されます。 シングル サインオン (SSO) エクスペリエンスを提供するには、サイト内で web アプリは、認証 cookie を共有する必要があります。 このシナリオをサポートするためには、データ保護スタックは、Katana cookie 認証と ASP.NET Core の cookie 認証チケットの共有を許可します。

次の例。

* 認証の cookie 名は、共通の値に設定されて`.AspNet.SharedCookie`します。
* `AuthenticationType`に設定されている`Identity.Application`明示的にまたは既定のいずれか。
* 一般的なアプリ名を使用して、データ保護キーを共有するデータ保護システムを有効に (`SharedCookieApp`)。
* `Identity.Application` 認証方式として使用されます。 どのようなスキームが使用される、一貫して使用する必要があります*内および間*cookie の共有アプリいずれかまたは明示的に設定することで、既定のスキームとして。 スキームは、アプリ間で一貫性のあるスキームを使用する必要がありますので、cookie を暗号化および暗号化のときに使用されます。
* 共通[データ保護キー](xref:security/data-protection/implementation/key-management)記憶域の場所を使用します。
  * ASP.NET Core アプリで<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>キー記憶域の場所を設定するために使用します。
  * Cookie 認証ミドルウェアを .NET Framework のアプリでの実装を使用して<xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>します。 `DataProtectionProvider` 暗号化と認証 cookie のペイロード データの復号化用のデータ保護サービスを提供します。 `DataProtectionProvider`インスタンスは、アプリの他の部分で使用されるデータ保護システムから分離されます。 [DataProtectionProvider.Create (System.IO.DirectoryInfo、アクション\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*)を受け入れる、<xref:System.IO.DirectoryInfo>データ保護キー記憶域の場所を指定します。
* `DataProtectionProvider` 必要があります、 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet パッケージ。
  * ASP.NET Core 2.x アプリでは、参照、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)します。
  * .NET Framework のアプリにパッケージ参照を追加[Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)します。
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 一般的なアプリ名を設定します。

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>ASP.NET Core アプリ間での認証 cookie を共有します。

ASP.NET Core Identity を使用する: 場合

* データ保護キーと、アプリ名は、アプリ間で共有する必要があります。 共通のキー記憶域の場所が提供される、<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>メソッドは、次の例です。 使用<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>一般的なアプリの共有名を構成する (`SharedCookieApp`次の例)。 詳細については、「 <xref:security/data-protection/configuration/overview> 」を参照してください。
* 使用して、 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> cookie のデータ保護サービスを設定する拡張メソッド。
* 次の例では、認証の種類に設定されて`Identity.Application`既定。

`Startup.ConfigureServices`の場合:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

ASP.NET Core Identity なしで直接 cookie を使用する場合は、データ保護との認証を構成`Startup.ConfigureServices`します。 次の例では、認証の種類に設定されて`Identity.Application`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

サブドメインで cookie を共有するアプリをホストする場合での一般的なドメインを指定してください。、 [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)プロパティ。 アプリ間で cookie を共有する`contoso.com`など`first_subdomain.contoso.com`と`second_subdomain.contoso.com`、指定、`Cookie.Domain`として`.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>保存時のデータ保護キーを暗号化します。

運用環境のデプロイ、構成、 `DataProtectionProvider` DPAPI または X509Certificate と保存時のキーを暗号化します。 詳細については、「 <xref:security/data-protection/implementation/key-encryption-at-rest> 」を参照してください。 次の例では、証明書の拇印が提供されている<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>ASP.NET との間の認証 cookie の共有 4.x および ASP.NET Core アプリ

ASP.NET Core の Cookie 認証ミドルウェアと互換性のある認証 cookie を生成する Katana Cookie 認証ミドルウェアを使用する ASP.NET 4.x アプリを構成できます。 これにより、サイト全体で滑らかな SSO エクスペリエンスを提供しながら、いくつかの手順で、大規模なサイトの個々 のアプリをアップグレードできます。

アプリは、Katana Cookie 認証ミドルウェアを使用しているときに呼び出す`UseCookieAuthentication`プロジェクトの*Startup.Auth.cs*ファイル。 ASP.NET 4.x web アプリ プロジェクトでは、Visual Studio 2013 で作成され、後で、既定では、Katana の Cookie 認証ミドルウェアを使用しています。 `UseCookieAuthentication`は古い形式の ASP.NET Core アプリを呼び出すのではサポート`UseCookieAuthentication`では、ASP.NET 4.x アプリ Katana Cookie 認証ミドルウェアを使用するが無効です。

ASP.NET 4.x アプリが .NET Framework 4.5.1 をターゲットする必要がありますまたはそれ以降。 それ以外の場合、必要な NuGet パッケージは、インストールに失敗します。

ASP.NET 4.x アプリと ASP.NET Core アプリの間の認証クッキーを共有するには、ASP.NET Core アプリで説明したように構成、 [ASP.NET Core アプリ間での認証 cookie の共有](#share-authentication-cookies-among-aspnet-core-apps)セクションで、その後として ASP.NET 4.x アプリを構成します。次のとおりです。

アプリのパッケージが最新のリリースに更新されることを確認します。 インストール、 [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)各 ASP.NET 4.x アプリにパッケージします。

検索し、呼び出しを変更し`UseCookieAuthentication`:

* ASP.NET Core の Cookie 認証ミドルウェアで使用される名前と一致する cookie の名前を変更 (`.AspNet.SharedCookie`の例)。
* 次の例では、認証の種類に設定されて`Identity.Application`します。
* インスタンスを指定する`DataProtectionProvider`一般的なデータ保護キー記憶域の場所を初期化します。
* アプリ名は、認証 cookie を共有するすべてのアプリで使用される共通のアプリ名に設定されていることを確認します (`SharedCookieApp`の例)。

設定しない場合`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`と`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`設定<xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier>に一意のユーザーを識別するクレーム。

*App_Start/Startup.Auth.cs*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create({PATH TO COMMON KEY RING FOLDER},
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

ユーザー id、認証の種類を生成するときに (`Identity.Application`) で定義された型に一致する必要があります`AuthenticationType`セット`UseCookieAuthentication`で*App_Start/Startup.Auth.cs*します。

*Models/IdentityModels.cs*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>一般的なユーザー データベースを使用します。

アプリ スキーマ (Id の同じバージョン) は、各アプリの Id システムが同じユーザー データベースで参照されていることを確認します。 同じ Id を使用するとします。 それ以外の場合、id システムでは、そのデータベース内の情報に対して認証クッキーの情報と一致しようとしたときに実行時にエラーが生成されます。

Id スキーマがアプリ間で異なる場合は、通常はアプリは、各種の Id を使用しているため、Id の最新バージョンに基づく一般的なデータベースを共有なしでは再マップとその他のアプリのユーザーのスキーマで列を追加します。 方が効率的に共通のデータベースは、アプリで共有できるように、最新の Id を使用するその他のアプリケーションをアップグレードします。

## <a name="additional-resources"></a>その他の資料

* <xref:host-and-deploy/web-farm>
