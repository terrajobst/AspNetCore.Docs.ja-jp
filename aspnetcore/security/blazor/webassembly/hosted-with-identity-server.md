---
title: Id サーバーを使用して、ASP.NET Core Blazor Webasホステッドアプリをセキュリティで保護する
author: guardrex
description: ホスティング[サーバー](https://identityserver.io/)バックエンドを使用する Visual Studio 内から認証を使用して新しい Blazor ホスト型アプリを作成するには
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 6c7942a827d88a620e6f295af3f523c23f4b3890
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219052"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="25fa1-103">Id サーバーを使用して、ASP.NET Core Blazor Webasホステッドアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="25fa1-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="25fa1-104">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="25fa1-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="25fa1-105">ユーザーと API 呼び出しを認証するためにユーザーを使用する、Visual Studio で新しい Blazor ホスト型アプリを作成する[には、](https://identityserver.io/)次のようにします。</span><span class="sxs-lookup"><span data-stu-id="25fa1-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="25fa1-106">Visual Studio を使用して、新しい **Blazor** アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="25fa1-107">詳細については、<xref:blazor/get-started> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25fa1-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="25fa1-108">[**新しい Blazor アプリの作成**] ダイアログで、 **[認証]** セクションの **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="25fa1-109">**個々のユーザーアカウント**を選択し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="25fa1-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="25fa1-110">**[詳細設定]** セクションで ホストされている **[ASP.NET Core]** チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="25fa1-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="25fa1-111">**[作成]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-111">Select the **Create** button.</span></span>

<span data-ttu-id="25fa1-112">コマンドシェルでアプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="25fa1-113">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="25fa1-114">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="25fa1-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="25fa1-115">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="25fa1-115">Server app configuration</span></span>

<span data-ttu-id="25fa1-116">次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="25fa1-117">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="25fa1-117">Startup class</span></span>

<span data-ttu-id="25fa1-118">`Startup` クラスには、次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="25fa1-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="25fa1-119">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="25fa1-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="25fa1-120">既定の UI を使用した id:</span><span class="sxs-lookup"><span data-stu-id="25fa1-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="25fa1-121">次のように、追加の <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ヘルパーメソッドを使用して、サーバーの上に既定の ASP.NET Core 規則を設定します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="25fa1-122">追加の <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ヘルパーメソッドを使用した認証。次のように、アプリを構成して、サーバーによって生成された JWT トークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="25fa1-123">`Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="25fa1-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="25fa1-124">要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="25fa1-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="25fa1-125">Open ID Connect (OIDC) エンドポイントを公開するサーバーミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="25fa1-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="25fa1-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="25fa1-126">AddApiAuthorization</span></span>

<span data-ttu-id="25fa1-127"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper メソッドは、ASP.NET Core シナリオ用に[サーバー](https://identityserver.io/)を構成します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="25fa1-128">サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="25fa1-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="25fa1-129">一般に、最も一般的なシナリオでは、不要な複雑さが公開されます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="25fa1-130">そのため、適切な出発点として考えられる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="25fa1-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="25fa1-131">認証を変更する必要が生じた場合でも、アプリの要件に合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="25fa1-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="25fa1-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="25fa1-133"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper メソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="25fa1-134">このポリシーは、id URL 空間 `/Identity`のサブパスにルーティングされたすべての要求を Id が処理できるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="25fa1-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="25fa1-135"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> は、他のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="25fa1-136">さらに、このメソッドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="25fa1-136">Additionally, this method:</span></span>

* <span data-ttu-id="25fa1-137">`{APPLICATION NAME}API`の既定のスコープを使用して、`{APPLICATION NAME}API` API リソースをサーバーに登録します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="25fa1-138">アプリに対して、サービスによって発行されたトークンを検証するように JWT ベアラートークンミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="25fa1-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="25fa1-139">WeatherForecastController</span></span>

<span data-ttu-id="25fa1-140">`WeatherForecastController` (*Controllers/WeatherForecastController*) では、 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)属性がクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="25fa1-141">属性は、ユーザーがリソースにアクセスするための既定のポリシーに基づいて承認される必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="25fa1-142">既定の承認ポリシーは、既定の認証スキームを使用するように構成されます。これは、前に説明したポリシースキームに <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> によって設定されます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="25fa1-143">ヘルパーメソッドは、アプリケーションに対する要求の既定のハンドラーとして <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> を構成します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="25fa1-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="25fa1-144">ApplicationDbContext</span></span>

<span data-ttu-id="25fa1-145">`ApplicationDbContext` (*Data/ApplicationDbContext*) では、id で同じ <xref:Microsoft.EntityFrameworkCore.DbContext> が使用されます。ただし、<xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 拡張して、ユーザーのスキーマを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="25fa1-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="25fa1-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。</span><span class="sxs-lookup"><span data-stu-id="25fa1-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="25fa1-147">データベーススキーマを完全に制御するには、使用可能な Id <xref:Microsoft.EntityFrameworkCore.DbContext> クラスの1つを継承し、`OnModelCreating` メソッドで `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` を呼び出して、Id スキーマを含めるようにコンテキストを構成します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="25fa1-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="25fa1-148">OidcConfigurationController</span></span>

<span data-ttu-id="25fa1-149">`OidcConfigurationController` (*Controllers/OidcConfigurationController*) では、oidc パラメーターを提供するためにクライアントエンドポイントがプロビジョニングされます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="25fa1-150">アプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="25fa1-150">App settings files</span></span>

<span data-ttu-id="25fa1-151">プロジェクトルートのアプリ設定ファイル (*appsettings. json*) で、[`IdentityServer`] セクションに構成されているクライアントの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="25fa1-152">次の例には、1つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="25fa1-152">In the following example, there's a single client.</span></span> <span data-ttu-id="25fa1-153">クライアント名はアプリケーション名に対応し、OAuth `ClientId` パラメーターに規約によってマップされます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="25fa1-154">プロファイルは、構成されているアプリの種類を示します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="25fa1-155">プロファイルは、サーバーの構成プロセスを簡略化する規則を推進するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="25fa1-156">開発環境のアプリ設定ファイル (appsettings) で、*appsettings.Development.json*`IdentityServer` セクションでは、プロジェクトルートで、トークンの署名に使用されるキーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="25fa1-157">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="25fa1-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="25fa1-158">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="25fa1-158">Authentication package</span></span>

<span data-ttu-id="25fa1-159">個々のユーザーアカウント (`Individual`) を使用するようにアプリを作成すると、アプリは、アプリのプロジェクトファイルで `Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="25fa1-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="25fa1-160">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="25fa1-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="25fa1-161">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="25fa1-162">前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="25fa1-163">API 承認のサポート</span><span class="sxs-lookup"><span data-stu-id="25fa1-163">API authorization support</span></span>

<span data-ttu-id="25fa1-164">ユーザー認証のサポートは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージ内で提供される拡張メソッドによってサービスコンテナーに接続されます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="25fa1-165">このメソッドは、アプリが既存の承認システムと対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="25fa1-166">既定では、`_configuration/{client-id}`から、規約に従ってアプリの構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="25fa1-167">規則により、クライアント ID はアプリのアセンブリ名に設定されます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="25fa1-168">この URL は、オプションを指定してオーバーロードを呼び出すことによって、別のエンドポイントを指すように変更できます。</span><span class="sxs-lookup"><span data-stu-id="25fa1-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="index-page"></a><span data-ttu-id="25fa1-169">Index ページ</span><span class="sxs-lookup"><span data-stu-id="25fa1-169">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="25fa1-170">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="25fa1-170">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="25fa1-171">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="25fa1-171">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="25fa1-172">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="25fa1-172">LoginDisplay component</span></span>

<span data-ttu-id="25fa1-173">`LoginDisplay` コンポーネント (*shared/Logindisplay*) は、`MainLayout` コンポーネント (*shared/mainlayout. razor*) にレンダリングされ、次の動作を管理します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-173">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="25fa1-174">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="25fa1-174">For authenticated users:</span></span>
  * <span data-ttu-id="25fa1-175">現在のユーザー名を表示します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-175">Displays the current user name.</span></span>
  * <span data-ttu-id="25fa1-176">ASP.NET Core Id のユーザープロファイルページへのリンクを提供します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-176">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="25fa1-177">アプリからログアウトするためのボタンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="25fa1-177">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="25fa1-178">匿名ユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="25fa1-178">For anonymous users:</span></span>
  * <span data-ttu-id="25fa1-179">登録するオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="25fa1-179">Offers the option to register.</span></span>
  * <span data-ttu-id="25fa1-180">ログインするオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="25fa1-180">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="25fa1-181">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="25fa1-181">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="25fa1-182">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="25fa1-182">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="25fa1-183">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="25fa1-183">Run the app</span></span>

<span data-ttu-id="25fa1-184">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-184">Run the app from the Server project.</span></span> <span data-ttu-id="25fa1-185">Visual Studio を使用する場合は、**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="25fa1-185">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
