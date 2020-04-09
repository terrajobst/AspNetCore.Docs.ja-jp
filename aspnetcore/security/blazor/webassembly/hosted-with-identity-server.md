---
title: アイデンティティサーバーを使用BlazorしてASP.NETコア WebAssembly ホストアプリケーションを保護する
author: guardrex
description: '[Id サーバー](https://identityserver.io/) Blazorバックエンドを使用する Visual Studio 内から認証を使用して新しいホストされたアプリを作成するには'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501279"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="56c04-103">アイデンティティサーバーを使用BlazorしてASP.NETコア WebAssembly ホストアプリケーションを保護する</span><span class="sxs-lookup"><span data-stu-id="56c04-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="56c04-104">[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)と[ルーク・レイサム](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="56c04-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="56c04-105">ユーザーと APIBlazor呼び出しを認証するために[IdentityServer を](https://identityserver.io/)使用する Visual Studio で新しいホストされたアプリを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="56c04-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="56c04-106">新しい**BlazorWeb アセンブリ**アプリを作成するのにには、Visual Studio を使用します。</span><span class="sxs-lookup"><span data-stu-id="56c04-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="56c04-107">詳細については、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56c04-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="56c04-108">[**新しいBlazorアプリの作成**] ダイアログで、[**認証**] セクションの **[変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="56c04-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="56c04-109">[**個々のユーザー アカウントと**それに続く**OK] を**選択します。</span><span class="sxs-lookup"><span data-stu-id="56c04-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="56c04-110">[**詳細**] セクションの **[core ホストASP.NET]** チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="56c04-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="56c04-111">**[作成]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="56c04-111">Select the **Create** button.</span></span>

<span data-ttu-id="56c04-112">コマンド シェルでアプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="56c04-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="56c04-113">プロジェクト フォルダーが存在しない場合にプロジェクト フォルダーを作成する出力場所を指定するには、コマンドに出力オプションをパス (など`-o BlazorSample`) で含めます。</span><span class="sxs-lookup"><span data-stu-id="56c04-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="56c04-114">フォルダー名もプロジェクト名の一部になります。</span><span class="sxs-lookup"><span data-stu-id="56c04-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="56c04-115">サーバー アプリの構成</span><span class="sxs-lookup"><span data-stu-id="56c04-115">Server app configuration</span></span>

<span data-ttu-id="56c04-116">次のセクションでは、認証サポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="56c04-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="56c04-117">スタートアップ クラス</span><span class="sxs-lookup"><span data-stu-id="56c04-117">Startup class</span></span>

<span data-ttu-id="56c04-118">この`Startup`クラスには、次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="56c04-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="56c04-119">`Startup.ConfigureServices`: </span><span class="sxs-lookup"><span data-stu-id="56c04-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="56c04-120">既定の UI を持つ ID:</span><span class="sxs-lookup"><span data-stu-id="56c04-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="56c04-121">IdServer の上<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>にいくつかの既定のASP.NETコア規約を設定する追加のヘルパー メソッドを持つ IdServer:</span><span class="sxs-lookup"><span data-stu-id="56c04-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="56c04-122">IdentityServer によって<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>生成された JWT トークンを検証するようにアプリを構成する追加のヘルパー メソッドを使用した認証:</span><span class="sxs-lookup"><span data-stu-id="56c04-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="56c04-123">`Startup.Configure`: </span><span class="sxs-lookup"><span data-stu-id="56c04-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="56c04-124">要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="56c04-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="56c04-125">オープン ID 接続 (OIDC) エンドポイントを公開する Id サーバーミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="56c04-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="56c04-126">認証の追加</span><span class="sxs-lookup"><span data-stu-id="56c04-126">AddApiAuthorization</span></span>

<span data-ttu-id="56c04-127">ヘルパー<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>メソッドは、ASP.NETコア シナリオ用[の IdentityServer](https://identityserver.io/)を構成します。</span><span class="sxs-lookup"><span data-stu-id="56c04-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="56c04-128">IdentityServer は、アプリのセキュリティに関する懸念を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="56c04-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="56c04-129">IdentityServer は、最も一般的なシナリオで不要な複雑さを公開します。</span><span class="sxs-lookup"><span data-stu-id="56c04-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="56c04-130">したがって、一連の規則と構成オプションが提供され、開始点として適しています。</span><span class="sxs-lookup"><span data-stu-id="56c04-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="56c04-131">認証の変更が必要になると、IdentityServerの全機能を使用して、アプリの要件に合わせて認証をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="56c04-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="56c04-132">を追加します。</span><span class="sxs-lookup"><span data-stu-id="56c04-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="56c04-133">ヘルパー<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>メソッドは、既定の認証ハンドラーとしてアプリのポリシー スキームを構成します。</span><span class="sxs-lookup"><span data-stu-id="56c04-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="56c04-134">ポリシーは、Identity URL スペース`/Identity`内のサブパスにルーティングされるすべてのリクエストを Identity が処理できるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="56c04-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="56c04-135">他<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="56c04-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="56c04-136">さらに、このメソッドは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="56c04-136">Additionally, this method:</span></span>

* <span data-ttu-id="56c04-137">`{APPLICATION NAME}API`既定のスコープが`{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="56c04-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="56c04-138">アプリに対して IdentityServer によって発行されたトークンを検証するように JWT ベアラー トークン ミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="56c04-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="56c04-139">天気予報コントローラ</span><span class="sxs-lookup"><span data-stu-id="56c04-139">WeatherForecastController</span></span>

<span data-ttu-id="56c04-140">(*コントローラ /天気予報コントローラ.cs*)では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)属性がクラスに適用されます。 `WeatherForecastController`</span><span class="sxs-lookup"><span data-stu-id="56c04-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="56c04-141">この属性は、リソースにアクセスするために、デフォルト・ポリシーに基づいてユーザーが許可される必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="56c04-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="56c04-142">デフォルトの許可ポリシーは、前述のポリシー スキーム<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>に設定されたデフォルト認証方式を使用するように構成されます。</span><span class="sxs-lookup"><span data-stu-id="56c04-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="56c04-143">ヘルパー メソッドは、<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>アプリへの要求の既定のハンドラーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="56c04-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="56c04-144">コンテキスト</span><span class="sxs-lookup"><span data-stu-id="56c04-144">ApplicationDbContext</span></span>

<span data-ttu-id="56c04-145">(*データ/アプリケーションDbContext.cs*) では<xref:Microsoft.EntityFrameworkCore.DbContext>、同じことが IdentityServer のスキーマを<xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>含むように拡張される例外を除いて、IDENTITY でも使用されます。 `ApplicationDbContext`</span><span class="sxs-lookup"><span data-stu-id="56c04-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="56c04-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。</span><span class="sxs-lookup"><span data-stu-id="56c04-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="56c04-147">データベース スキーマの完全な制御を取得するには、使用可能な Id<xref:Microsoft.EntityFrameworkCore.DbContext>クラスのいずれかを継承し、メソッドを呼び出`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`すことによって、Identity`OnModelCreating`スキーマを含めるコンテキストを構成します。</span><span class="sxs-lookup"><span data-stu-id="56c04-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="56c04-148">オイドコンセプコンコントローラ</span><span class="sxs-lookup"><span data-stu-id="56c04-148">OidcConfigurationController</span></span>

<span data-ttu-id="56c04-149">`OidcConfigurationController` (*コントローラ/OidcConfigurationController.cs)では、* クライアント エンドポイントが OIDC パラメータを提供するようにプロビジョニングされます。</span><span class="sxs-lookup"><span data-stu-id="56c04-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="56c04-150">アプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="56c04-150">App settings files</span></span>

<span data-ttu-id="56c04-151">プロジェクト ルートのアプリ設定ファイル (*appsettings.json*)`IdentityServer`では、構成済みクライアントの一覧を説明します。</span><span class="sxs-lookup"><span data-stu-id="56c04-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="56c04-152">次の例では、1 つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="56c04-152">In the following example, there's a single client.</span></span> <span data-ttu-id="56c04-153">クライアント名はアプリ名に対応し、規則によって OAuth`ClientId`パラメーターにマップされます。</span><span class="sxs-lookup"><span data-stu-id="56c04-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="56c04-154">プロファイルは、構成されているアプリの種類を示します。</span><span class="sxs-lookup"><span data-stu-id="56c04-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="56c04-155">プロファイルは、サーバーの構成プロセスを簡素化する規則を駆動するために内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="56c04-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="56c04-156">開発環境アプリ設定ファイル (*アプリ設定) でDevelopment.json*) は、プロジェクトルート`IdentityServer`で、トークンの署名に使用されるキーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="56c04-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="56c04-157">クライアント アプリの構成</span><span class="sxs-lookup"><span data-stu-id="56c04-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="56c04-158">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="56c04-158">Authentication package</span></span>

<span data-ttu-id="56c04-159">個々のユーザー アカウント (`Individual`) を使用するアプリが作成されると、アプリは自動的に`Microsoft.AspNetCore.Components.WebAssembly.Authentication`アプリのプロジェクト ファイル内のパッケージのパッケージ参照を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="56c04-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="56c04-160">パッケージには、アプリがユーザーを認証し、保護された API を呼び出すトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="56c04-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="56c04-161">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="56c04-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="56c04-162">上記`{VERSION}`のパッケージリファレンスを<xref:blazor/get-started>、この記事に示されているパッケージ`Microsoft.AspNetCore.Blazor.Templates`のバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="56c04-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="56c04-163">API 承認のサポート</span><span class="sxs-lookup"><span data-stu-id="56c04-163">API authorization support</span></span>

<span data-ttu-id="56c04-164">ユーザー認証のサポートは、パッケージ内に用意されている拡張メソッドによってサービス コンテナーに接続されます`Microsoft.AspNetCore.Components.WebAssembly.Authentication`。</span><span class="sxs-lookup"><span data-stu-id="56c04-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="56c04-165">このメソッドは、アプリが既存の承認システムと対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="56c04-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="56c04-166">既定では、アプリの構成を規則に従って`_configuration/{client-id}`から読み込みます。</span><span class="sxs-lookup"><span data-stu-id="56c04-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="56c04-167">慣例により、クライアント ID はアプリのアセンブリ名に設定されます。</span><span class="sxs-lookup"><span data-stu-id="56c04-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="56c04-168">この URL は、オプションを指定してオーバーロードを呼び出すことによって、別のエンドポイントを指すように変更できます。</span><span class="sxs-lookup"><span data-stu-id="56c04-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="56c04-169">ファイルをインポートする</span><span class="sxs-lookup"><span data-stu-id="56c04-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="56c04-170">Index ページ</span><span class="sxs-lookup"><span data-stu-id="56c04-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="56c04-171">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="56c04-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="56c04-172">コンポーネントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="56c04-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="56c04-173">ログインディスプレイコンポーネント</span><span class="sxs-lookup"><span data-stu-id="56c04-173">LoginDisplay component</span></span>

<span data-ttu-id="56c04-174">コンポーネント (*共有 /ログインディスプレイ.razor*)`MainLayout`はコンポーネント (*共有/メインレイアウト.カミソリ*) でレンダリングされ、次の動作を管理します。 `LoginDisplay`</span><span class="sxs-lookup"><span data-stu-id="56c04-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="56c04-175">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="56c04-175">For authenticated users:</span></span>
  * <span data-ttu-id="56c04-176">現在のユーザー名を表示します。</span><span class="sxs-lookup"><span data-stu-id="56c04-176">Displays the current user name.</span></span>
  * <span data-ttu-id="56c04-177">コア ID のユーザー プロファイル ページへのリンクASP.NET提供します。</span><span class="sxs-lookup"><span data-stu-id="56c04-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="56c04-178">アプリからログアウトするためのボタンを提供します。</span><span class="sxs-lookup"><span data-stu-id="56c04-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="56c04-179">匿名ユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="56c04-179">For anonymous users:</span></span>
  * <span data-ttu-id="56c04-180">登録するオプションを提供します。</span><span class="sxs-lookup"><span data-stu-id="56c04-180">Offers the option to register.</span></span>
  * <span data-ttu-id="56c04-181">ログインオプションを提供します。</span><span class="sxs-lookup"><span data-stu-id="56c04-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="56c04-182">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="56c04-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="56c04-183">コンポーネントをフェッチします。</span><span class="sxs-lookup"><span data-stu-id="56c04-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="56c04-184">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="56c04-184">Run the app</span></span>

<span data-ttu-id="56c04-185">サーバー プロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="56c04-185">Run the app from the Server project.</span></span> <span data-ttu-id="56c04-186">Visual Studio を使用する場合は、**ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="56c04-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="56c04-187">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="56c04-187">Additional resources</span></span>

* [<span data-ttu-id="56c04-188">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="56c04-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
