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
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="9be56-103">ASP.NET アプリ間での認証 cookie を共有します。</span><span class="sxs-lookup"><span data-stu-id="9be56-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="9be56-104">によって[Rick Anderson](https://twitter.com/RickAndMSFT)と[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9be56-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9be56-105">Web サイトは多くの場合、連携して、個々 の web アプリで構成されます。</span><span class="sxs-lookup"><span data-stu-id="9be56-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="9be56-106">シングル サインオン (SSO) エクスペリエンスを提供するには、サイト内で web アプリは、認証 cookie を共有する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9be56-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="9be56-107">このシナリオをサポートするためには、データ保護スタックは、Katana cookie 認証と ASP.NET Core の cookie 認証チケットの共有を許可します。</span><span class="sxs-lookup"><span data-stu-id="9be56-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="9be56-108">次の例。</span><span class="sxs-lookup"><span data-stu-id="9be56-108">In the examples that follow:</span></span>

* <span data-ttu-id="9be56-109">認証の cookie 名は、共通の値に設定されて`.AspNet.SharedCookie`します。</span><span class="sxs-lookup"><span data-stu-id="9be56-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="9be56-110">`AuthenticationType`に設定されている`Identity.Application`明示的にまたは既定のいずれか。</span><span class="sxs-lookup"><span data-stu-id="9be56-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="9be56-111">一般的なアプリ名を使用して、データ保護キーを共有するデータ保護システムを有効に (`SharedCookieApp`)。</span><span class="sxs-lookup"><span data-stu-id="9be56-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="9be56-112">`Identity.Application` 認証方式として使用されます。</span><span class="sxs-lookup"><span data-stu-id="9be56-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="9be56-113">どのようなスキームが使用される、一貫して使用する必要があります*内および間*cookie の共有アプリいずれかまたは明示的に設定することで、既定のスキームとして。</span><span class="sxs-lookup"><span data-stu-id="9be56-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="9be56-114">スキームは、アプリ間で一貫性のあるスキームを使用する必要がありますので、cookie を暗号化および暗号化のときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9be56-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="9be56-115">共通[データ保護キー](xref:security/data-protection/implementation/key-management)記憶域の場所を使用します。</span><span class="sxs-lookup"><span data-stu-id="9be56-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="9be56-116">ASP.NET Core アプリで<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>キー記憶域の場所を設定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="9be56-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="9be56-117">Cookie 認証ミドルウェアを .NET Framework のアプリでの実装を使用して<xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>します。</span><span class="sxs-lookup"><span data-stu-id="9be56-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="9be56-118">`DataProtectionProvider` 暗号化と認証 cookie のペイロード データの復号化用のデータ保護サービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9be56-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="9be56-119">`DataProtectionProvider`インスタンスは、アプリの他の部分で使用されるデータ保護システムから分離されます。</span><span class="sxs-lookup"><span data-stu-id="9be56-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="9be56-120">[DataProtectionProvider.Create (System.IO.DirectoryInfo、アクション\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*)を受け入れる、<xref:System.IO.DirectoryInfo>データ保護キー記憶域の場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="9be56-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="9be56-121">`DataProtectionProvider` 必要があります、 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="9be56-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="9be56-122">ASP.NET Core 2.x アプリでは、参照、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)します。</span><span class="sxs-lookup"><span data-stu-id="9be56-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="9be56-123">.NET Framework のアプリにパッケージ参照を追加[Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)します。</span><span class="sxs-lookup"><span data-stu-id="9be56-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="9be56-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 一般的なアプリ名を設定します。</span><span class="sxs-lookup"><span data-stu-id="9be56-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="9be56-125">ASP.NET Core アプリ間での認証 cookie を共有します。</span><span class="sxs-lookup"><span data-stu-id="9be56-125">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="9be56-126">ASP.NET Core Identity を使用する: 場合</span><span class="sxs-lookup"><span data-stu-id="9be56-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="9be56-127">データ保護キーと、アプリ名は、アプリ間で共有する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9be56-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="9be56-128">共通のキー記憶域の場所が提供される、<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>メソッドは、次の例です。</span><span class="sxs-lookup"><span data-stu-id="9be56-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="9be56-129">使用<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>一般的なアプリの共有名を構成する (`SharedCookieApp`次の例)。</span><span class="sxs-lookup"><span data-stu-id="9be56-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="9be56-130">詳細については、「 <xref:security/data-protection/configuration/overview> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9be56-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="9be56-131">使用して、 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> cookie のデータ保護サービスを設定する拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="9be56-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="9be56-132">次の例では、認証の種類に設定されて`Identity.Application`既定。</span><span class="sxs-lookup"><span data-stu-id="9be56-132">In the following example, the authentication type is set to `Identity.Application` by default.</span></span>

<span data-ttu-id="9be56-133">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="9be56-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="9be56-134">ASP.NET Core Identity なしで直接 cookie を使用する場合は、データ保護との認証を構成`Startup.ConfigureServices`します。</span><span class="sxs-lookup"><span data-stu-id="9be56-134">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9be56-135">次の例では、認証の種類に設定されて`Identity.Application`:</span><span class="sxs-lookup"><span data-stu-id="9be56-135">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

<span data-ttu-id="9be56-136">サブドメインで cookie を共有するアプリをホストする場合での一般的なドメインを指定してください。、 [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)プロパティ。</span><span class="sxs-lookup"><span data-stu-id="9be56-136">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="9be56-137">アプリ間で cookie を共有する`contoso.com`など`first_subdomain.contoso.com`と`second_subdomain.contoso.com`、指定、`Cookie.Domain`として`.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="9be56-137">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="9be56-138">保存時のデータ保護キーを暗号化します。</span><span class="sxs-lookup"><span data-stu-id="9be56-138">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="9be56-139">運用環境のデプロイ、構成、 `DataProtectionProvider` DPAPI または X509Certificate と保存時のキーを暗号化します。</span><span class="sxs-lookup"><span data-stu-id="9be56-139">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="9be56-140">詳細については、「 <xref:security/data-protection/implementation/key-encryption-at-rest> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9be56-140">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="9be56-141">次の例では、証明書の拇印が提供されている<xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span><span class="sxs-lookup"><span data-stu-id="9be56-141">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="9be56-142">ASP.NET との間の認証 cookie の共有 4.x および ASP.NET Core アプリ</span><span class="sxs-lookup"><span data-stu-id="9be56-142">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="9be56-143">ASP.NET Core の Cookie 認証ミドルウェアと互換性のある認証 cookie を生成する Katana Cookie 認証ミドルウェアを使用する ASP.NET 4.x アプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9be56-143">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="9be56-144">これにより、サイト全体で滑らかな SSO エクスペリエンスを提供しながら、いくつかの手順で、大規模なサイトの個々 のアプリをアップグレードできます。</span><span class="sxs-lookup"><span data-stu-id="9be56-144">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="9be56-145">アプリは、Katana Cookie 認証ミドルウェアを使用しているときに呼び出す`UseCookieAuthentication`プロジェクトの*Startup.Auth.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="9be56-145">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="9be56-146">ASP.NET 4.x web アプリ プロジェクトでは、Visual Studio 2013 で作成され、後で、既定では、Katana の Cookie 認証ミドルウェアを使用しています。</span><span class="sxs-lookup"><span data-stu-id="9be56-146">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="9be56-147">`UseCookieAuthentication`は古い形式の ASP.NET Core アプリを呼び出すのではサポート`UseCookieAuthentication`では、ASP.NET 4.x アプリ Katana Cookie 認証ミドルウェアを使用するが無効です。</span><span class="sxs-lookup"><span data-stu-id="9be56-147">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="9be56-148">ASP.NET 4.x アプリが .NET Framework 4.5.1 をターゲットする必要がありますまたはそれ以降。</span><span class="sxs-lookup"><span data-stu-id="9be56-148">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="9be56-149">それ以外の場合、必要な NuGet パッケージは、インストールに失敗します。</span><span class="sxs-lookup"><span data-stu-id="9be56-149">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="9be56-150">ASP.NET 4.x アプリと ASP.NET Core アプリの間の認証クッキーを共有するには、ASP.NET Core アプリで説明したように構成、 [ASP.NET Core アプリ間での認証 cookie の共有](#share-authentication-cookies-among-aspnet-core-apps)セクションで、その後として ASP.NET 4.x アプリを構成します。次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9be56-150">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-among-aspnet-core-apps) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="9be56-151">アプリのパッケージが最新のリリースに更新されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9be56-151">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="9be56-152">インストール、 [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)各 ASP.NET 4.x アプリにパッケージします。</span><span class="sxs-lookup"><span data-stu-id="9be56-152">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="9be56-153">検索し、呼び出しを変更し`UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="9be56-153">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="9be56-154">ASP.NET Core の Cookie 認証ミドルウェアで使用される名前と一致する cookie の名前を変更 (`.AspNet.SharedCookie`の例)。</span><span class="sxs-lookup"><span data-stu-id="9be56-154">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="9be56-155">次の例では、認証の種類に設定されて`Identity.Application`します。</span><span class="sxs-lookup"><span data-stu-id="9be56-155">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="9be56-156">インスタンスを指定する`DataProtectionProvider`一般的なデータ保護キー記憶域の場所を初期化します。</span><span class="sxs-lookup"><span data-stu-id="9be56-156">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="9be56-157">アプリ名は、認証 cookie を共有するすべてのアプリで使用される共通のアプリ名に設定されていることを確認します (`SharedCookieApp`の例)。</span><span class="sxs-lookup"><span data-stu-id="9be56-157">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="9be56-158">設定しない場合`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`と`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`設定<xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier>に一意のユーザーを識別するクレーム。</span><span class="sxs-lookup"><span data-stu-id="9be56-158">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="9be56-159">*App_Start/Startup.Auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="9be56-159">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="9be56-160">ユーザー id、認証の種類を生成するときに (`Identity.Application`) で定義された型に一致する必要があります`AuthenticationType`セット`UseCookieAuthentication`で*App_Start/Startup.Auth.cs*します。</span><span class="sxs-lookup"><span data-stu-id="9be56-160">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="9be56-161">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="9be56-161">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="9be56-162">一般的なユーザー データベースを使用します。</span><span class="sxs-lookup"><span data-stu-id="9be56-162">Use a common user database</span></span>

<span data-ttu-id="9be56-163">アプリ スキーマ (Id の同じバージョン) は、各アプリの Id システムが同じユーザー データベースで参照されていることを確認します。 同じ Id を使用するとします。</span><span class="sxs-lookup"><span data-stu-id="9be56-163">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="9be56-164">それ以外の場合、id システムでは、そのデータベース内の情報に対して認証クッキーの情報と一致しようとしたときに実行時にエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9be56-164">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="9be56-165">Id スキーマがアプリ間で異なる場合は、通常はアプリは、各種の Id を使用しているため、Id の最新バージョンに基づく一般的なデータベースを共有なしでは再マップとその他のアプリのユーザーのスキーマで列を追加します。</span><span class="sxs-lookup"><span data-stu-id="9be56-165">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="9be56-166">方が効率的に共通のデータベースは、アプリで共有できるように、最新の Id を使用するその他のアプリケーションをアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="9be56-166">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9be56-167">その他の資料</span><span class="sxs-lookup"><span data-stu-id="9be56-167">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
