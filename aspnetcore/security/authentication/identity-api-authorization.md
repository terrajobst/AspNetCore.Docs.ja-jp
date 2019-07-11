---
title: ASP.NET Core でのシングル ページ アプリの認証の概要
author: javiercn
description: ASP.NET Core アプリ内でホストされているシングル ページ アプリで Id を使用します。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 302a5e10a70e40e75ab9fe4b3e5a98c4e847b822
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815218"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="c6034-103">Spa の認証と承認</span><span class="sxs-lookup"><span data-stu-id="c6034-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="c6034-104">ASP.NET Core 3.0 以降では、API 認証のサポートを使用してシングル ページ アプリ (Spa) での認証を提供します。</span><span class="sxs-lookup"><span data-stu-id="c6034-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="c6034-105">認証およびユーザーを格納するための ASP.NET Core Identity が組み合わされます[IdentityServer](https://identityserver.io/) Open ID Connect を実装するためです。</span><span class="sxs-lookup"><span data-stu-id="c6034-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="c6034-106">認証パラメーターに追加された、 **Angular**と**React**プロジェクト テンプレートでの認証パラメーターに似ている**Web アプリケーション (モデル-ビュー-コント ローラー)** (MVC) および**Web アプリケーション**(Razor ページ) のプロジェクト テンプレート。</span><span class="sxs-lookup"><span data-stu-id="c6034-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="c6034-107">許可されているパラメーター値は**None**と**個々**します。</span><span class="sxs-lookup"><span data-stu-id="c6034-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="c6034-108">**React.js と Redux**プロジェクト テンプレートは、現時点での認証パラメーターをサポートしません。</span><span class="sxs-lookup"><span data-stu-id="c6034-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="c6034-109">API 認証のサポートをアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c6034-109">Create an app with API authorization support</span></span>

<span data-ttu-id="c6034-110">ユーザーの認証と承認は、Angular と React の Spa の両方で使用できます。</span><span class="sxs-lookup"><span data-stu-id="c6034-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="c6034-111">コマンド シェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c6034-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="c6034-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="c6034-112">**Angular**:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="c6034-113">**React**:</span><span class="sxs-lookup"><span data-stu-id="c6034-113">**React**:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="c6034-114">上記のコマンドで ASP.NET Core アプリを作成し、 *ClientApp* SPA を含むディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="c6034-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="c6034-115">アプリの ASP.NET Core コンポーネントの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="c6034-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="c6034-116">次のセクションでは認証のサポートが含まれる場合、プロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="c6034-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="c6034-117">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="c6034-117">Startup class</span></span>

<span data-ttu-id="c6034-118">`Startup`クラスには、次の追加。</span><span class="sxs-lookup"><span data-stu-id="c6034-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="c6034-119">内で、`Startup.ConfigureServices`メソッド。</span><span class="sxs-lookup"><span data-stu-id="c6034-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="c6034-120">既定の UI を持つ id:</span><span class="sxs-lookup"><span data-stu-id="c6034-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="c6034-121">追加された IdentityServer`AddApiAuthorization`ヘルパー メソッドそのセットアップの一部の既定設定 IdentityServer の上に ASP.NET Core の表記規則。</span><span class="sxs-lookup"><span data-stu-id="c6034-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="c6034-122">追加された認証`AddIdentityServerJwt`IdentityServer によって生成される JWT トークンを検証するアプリを構成するヘルパー メソッド。</span><span class="sxs-lookup"><span data-stu-id="c6034-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="c6034-123">内で、`Startup.Configure`メソッド。</span><span class="sxs-lookup"><span data-stu-id="c6034-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="c6034-124">要求の資格情報を検証および要求のコンテキストでユーザーを設定するため、認証ミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="c6034-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="c6034-125">Open ID Connect エンドポイントを公開する、IdentityServer ミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="c6034-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="c6034-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="c6034-126">AddApiAuthorization</span></span>

<span data-ttu-id="c6034-127">このヘルパー メソッドでは、IdentityServer は、サポートされている構成を使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="c6034-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="c6034-128">IdentityServer とは、アプリのセキュリティに関する注意事項を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="c6034-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="c6034-129">同時に、最も一般的なシナリオは不必要に複雑さを公開します。</span><span class="sxs-lookup"><span data-stu-id="c6034-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="c6034-130">そのため、一連の規則と構成オプションは、適切な開始点と見なされるに提供されます。</span><span class="sxs-lookup"><span data-stu-id="c6034-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="c6034-131">認証のニーズの変化、IdentityServer の全機能を引き続きできるよう認証をニーズに合わせてカスタマイズになります。</span><span class="sxs-lookup"><span data-stu-id="c6034-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="c6034-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="c6034-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="c6034-133">このヘルパー メソッドは、既定の認証ハンドラーとしてアプリのポリシー設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="c6034-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="c6034-134">Identity Id URL 空間で任意のサブパスにルーティングされるすべての要求を処理できるようにするポリシーが構成されている"/Identity"。</span><span class="sxs-lookup"><span data-stu-id="c6034-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="c6034-135">`JwtBearerHandler`他のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="c6034-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="c6034-136">さらに、このメソッドは、登録、 `<<ApplicationName>>API` API リソースの IdentityServer との既定のスコープを持つ`<<ApplicationName>>API`し、アプリの IdentityServer によって発行されたトークンを検証する JWT ベアラ トークン ミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="c6034-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="c6034-137">SampleDataController</span><span class="sxs-lookup"><span data-stu-id="c6034-137">SampleDataController</span></span>

<span data-ttu-id="c6034-138">*Controllers\SampleDataController.cs*ファイルに注意してください、`[Authorize]`リソースにアクセスする既定のポリシーに基づいて、ユーザーを承認する必要があることを示すクラスに適用される属性。</span><span class="sxs-lookup"><span data-stu-id="c6034-138">In the *Controllers\SampleDataController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="c6034-139">既定の承認ポリシーによって設定される既定の認証スキームを使用するように構成しようとしました`AddIdentityServerJwt`が上記のように作成したポリシーの構成、`JwtBearerHandler`によってこのようなヘルパー メソッドの既定のハンドラーを構成します。アプリに要求します。</span><span class="sxs-lookup"><span data-stu-id="c6034-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="c6034-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="c6034-140">ApplicationDbContext</span></span>

<span data-ttu-id="c6034-141">*Data\ApplicationDbContext.cs*ファイルで同じことを確認`DbContext`を拡張する例外の併用の Id に`ApiAuthorizationDbContext`(クラスから派生`IdentityDbContext`) IdentityServer は、スキーマを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c6034-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="c6034-142">データベース スキーマの完全な制御を取得するには、使用可能な Id のいずれかから継承`DbContext`クラスし、構成を呼び出すことによって Id スキーマを含めるコンテキスト`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`上、`OnModelCreating`メソッド。</span><span class="sxs-lookup"><span data-stu-id="c6034-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="c6034-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="c6034-143">OidcConfigurationController</span></span>

<span data-ttu-id="c6034-144">*Controllers\OidcConfigurationController.cs*ファイルで、クライアントが使用する必要がある OIDC パラメーターを処理するためにプロビジョニングされたエンドポイントに注意してください。</span><span class="sxs-lookup"><span data-stu-id="c6034-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="c6034-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="c6034-145">appsettings.json</span></span>

<span data-ttu-id="c6034-146">*Appsettings.json*ファイルをプロジェクトのルートが新しい`IdentityServer`セクションの一覧を記述するには、クライアントが構成されています。</span><span class="sxs-lookup"><span data-stu-id="c6034-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="c6034-147">次の例では、1 つのクライアントです。</span><span class="sxs-lookup"><span data-stu-id="c6034-147">In the following example, there's a single client.</span></span> <span data-ttu-id="c6034-148">クライアント名は、アプリ名に対応し、OAuth に規約によってマップされます`ClientId`パラメーター。</span><span class="sxs-lookup"><span data-stu-id="c6034-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="c6034-149">プロファイルでは、構成されているアプリの種類を示します。</span><span class="sxs-lookup"><span data-stu-id="c6034-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="c6034-150">サーバーの構成プロセスを簡略化されるドライブの表記規則を内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c6034-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="c6034-151">いくつかプロファイルが使用可能なで説明したように、[アプリケーション プロファイル](#application-profiles)セクション。</span><span class="sxs-lookup"><span data-stu-id="c6034-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="c6034-152">appsettings します。Development.json</span><span class="sxs-lookup"><span data-stu-id="c6034-152">appsettings.Development.json</span></span>

<span data-ttu-id="c6034-153">*Appsettings します。Development.json*プロジェクトのルートのファイルがある、`IdentityServer`トークンの署名に使用するキーについて説明したセクション。</span><span class="sxs-lookup"><span data-stu-id="c6034-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="c6034-154">説明したように、キーがプロビジョニングして、アプリと共にデプロイする必要を運用環境にデプロイするとき、[を運用環境にデプロイ](#deploy-to-production)セクション。</span><span class="sxs-lookup"><span data-stu-id="c6034-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="c6034-155">Angular アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="c6034-155">General description of the Angular app</span></span>

<span data-ttu-id="c6034-156">認証と承認の API をサポートして、Angular での Angular モジュールは独自のテンプレートが存在する、 *ClientApp\src\api 承認*ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="c6034-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="c6034-157">モジュールは、次の要素で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c6034-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="c6034-158">3 つのコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c6034-158">3 components:</span></span>
  * <span data-ttu-id="c6034-159">*login.component.ts*:アプリのログイン フローを処理します。</span><span class="sxs-lookup"><span data-stu-id="c6034-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="c6034-160">*logout.component.ts*:アプリのログアウトのフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="c6034-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="c6034-161">*login-menu.component.ts*:次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="c6034-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="c6034-162">ユーザー プロファイルの管理とリンク、ユーザーが認証されるときにログアウトします。</span><span class="sxs-lookup"><span data-stu-id="c6034-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="c6034-163">登録と、ユーザーが認証されないときのリンクでのログ。</span><span class="sxs-lookup"><span data-stu-id="c6034-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="c6034-164">ルート guard`AuthorizeGuard`をルートに追加することができ、ルートにアクセスする前に認証するユーザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="c6034-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="c6034-165">HTTP インターセプター`AuthorizeInterceptor`ユーザーが認証されるときに、API を対象とする HTTP 要求を送信するアクセス トークンをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="c6034-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="c6034-166">サービス`AuthorizeService`を認証プロセスの低レベルの詳細を処理し、使用量のアプリの残りの部分に認証されたユーザーに関する情報を公開します。</span><span class="sxs-lookup"><span data-stu-id="c6034-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="c6034-167">アプリの認証の部分に関連付けられているルートを定義する Angular モジュール。</span><span class="sxs-lookup"><span data-stu-id="c6034-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="c6034-168">ログイン] メニューの [コンポーネント、インターセプターの保護、およびサービスをアプリの他の部分で使用を公開します。</span><span class="sxs-lookup"><span data-stu-id="c6034-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="c6034-169">React のアプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="c6034-169">General description of the React app</span></span>

<span data-ttu-id="c6034-170">認証と React テンプレートでの API の承認のサポートが存在する、 *ClientApp\src\components\api 承認*ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="c6034-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="c6034-171">次の要素で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c6034-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="c6034-172">4 個のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c6034-172">4 components:</span></span>
  * <span data-ttu-id="c6034-173">*Login.js*:アプリのログイン フローを処理します。</span><span class="sxs-lookup"><span data-stu-id="c6034-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="c6034-174">*Logout.js*:アプリのログアウトのフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="c6034-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="c6034-175">*LoginMenu.js*:次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="c6034-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="c6034-176">ユーザー プロファイルの管理とリンク、ユーザーが認証されるときにログアウトします。</span><span class="sxs-lookup"><span data-stu-id="c6034-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="c6034-177">登録と、ユーザーが認証されないときのリンクでのログ。</span><span class="sxs-lookup"><span data-stu-id="c6034-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="c6034-178">*AuthorizeRoute.js*:示されているコンポーネントを表示する前に認証するユーザーを必要とするルート コンポーネント、`Component`パラメーター。</span><span class="sxs-lookup"><span data-stu-id="c6034-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="c6034-179">エクスポートした`authService`クラスのインスタンス`AuthorizeService`を認証プロセスの低レベルの詳細を処理し、使用量のアプリの残りの部分に認証されたユーザーに関する情報を公開します。</span><span class="sxs-lookup"><span data-stu-id="c6034-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="c6034-180">を、ソリューションの主要なコンポーネントを確認するには、アプリの個々 のシナリオについて詳しく説明を実行できます。</span><span class="sxs-lookup"><span data-stu-id="c6034-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="c6034-181">新しい API の承認を要求します。</span><span class="sxs-lookup"><span data-stu-id="c6034-181">Require authorization on a new API</span></span>

<span data-ttu-id="c6034-182">既定では、システムは簡単に新しい Api の承認を要求するように構成します。</span><span class="sxs-lookup"><span data-stu-id="c6034-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="c6034-183">これを行うには、新しいコント ローラーを作成し、追加、`[Authorize]`属性をコント ローラー クラスまたはコント ローラー内の任意のアクション。</span><span class="sxs-lookup"><span data-stu-id="c6034-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="c6034-184">クライアント側ルート (Angular) を保護します。</span><span class="sxs-lookup"><span data-stu-id="c6034-184">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="c6034-185">クライアント側ルートを保護すると、ルートを構成するときに実行する警備員の一覧に、承認の保護を追加することで行われます。</span><span class="sxs-lookup"><span data-stu-id="c6034-185">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="c6034-186">表示できる例として、方法、`fetch-data`ルートは、メイン アプリケーションの Angular モジュール内で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c6034-186">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="c6034-187">ルートを保護すると、実際のエンドポイントが保護しないことは言うまでも重要です (これが必要、`[Authorize]`属性が適用されて) が、認証が行われないときに、特定のクライアント側のルートに移動することから、ユーザーのみができないようにします。</span><span class="sxs-lookup"><span data-stu-id="c6034-187">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="c6034-188">API の要求 (Angular) の認証します。</span><span class="sxs-lookup"><span data-stu-id="c6034-188">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="c6034-189">アプリと共にホストされている Api に要求の認証は、アプリによって定義されている HTTP クライアントのインターセプターを使用して自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="c6034-189">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="c6034-190">クライアント側ルート (React) を保護します。</span><span class="sxs-lookup"><span data-stu-id="c6034-190">Protect a client-side route (React)</span></span>

<span data-ttu-id="c6034-191">クライアント側ルートを使用して、保護、 `AuthorizeRoute` 、プレーンではなくコンポーネント`Route`コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="c6034-191">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="c6034-192">たとえば、方法、`fetch-data`内でルートが構成されて、`App`コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="c6034-192">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="c6034-193">ルートの保護。</span><span class="sxs-lookup"><span data-stu-id="c6034-193">Protecting a route:</span></span>

* <span data-ttu-id="c6034-194">実際のエンドポイントを保護しません (これが必要、`[Authorize]`属性が適用されて)。</span><span class="sxs-lookup"><span data-stu-id="c6034-194">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="c6034-195">のみ、ユーザー、認証が行われないときに、特定のクライアント側のルートに移動できません。</span><span class="sxs-lookup"><span data-stu-id="c6034-195">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="c6034-196">API の要求 (React) の認証します。</span><span class="sxs-lookup"><span data-stu-id="c6034-196">Authenticate API requests (React)</span></span>

<span data-ttu-id="c6034-197">最初のインポートを行うを React での要求の認証、`authService`インスタンスから、`AuthorizeService`します。</span><span class="sxs-lookup"><span data-stu-id="c6034-197">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="c6034-198">アクセス トークンがから取得した、`authService`され次に示すように、要求にアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="c6034-198">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="c6034-199">React コンポーネントでは、この作業は通常で実行、`componentDidMount`ライフ サイクル メソッドまたは一部のユーザー操作から結果として。</span><span class="sxs-lookup"><span data-stu-id="c6034-199">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="c6034-200">コンポーネントへのインポート、authService</span><span class="sxs-lookup"><span data-stu-id="c6034-200">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="c6034-201">取得し、アクセス トークンを応答にアタッチ</span><span class="sxs-lookup"><span data-stu-id="c6034-201">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="c6034-202">運用環境にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="c6034-202">Deploy to production</span></span>

<span data-ttu-id="c6034-203">運用環境にアプリを展開するには、次のリソースをプロビジョニングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6034-203">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="c6034-204">Id のユーザー アカウントと、IdentityServer を格納するデータベースを許可します。</span><span class="sxs-lookup"><span data-stu-id="c6034-204">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="c6034-205">トークンの署名に使用する運用証明書。</span><span class="sxs-lookup"><span data-stu-id="c6034-205">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="c6034-206">この証明書の特定の要件はありません。自己署名証明書または CA 機関を使用してプロビジョニング証明書を指定できます。</span><span class="sxs-lookup"><span data-stu-id="c6034-206">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="c6034-207">PowerShell または OpenSSL などの標準のツールによって生成できます。</span><span class="sxs-lookup"><span data-stu-id="c6034-207">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="c6034-208">ターゲット コンピューターの証明書ストアにインストールされているまたはとしてデプロイされていることができます、 *.pfx*強力なパスワードを持つファイル。</span><span class="sxs-lookup"><span data-stu-id="c6034-208">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="c6034-209">例:Azure Websites にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="c6034-209">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="c6034-210">このセクションでは、証明書ストアに格納されている証明書を使用して Azure の web サイトにアプリのデプロイについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c6034-210">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="c6034-211">証明書ストアから証明書の読み込みにアプリを変更するには、App Service プランに以上である、Standard レベル、後の手順で構成するときに必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6034-211">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="c6034-212">アプリの*appsettings.json*ファイルで、変更、`IdentityServer`セクションにキーの詳細を含めます。</span><span class="sxs-lookup"><span data-stu-id="c6034-212">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="c6034-213">証明書の名前プロパティは、識別証明書の件名と一致します。</span><span class="sxs-lookup"><span data-stu-id="c6034-213">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="c6034-214">ストアの場所から証明書を読み込む場所を表します (`CurrentUser`または`LocalMachine`)。</span><span class="sxs-lookup"><span data-stu-id="c6034-214">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="c6034-215">ストアの名前は、証明書が格納されている証明書ストアの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="c6034-215">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="c6034-216">この場合、これは、ユーザーの個人ストアを指します。</span><span class="sxs-lookup"><span data-stu-id="c6034-216">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="c6034-217">Azure Websites にデプロイするには、次の手順では、アプリをデプロイ[アプリを Azure にデプロイ](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)を必要な Azure リソースを作成し、運用環境にアプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="c6034-217">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="c6034-218">上記の手順を実行した後、アプリは Azure にデプロイされますはまだ機能しません。</span><span class="sxs-lookup"><span data-stu-id="c6034-218">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="c6034-219">アプリによって使用される証明書は、設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6034-219">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="c6034-220">使用する証明書の拇印を見つけるしで説明されている手順に従います[証明書の読み込み](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)します。</span><span class="sxs-lookup"><span data-stu-id="c6034-220">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="c6034-221">中にこれらの手順には、SSL が言うまでは、**プライベート証明書**ポータルのセクションに、アプリで使用するプロビジョニング証明書をアップロードすることができます。</span><span class="sxs-lookup"><span data-stu-id="c6034-221">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="c6034-222">この手順の後、アプリを再起動し、機能が必要です。</span><span class="sxs-lookup"><span data-stu-id="c6034-222">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="c6034-223">他の構成オプション</span><span class="sxs-lookup"><span data-stu-id="c6034-223">Other configuration options</span></span>

<span data-ttu-id="c6034-224">API 認証のサポートは、一連の規則、既定値、および、Spa のエクスペリエンスを簡略化の機能強化を使用して、IdentityServer の上にビルドします。</span><span class="sxs-lookup"><span data-stu-id="c6034-224">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="c6034-225">もちろん、IdentityServer の能力を最大限はバック グラウンドで使用可能な ASP.NET Core 統合は、シナリオで説明されていない場合です。</span><span class="sxs-lookup"><span data-stu-id="c6034-225">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="c6034-226">ASP.NET Core のサポートは、「ファーストパーティ」アプリ、すべてのアプリを作成し、組織で展開する場所を重視されています。</span><span class="sxs-lookup"><span data-stu-id="c6034-226">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="c6034-227">そのため、同意またはフェデレーションなどのサポートが用意されていません。</span><span class="sxs-lookup"><span data-stu-id="c6034-227">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="c6034-228">これらのシナリオでは、IdentityServer を使用し、そのドキュメントに従ってください。</span><span class="sxs-lookup"><span data-stu-id="c6034-228">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="c6034-229">アプリケーション プロファイル</span><span class="sxs-lookup"><span data-stu-id="c6034-229">Application profiles</span></span>

<span data-ttu-id="c6034-230">アプリケーション プロファイルは、さらに、パラメーターを定義するアプリの定義済みの構成を示します。</span><span class="sxs-lookup"><span data-stu-id="c6034-230">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="c6034-231">この時点で、次のプロファイルがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c6034-231">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="c6034-232">`IdentityServerSPA`:1 つの単位として IdentityServer と共にホストされている SPA を表します。</span><span class="sxs-lookup"><span data-stu-id="c6034-232">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="c6034-233">`redirect_uri`の既定値は`/authentication/login-callback`します。</span><span class="sxs-lookup"><span data-stu-id="c6034-233">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="c6034-234">`post_logout_redirect_uri`の既定値は`/authentication/logout-callback`します。</span><span class="sxs-lookup"><span data-stu-id="c6034-234">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="c6034-235">スコープのセットが含まれています、 `openid`、 `profile`、Api アプリに対して定義されているすべてのスコープとします。</span><span class="sxs-lookup"><span data-stu-id="c6034-235">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="c6034-236">許可されている OIDC 応答型のセットは、`id_token token`またはそれぞれ個別に (`id_token`、 `token`)。</span><span class="sxs-lookup"><span data-stu-id="c6034-236">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="c6034-237">許可される応答モードは`fragment`します。</span><span class="sxs-lookup"><span data-stu-id="c6034-237">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="c6034-238">`SPA` :IdentityServer とされていないホストされている SPA を表します。</span><span class="sxs-lookup"><span data-stu-id="c6034-238">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="c6034-239">スコープのセットが含まれています、 `openid`、 `profile`、Api アプリに対して定義されているすべてのスコープとします。</span><span class="sxs-lookup"><span data-stu-id="c6034-239">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="c6034-240">許可されている OIDC 応答型のセットは、`id_token token`またはそれぞれ個別に (`id_token`、 `token`)。</span><span class="sxs-lookup"><span data-stu-id="c6034-240">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="c6034-241">許可される応答モードは`fragment`します。</span><span class="sxs-lookup"><span data-stu-id="c6034-241">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="c6034-242">`IdentityServerJwt`:IdentityServer とと共にホストされている API を表します。</span><span class="sxs-lookup"><span data-stu-id="c6034-242">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="c6034-243">既定値は、アプリ名を 1 つのスコープを持つアプリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="c6034-243">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="c6034-244">`API`:IdentityServer でホストされていない API を表します。</span><span class="sxs-lookup"><span data-stu-id="c6034-244">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="c6034-245">既定値は、アプリ名を 1 つのスコープを持つアプリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="c6034-245">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="c6034-246">AppSettings を使用した構成</span><span class="sxs-lookup"><span data-stu-id="c6034-246">Configuration through AppSettings</span></span>

<span data-ttu-id="c6034-247">一覧に追加することで、構成システムでアプリを構成する`Clients`または`Resources`します。</span><span class="sxs-lookup"><span data-stu-id="c6034-247">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="c6034-248">各クライアントの構成`redirect_uri`と`post_logout_redirect_uri`プロパティは、次の例に示すようにします。</span><span class="sxs-lookup"><span data-stu-id="c6034-248">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="c6034-249">リソースを構成するときに、次に示すように、リソースのスコープを構成できます。</span><span class="sxs-lookup"><span data-stu-id="c6034-249">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="c6034-250">コードを使用した構成</span><span class="sxs-lookup"><span data-stu-id="c6034-250">Configuration through code</span></span>

<span data-ttu-id="c6034-251">クライアントとのオーバー ロードを使用してコードを使用したリソースを構成することも`AddApiAuthorization`オプションを構成するアクションを取得します。</span><span class="sxs-lookup"><span data-stu-id="c6034-251">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c6034-252">その他の資料</span><span class="sxs-lookup"><span data-stu-id="c6034-252">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
