---
title: ASP.NET アプリ間で認証 cookie を共有する
author: rick-anderson
description: ASP.NET 4.x と ASP.NET Core アプリ間で認証 cookie を共有する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: security/cookie-sharing
ms.openlocfilehash: 7e29be22717f0b97fc115ac036cc54e333bed4e2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651608"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>ASP.NET アプリ間で認証 cookie を共有する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

多くの場合、web サイトは、連携して動作する個々の web アプリで構成されます。 シングルサインオン (SSO) エクスペリエンスを提供するには、サイト内の web アプリで認証 cookie を共有する必要があります。 このシナリオをサポートするために、データ保護スタックは、Katana cookie 認証とクッキー認証チケットの ASP.NET Core 共有を許可します。

次の例を参照してください。

* 認証 cookie 名は、`.AspNet.SharedCookie`の共通値に設定されます。
* `AuthenticationType` は、明示的に、または既定で `Identity.Application` に設定されます。
* 共通のアプリ名は、データ保護システムがデータ保護キー (`SharedCookieApp`) を共有できるようにするために使用されます。
* 認証スキームとして `Identity.Application` が使用されます。 使用されるスキームにかかわらず、共有 cookie アプリ*内*でも、既定のスキームとしても、明示的に設定することによっても、一貫して使用する必要があります。 スキームは、cookie の暗号化と復号化に使用されるため、一貫したスキームをアプリ間で使用する必要があります。
* 一般的な[データ保護キー](xref:security/data-protection/implementation/key-management)の保存場所が使用されます。
  * ASP.NET Core アプリでは、キーの格納場所を設定するために <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> が使用されます。
  * .NET Framework アプリでは、Cookie 認証ミドルウェアは <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>の実装を使用します。 `DataProtectionProvider` には、認証 cookie ペイロードデータの暗号化と復号化を行うためのデータ保護サービスが用意されています。 `DataProtectionProvider` インスタンスは、アプリの他の部分で使用されるデータ保護システムから分離されています。 [Dataprotectionprovider. Create (DirectoryInfo, Action\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*)は、データ保護キーストレージの場所を指定するための <xref:System.IO.DirectoryInfo> を受け入れます。
* `DataProtectionProvider` には、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet パッケージが必要です。
  * ASP.NET Core 2.x アプリで、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照します。
  * .NET Framework アプリで、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)へのパッケージ参照を追加します。
* 共通アプリ名を <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 設定します。

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>認証 cookie を ASP.NET Core Id と共有する

ASP.NET Core Id を使用する場合:

* データ保護キーとアプリ名は、アプリ間で共有する必要があります。 共通のキー格納場所は、次の例のように <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> メソッドに提供されます。 共通の共有アプリ名を構成するには <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> を使用します (次の例では`SharedCookieApp`)。 詳細については、<xref:security/data-protection/configuration/overview> を参照してください。
* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> 拡張メソッドを使用して、cookie 用のデータ保護サービスを設定します。
* 既定の認証の種類は `Identity.Application`です。

`Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>ASP.NET Core Id なしで認証 cookie を共有する

ASP.NET Core Id なしで cookie を直接使用する場合は、`Startup.ConfigureServices`でデータ保護と認証を構成します。 次の例では、認証の種類が `Identity.Application`に設定されています。

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a>異なるベースパス間での cookie の共有

認証クッキーは、既定の[cookie のパス](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)として[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)を使用します。 アプリの cookie を異なるベースパス間で共有する必要がある場合は、`Path` をオーバーライドする必要があります。

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a>サブドメイン間での cookie の共有

サブドメイン間で cookie を共有するアプリをホストする場合は、" [cookie. ドメイン](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)" プロパティで共通のドメインを指定します。 `first_subdomain.contoso.com` や `second_subdomain.contoso.com`などの `contoso.com`のアプリ間で cookie を共有するには、`Cookie.Domain` を `.contoso.com`として指定します。

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>保存時のデータ保護キーの暗号化

運用環境のデプロイでは、`DataProtectionProvider` を構成して、DPAPI または X509Certificate を使用して保存時のキーを暗号化します。 詳細については、<xref:security/data-protection/implementation/key-encryption-at-rest> を参照してください。 次の例では、<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>に証明書の拇印が提供されています。

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>ASP.NET 4.x と ASP.NET Core アプリ間で認証 cookie を共有する

Katana Cookie 認証ミドルウェアを使用する ASP.NET 4.x アプリは、ASP.NET Core Cookie 認証ミドルウェアと互換性のある認証 cookie を生成するように構成できます。 これにより、複数の手順で大規模なサイトの個々のアプリをアップグレードしながら、サイト全体でスムーズに SSO を利用することができます。

アプリで Katana Cookie 認証ミドルウェアを使用すると、プロジェクトの*Startup.Auth.cs*ファイルで `UseCookieAuthentication` が呼び出されます。 Visual Studio 2013 以降で作成された ASP.NET 4.x web アプリプロジェクトは、既定で Katana Cookie 認証ミドルウェアを使用します。 `UseCookieAuthentication` は互換性のために残されており ASP.NET Core アプリではサポートされていませんが、Katana Cookie 認証ミドルウェアを使用する ASP.NET 4.x アプリで `UseCookieAuthentication` を呼び出すことは有効です。

ASP.NET 4.x アプリは .NET Framework 4.5.1 以降を対象にする必要があります。 そうしないと、必要な NuGet パッケージのインストールに失敗します。

ASP.NET 4.x アプリと ASP.NET Core アプリの間で認証 cookie を共有するには、「 [ASP.NET Core アプリ間で認証 cookie を共有](#share-authentication-cookies-with-aspnet-core-identity)する」セクションの説明に従って ASP.NET Core アプリを構成し、次のように ASP.NET 4.x アプリを構成します。

アプリのパッケージが最新リリースに更新されていることを確認します。 それぞれの ASP.NET 4.x アプリに[Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)パッケージをインストールします。

`UseCookieAuthentication`の呼び出しを見つけて変更します。

* Cookie 名を ASP.NET Core Cookie 認証ミドルウェアによって使用される名前 (例では`.AspNet.SharedCookie`) と一致するように変更します。
* 次の例では、認証の種類が `Identity.Application`に設定されています。
* Common data protection キーの格納場所に初期化された `DataProtectionProvider` のインスタンスを指定します。
* アプリケーション名が、認証 cookie を共有するすべてのアプリ (例では`SharedCookieApp`) によって使用される共通アプリ名に設定されていることを確認します。

`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` と `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`を設定していない場合は、一意のユーザーを識別するクレームに <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> を設定します。

*App_Start/startup.auth.cs*:

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
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
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

ユーザー id を生成する場合、認証の種類 (`Identity.Application`) は、 *App_Start/startup.auth.cs*の `UseCookieAuthentication` で `AuthenticationType` セットで定義されている種類と一致する必要があります。

*モデル/* モジュール:

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

## <a name="use-a-common-user-database"></a>共通のユーザーデータベースを使用する

アプリで同じ Id スキーマ (同じバージョンの Id) を使用する場合は、各アプリの Id システムが同じユーザーデータベースで参照されていることを確認します。 それ以外の場合、id システムは、認証クッキーの情報とデータベース内の情報を照合しようとしたときに、実行時にエラーを生成します。

アプリ間で Id スキーマが異なる場合、通常、アプリでは異なる Id バージョンが使用されるため、最新バージョンの Id に基づいて共通データベースを共有することはできません。他のアプリの Id スキーマに再マップして列を追加する必要はありません。 多くの場合、アプリで共通のデータベースを共有できるように、他のアプリをアップグレードして最新の Id バージョンを使用する方が効率的です。

## <a name="additional-resources"></a>その他のリソース

* <xref:host-and-deploy/web-farm>
