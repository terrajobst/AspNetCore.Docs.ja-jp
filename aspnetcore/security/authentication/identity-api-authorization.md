---
title: ASP.NET Core でのシングルページアプリの認証の概要
author: javiercn
description: ASP.NET Core アプリ内でホストされるシングルページアプリで Id を使用します。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 5ed5fb61e5989b291523332c6a2ec332f9ca0f6b
ms.sourcegitcommit: e5d4768aaf85703effb4557a520d681af8284e26
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616613"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="1af34-103">SPAs の認証と承認</span><span class="sxs-lookup"><span data-stu-id="1af34-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="1af34-104">ASP.NET Core 3.0 以降では、API 承認のサポートを使用して、シングルページアプリ (spa) で認証を提供します。</span><span class="sxs-lookup"><span data-stu-id="1af34-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="1af34-105">ユーザーを認証および格納するための ASP.NET Core Id は、Open ID Connect を実装する[ために、ユーザーと組み合わせ](https://identityserver.io/)て使用されます。</span><span class="sxs-lookup"><span data-stu-id="1af34-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="1af34-106">認証パラメーターが、 **Web アプリケーション (モデルビューコントローラー)** (MVC) と**web アプリケーション**(Razor Pages) の認証パラメーターに似た**角度**で、**応答**するプロジェクトテンプレートに追加されました。プロジェクトテンプレート。</span><span class="sxs-lookup"><span data-stu-id="1af34-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="1af34-107">許可されるパラメーター値は、 **None**および**個人**です。</span><span class="sxs-lookup"><span data-stu-id="1af34-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="1af34-108">この時点では、対応する **.js および Redux**プロジェクトテンプレートで認証パラメーターがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1af34-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="1af34-109">API authorization サポートを使用してアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="1af34-109">Create an app with API authorization support</span></span>

<span data-ttu-id="1af34-110">ユーザーの認証と承認は、両方の角度で使用でき、SPAs として対応します。</span><span class="sxs-lookup"><span data-stu-id="1af34-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="1af34-111">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1af34-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="1af34-112">**角度**:</span><span class="sxs-lookup"><span data-stu-id="1af34-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="1af34-113">**反応**:</span><span class="sxs-lookup"><span data-stu-id="1af34-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="1af34-114">前のコマンドは、 *ClientApp*ディレクトリに SPA を含む ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="1af34-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="1af34-115">アプリの ASP.NET Core コンポーネントの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="1af34-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="1af34-116">次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="1af34-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="1af34-117">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="1af34-117">Startup class</span></span>

<span data-ttu-id="1af34-118">`Startup` クラスには、次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="1af34-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="1af34-119">`Startup.ConfigureServices` メソッド内:</span><span class="sxs-lookup"><span data-stu-id="1af34-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="1af34-120">既定の UI を使用した id:</span><span class="sxs-lookup"><span data-stu-id="1af34-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="1af34-121">次のように、追加の `AddApiAuthorization` ヘルパーメソッドを使用して、サーバーの上に既定の ASP.NET Core 規則を設定します。</span><span class="sxs-lookup"><span data-stu-id="1af34-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="1af34-122">追加の `AddIdentityServerJwt` ヘルパーメソッドを使用した認証。次のように、アプリを構成して、サーバーによって生成された JWT トークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="1af34-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="1af34-123">`Startup.Configure` メソッド内:</span><span class="sxs-lookup"><span data-stu-id="1af34-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="1af34-124">要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="1af34-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="1af34-125">Open ID Connect エンドポイントを公開するサーバーミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="1af34-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="1af34-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="1af34-126">AddApiAuthorization</span></span>

<span data-ttu-id="1af34-127">このヘルパーメソッドは、サポートされる構成を使用するようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="1af34-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="1af34-128">サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="1af34-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="1af34-129">同時に、最も一般的なシナリオでは不必要な複雑さを公開します。</span><span class="sxs-lookup"><span data-stu-id="1af34-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="1af34-130">そのため、適切な出発点と見なされる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="1af34-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="1af34-131">認証を変更する必要がある場合でも、ユーザーのニーズに合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="1af34-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="1af34-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="1af34-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="1af34-133">このヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="1af34-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="1af34-134">Id URL 空間 "/identity" のサブパスにルーティングされるすべての要求を Id で処理できるように、ポリシーが構成されています。</span><span class="sxs-lookup"><span data-stu-id="1af34-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="1af34-135">`JwtBearerHandler` は、他のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="1af34-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="1af34-136">さらに、このメソッドは、`<<ApplicationName>>API` API リソースを、既定のスコープである `<<ApplicationName>>API` を使用して登録し、JWT ベアラートークンミドルウェアを構成して、アプリのために、サービスによって発行されたトークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="1af34-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="1af34-137">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="1af34-137">WeatherForecastController</span></span>

<span data-ttu-id="1af34-138">*Controllers\WeatherForecastController.cs*ファイルで、リソースにアクセスするための既定のポリシーに基づいてユーザーを承認する必要があることを示す `[Authorize]` 属性がクラスに適用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="1af34-138">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="1af34-139">既定の承認ポリシーは、既定の認証スキームを使用するように構成されます。これは、前述のポリシースキームに `AddIdentityServerJwt` によって設定されます。このようなヘルパーメソッドによって構成された `JwtBearerHandler` は、要求の既定のハンドラーになります。アプリ。</span><span class="sxs-lookup"><span data-stu-id="1af34-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="1af34-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="1af34-140">ApplicationDbContext</span></span>

<span data-ttu-id="1af34-141">*Data\ApplicationDbContext.cs*ファイルで、id に同じ `DbContext` が使用されていることに注意してください `ApiAuthorizationDbContext` (`IdentityDbContext`から派生したクラス) を使用して、ユーザーのスキーマを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1af34-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="1af34-142">データベーススキーマを完全に制御するには、使用可能な Id `DbContext` クラスの1つを継承し、`OnModelCreating` メソッドで `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` を呼び出して、Id スキーマを含めるようにコンテキストを構成します。</span><span class="sxs-lookup"><span data-stu-id="1af34-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="1af34-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="1af34-143">OidcConfigurationController</span></span>

<span data-ttu-id="1af34-144">*Controllers\OidcConfigurationController.cs*ファイルで、クライアントが使用する必要のある oidc パラメーターを提供するためにプロビジョニングされたエンドポイントを確認します。</span><span class="sxs-lookup"><span data-stu-id="1af34-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1af34-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="1af34-145">appsettings.json</span></span>

<span data-ttu-id="1af34-146">プロジェクトルートの*appsettings*ファイルには、構成されたクライアントの一覧を説明する新しい `IdentityServer` セクションがあります。</span><span class="sxs-lookup"><span data-stu-id="1af34-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="1af34-147">次の例には、1つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="1af34-147">In the following example, there's a single client.</span></span> <span data-ttu-id="1af34-148">クライアント名はアプリケーション名に対応し、OAuth `ClientId` パラメーターに規約によってマップされます。</span><span class="sxs-lookup"><span data-stu-id="1af34-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="1af34-149">プロファイルは、構成されているアプリの種類を示します。</span><span class="sxs-lookup"><span data-stu-id="1af34-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="1af34-150">サーバーの構成プロセスを簡略化する規則を実現するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="1af34-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="1af34-151">「[アプリケーションプロファイル](#application-profiles)」セクションで説明されているように、使用可能なプロファイルがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="1af34-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="1af34-152">appsettings.開発. json</span><span class="sxs-lookup"><span data-stu-id="1af34-152">appsettings.Development.json</span></span>

<span data-ttu-id="1af34-153">Appsettings で *。プロジェクトルートの開発用 json*ファイルには、トークンの署名に使用されるキーについて説明する `IdentityServer` セクションがあります。</span><span class="sxs-lookup"><span data-stu-id="1af34-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="1af34-154">運用環境にデプロイする場合は、「[運用環境にデプロイする](#deploy-to-production)」セクションで説明されているように、アプリと共にキーをプロビジョニングしてデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1af34-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="1af34-155">角度アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="1af34-155">General description of the Angular app</span></span>

<span data-ttu-id="1af34-156">角度テンプレートでの認証と API 承認のサポートは、独自の角度モジュールの*Clientapp-authorization*ディレクトリに存在します。</span><span class="sxs-lookup"><span data-stu-id="1af34-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="1af34-157">モジュールは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="1af34-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="1af34-158">3個のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="1af34-158">3 components:</span></span>
  * <span data-ttu-id="1af34-159">*login. component. ts*: アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="1af34-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="1af34-160">*logout*: アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="1af34-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="1af34-161">*login-menu. component. ts*: 次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="1af34-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="1af34-162">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="1af34-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="1af34-163">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="1af34-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="1af34-164">ルートに追加することができ、ルートにアクセスする前にユーザーを認証する必要があるルートガード `AuthorizeGuard`。</span><span class="sxs-lookup"><span data-stu-id="1af34-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="1af34-165">ユーザーが認証されるときに、API を対象とする発信 HTTP 要求にアクセストークンを結び付ける HTTP インターセプター `AuthorizeInterceptor`。</span><span class="sxs-lookup"><span data-stu-id="1af34-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="1af34-166">認証プロセスの下位レベルの詳細を処理し、認証されたユーザーに関する情報をアプリの残りの部分に公開するサービス `AuthorizeService`。</span><span class="sxs-lookup"><span data-stu-id="1af34-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="1af34-167">アプリの認証部分に関連付けられているルートを定義する角度モジュール。</span><span class="sxs-lookup"><span data-stu-id="1af34-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="1af34-168">ログインメニューコンポーネント、インターセプター、ガード、およびアプリの残りの部分から使用するためのサービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="1af34-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="1af34-169">反応アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="1af34-169">General description of the React app</span></span>

<span data-ttu-id="1af34-170">応答テンプレートでの認証と API 承認のサポートは、 *ClientApp\src\components\api-authorization*ディレクトリにあります。</span><span class="sxs-lookup"><span data-stu-id="1af34-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="1af34-171">これは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="1af34-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="1af34-172">4つのコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="1af34-172">4 components:</span></span>
  * <span data-ttu-id="1af34-173">*.Js*: アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="1af34-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="1af34-174">*Logout*: アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="1af34-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="1af34-175">*Loginmenu js*: 次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="1af34-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="1af34-176">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="1af34-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="1af34-177">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="1af34-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="1af34-178">*AuthorizeRoute*: `Component` パラメーターに示されているコンポーネントを表示する前に、ユーザーを認証する必要があるルートコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="1af34-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="1af34-179">認証プロセスの下位レベルの詳細を処理し、認証されたユーザーに関する情報をアプリの残りの部分に公開する、クラス `AuthorizeService` のエクスポートされた `authService` インスタンス。</span><span class="sxs-lookup"><span data-stu-id="1af34-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="1af34-180">これで、ソリューションの主要なコンポーネントを確認できました。次は、アプリの個々のシナリオについて詳しく見ていきましょう。</span><span class="sxs-lookup"><span data-stu-id="1af34-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="1af34-181">新しい API での承認が必要</span><span class="sxs-lookup"><span data-stu-id="1af34-181">Require authorization on a new API</span></span>

<span data-ttu-id="1af34-182">既定では、システムは新しい Api の承認を要求するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="1af34-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="1af34-183">これを行うには、新しいコントローラーを作成し、コントローラークラスまたはコントローラー内の任意のアクションに `[Authorize]` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="1af34-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="1af34-184">API 認証ハンドラーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="1af34-184">Customize the API authentication handler</span></span>

<span data-ttu-id="1af34-185">API の JWT ハンドラーの構成をカスタマイズするには、その <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> インスタンスを構成します。</span><span class="sxs-lookup"><span data-stu-id="1af34-185">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="1af34-186">クライアント側のルートを保護する (角度)</span><span class="sxs-lookup"><span data-stu-id="1af34-186">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="1af34-187">クライアント側ルートの保護は、ルートを構成するときに実行するガードのリストに承認ガードを追加することによって行われます。</span><span class="sxs-lookup"><span data-stu-id="1af34-187">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="1af34-188">例として、`fetch-data` ルートがメインアプリの角度モジュール内でどのように構成されているかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="1af34-188">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="1af34-189">ルートを保護しても実際のエンドポイントが保護されないことに注意してください (これには `[Authorize]` 属性が適用されている必要があります) が、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1af34-189">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="1af34-190">API 要求の認証 (角度)</span><span class="sxs-lookup"><span data-stu-id="1af34-190">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="1af34-191">アプリと共にホストされる Api に対する要求の認証は、アプリによって定義された HTTP クライアントインターセプターを使用することによって自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="1af34-191">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="1af34-192">クライアント側のルートを保護する (応答)</span><span class="sxs-lookup"><span data-stu-id="1af34-192">Protect a client-side route (React)</span></span>

<span data-ttu-id="1af34-193">プレーン `Route` コンポーネントの代わりに `AuthorizeRoute` コンポーネントを使用して、クライアント側のルートを保護します。</span><span class="sxs-lookup"><span data-stu-id="1af34-193">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="1af34-194">たとえば、`App` コンポーネント内で `fetch-data` ルートがどのように構成されているかに注目してください。</span><span class="sxs-lookup"><span data-stu-id="1af34-194">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="1af34-195">ルートの保護:</span><span class="sxs-lookup"><span data-stu-id="1af34-195">Protecting a route:</span></span>

* <span data-ttu-id="1af34-196">では、実際のエンドポイントは保護されません (`[Authorize]` 属性も適用する必要があります)。</span><span class="sxs-lookup"><span data-stu-id="1af34-196">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="1af34-197">は、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="1af34-197">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="1af34-198">API 要求の認証 (応答)</span><span class="sxs-lookup"><span data-stu-id="1af34-198">Authenticate API requests (React)</span></span>

<span data-ttu-id="1af34-199">応答を含む要求の認証は、最初に `AuthorizeService`から `authService` インスタンスをインポートすることによって行われます。</span><span class="sxs-lookup"><span data-stu-id="1af34-199">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="1af34-200">次に示すように、アクセストークンは `authService` から取得され、要求にアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="1af34-200">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="1af34-201">コンポーネントの処理では、通常、この作業は `componentDidMount` ライフサイクルメソッドで実行されるか、一部のユーザー操作の結果として行われます。</span><span class="sxs-lookup"><span data-stu-id="1af34-201">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="1af34-202">コンポーネントに authService をインポートする</span><span class="sxs-lookup"><span data-stu-id="1af34-202">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="1af34-203">アクセストークンを取得して応答にアタッチする</span><span class="sxs-lookup"><span data-stu-id="1af34-203">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="1af34-204">運用環境へのデプロイ</span><span class="sxs-lookup"><span data-stu-id="1af34-204">Deploy to production</span></span>

<span data-ttu-id="1af34-205">アプリを運用環境にデプロイするには、次のリソースをプロビジョニングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1af34-205">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="1af34-206">Id ユーザーアカウントとサーバー権限を格納するデータベース。</span><span class="sxs-lookup"><span data-stu-id="1af34-206">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="1af34-207">トークンの署名に使用する実稼働証明書。</span><span class="sxs-lookup"><span data-stu-id="1af34-207">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="1af34-208">この証明書には特定の要件はありません。自己署名証明書、または CA 証明機関を通じてプロビジョニングされた証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="1af34-208">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="1af34-209">PowerShell や OpenSSL などの標準ツールを使用して生成できます。</span><span class="sxs-lookup"><span data-stu-id="1af34-209">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="1af34-210">ターゲットコンピューターの証明書ストアにインストールすることも、強力なパスワードを使用して *.pfx*ファイルとして展開することもできます。</span><span class="sxs-lookup"><span data-stu-id="1af34-210">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="1af34-211">例: Azure Websites へのデプロイ</span><span class="sxs-lookup"><span data-stu-id="1af34-211">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="1af34-212">このセクションでは、証明書ストアに格納されている証明書を使用して、Azure websites にアプリをデプロイする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1af34-212">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="1af34-213">証明書ストアから証明書を読み込むようにアプリを変更するには、後の手順でを構成するときに、App Service プランが少なくとも標準レベルになっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="1af34-213">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="1af34-214">アプリの*appsettings*ファイルで、`IdentityServer` セクションを変更して、キーの詳細を含めます。</span><span class="sxs-lookup"><span data-stu-id="1af34-214">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="1af34-215">Certificate の name プロパティは、証明書の識別サブジェクトに対応します。</span><span class="sxs-lookup"><span data-stu-id="1af34-215">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="1af34-216">ストアの場所は、証明書の読み込み元 (`CurrentUser` または `LocalMachine`) を表します。</span><span class="sxs-lookup"><span data-stu-id="1af34-216">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="1af34-217">ストア名は、証明書が格納されている証明書ストアの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="1af34-217">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="1af34-218">この場合、個人ユーザーストアを指します。</span><span class="sxs-lookup"><span data-stu-id="1af34-218">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="1af34-219">Azure Websites にデプロイするには、「 [azure へのアプリのデプロイ](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)」の手順に従ってアプリをデプロイし、必要な azure リソースを作成して、アプリを運用環境にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="1af34-219">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="1af34-220">前述の手順に従うと、アプリは Azure にデプロイされますが、まだ機能していません。</span><span class="sxs-lookup"><span data-stu-id="1af34-220">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="1af34-221">アプリによって使用される証明書は、まだセットアップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1af34-221">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="1af34-222">使用する証明書のサムプリントを見つけて、「[証明書の読み込み](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)」で説明されている手順に従います。</span><span class="sxs-lookup"><span data-stu-id="1af34-222">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="1af34-223">これらの手順では SSL について説明していますが、ポータルには **[プライベート証明書]** セクションがあります。このセクションでは、アプリで使用するプロビジョニング済みの証明書をアップロードできます。</span><span class="sxs-lookup"><span data-stu-id="1af34-223">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="1af34-224">この手順の後にアプリを再起動すると、アプリが機能するようになります。</span><span class="sxs-lookup"><span data-stu-id="1af34-224">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="1af34-225">その他の構成オプション</span><span class="sxs-lookup"><span data-stu-id="1af34-225">Other configuration options</span></span>

<span data-ttu-id="1af34-226">API 承認のサポートは、一連の規則、既定値、および拡張機能を使用して、サーバー上に構築されます。これにより、SPAs のエクスペリエンスが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="1af34-226">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="1af34-227">言うまでもありませんが、ASP.NET Core 統合によって実際のシナリオがカバーされていない場合は、サーバーの全機能をバックグラウンドで利用できます。</span><span class="sxs-lookup"><span data-stu-id="1af34-227">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="1af34-228">ASP.NET Core サポートは、すべてのアプリが組織によって作成および展開される "ファーストパーティ" アプリに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="1af34-228">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="1af34-229">そのため、同意やフェデレーションなどのサポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="1af34-229">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="1af34-230">これらのシナリオでは、ユーザーを使用して、そのドキュメントに従ってください。</span><span class="sxs-lookup"><span data-stu-id="1af34-230">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="1af34-231">アプリケーションプロファイル</span><span class="sxs-lookup"><span data-stu-id="1af34-231">Application profiles</span></span>

<span data-ttu-id="1af34-232">アプリケーションプロファイルは、そのパラメーターをさらに定義するアプリの事前定義された構成です。</span><span class="sxs-lookup"><span data-stu-id="1af34-232">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="1af34-233">現時点では、次のプロファイルがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1af34-233">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="1af34-234">`IdentityServerSPA`: 1 つの単位としてホストされる SPA サーバーを表します。</span><span class="sxs-lookup"><span data-stu-id="1af34-234">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="1af34-235">`redirect_uri` の既定値は `/authentication/login-callback`です。</span><span class="sxs-lookup"><span data-stu-id="1af34-235">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="1af34-236">`post_logout_redirect_uri` の既定値は `/authentication/logout-callback`です。</span><span class="sxs-lookup"><span data-stu-id="1af34-236">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="1af34-237">スコープのセットには、アプリ内の Api に対して定義されている、`openid`、`profile`、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1af34-237">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="1af34-238">許可される OIDC 応答の種類のセットは、`id_token token` またはそれぞれ個別に (`id_token`、`token`) 使用されます。</span><span class="sxs-lookup"><span data-stu-id="1af34-238">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="1af34-239">許可される応答モードは `fragment`です。</span><span class="sxs-lookup"><span data-stu-id="1af34-239">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="1af34-240">`SPA`: は、サーバーでホストされていない SPA を表します。</span><span class="sxs-lookup"><span data-stu-id="1af34-240">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="1af34-241">スコープのセットには、アプリ内の Api に対して定義されている、`openid`、`profile`、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1af34-241">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="1af34-242">許可される OIDC 応答の種類のセットは、`id_token token` またはそれぞれ個別に (`id_token`、`token`) 使用されます。</span><span class="sxs-lookup"><span data-stu-id="1af34-242">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="1af34-243">許可される応答モードは `fragment`です。</span><span class="sxs-lookup"><span data-stu-id="1af34-243">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="1af34-244">`IdentityServerJwt`: サービスと共にホストされる API を表します。</span><span class="sxs-lookup"><span data-stu-id="1af34-244">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="1af34-245">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="1af34-245">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="1af34-246">`API`: は、サーバーでホストされていない API を表します。</span><span class="sxs-lookup"><span data-stu-id="1af34-246">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="1af34-247">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="1af34-247">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="1af34-248">AppSettings を使用した構成</span><span class="sxs-lookup"><span data-stu-id="1af34-248">Configuration through AppSettings</span></span>

<span data-ttu-id="1af34-249">構成システムを使用してアプリを構成するには、`Clients` または `Resources`の一覧にアプリを追加します。</span><span class="sxs-lookup"><span data-stu-id="1af34-249">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="1af34-250">次の例に示すように、各クライアントの `redirect_uri` と `post_logout_redirect_uri` プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="1af34-250">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="1af34-251">リソースを構成するときは、次に示すように、リソースのスコープを構成できます。</span><span class="sxs-lookup"><span data-stu-id="1af34-251">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="1af34-252">コードを使用した構成</span><span class="sxs-lookup"><span data-stu-id="1af34-252">Configuration through code</span></span>

<span data-ttu-id="1af34-253">また、オプションを構成するアクションを実行する `AddApiAuthorization` のオーバーロードを使用して、コードを使用してクライアントとリソースを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="1af34-253">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="1af34-254">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1af34-254">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
