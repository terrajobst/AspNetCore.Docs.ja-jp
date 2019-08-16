---
title: ASP.NET Core でのシングルページアプリの認証の概要
author: javiercn
description: ASP.NET Core アプリ内でホストされるシングルページアプリで Id を使用します。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 08/05/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 6b8818cc89a87e66ecec445ff8071348aacde64a
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819921"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="4ca3e-103">SPAs の認証と承認</span><span class="sxs-lookup"><span data-stu-id="4ca3e-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="4ca3e-104">ASP.NET Core 3.0 以降では、API 承認のサポートを使用して、シングルページアプリ (spa) で認証を提供します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="4ca3e-105">ユーザーを認証および格納するための ASP.NET Core Id は、Open ID Connect を実装するために、ユーザーと [IdentityServer](https://identityserver.io/) を組み合わせて使用されます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="4ca3e-106">認証パラメーターが、 **Web アプリケーション (モデルビューコントローラー)** (MVC) と**web アプリケーション**(Razor Pages) の認証パラメーターに似た**角度**で、**応答**するプロジェクトテンプレートに追加されました。プロジェクトテンプレート。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="4ca3e-107">許可されるパラメーター値は、 **None**および**個人**です。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="4ca3e-108">この時点では、対応する **.js および Redux**プロジェクトテンプレートで認証パラメーターがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="4ca3e-109">API authorization サポートを使用してアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="4ca3e-109">Create an app with API authorization support</span></span>

<span data-ttu-id="4ca3e-110">ユーザーの認証と承認は、両方の角度で使用でき、SPAs として対応します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="4ca3e-111">コマンド シェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="4ca3e-112">**角度**:</span><span class="sxs-lookup"><span data-stu-id="4ca3e-112">**Angular**:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="4ca3e-113">**反応**:</span><span class="sxs-lookup"><span data-stu-id="4ca3e-113">**React**:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="4ca3e-114">前のコマンドは、 *ClientApp*ディレクトリに SPA を含む ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="4ca3e-115">アプリの ASP.NET Core コンポーネントの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="4ca3e-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="4ca3e-116">次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="4ca3e-117">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="4ca3e-117">Startup class</span></span>

<span data-ttu-id="4ca3e-118">クラス`Startup`には、次の追加機能があります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="4ca3e-119">`Startup.ConfigureServices`メソッド内:</span><span class="sxs-lookup"><span data-stu-id="4ca3e-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="4ca3e-120">既定の UI を使用した id:</span><span class="sxs-lookup"><span data-stu-id="4ca3e-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="4ca3e-121">次のように、 `AddApiAuthorization`既定の ASP.NET Core 規則を設定する追加のヘルパーメソッドを使用して、サーバーに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="4ca3e-122">次のように`AddIdentityServerJwt`追加のヘルパーメソッドを使用して認証します。これにより、アプリは、サービスによって生成された JWT トークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="4ca3e-123">`Startup.Configure`メソッド内:</span><span class="sxs-lookup"><span data-stu-id="4ca3e-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="4ca3e-124">要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="4ca3e-125">Open ID Connect エンドポイントを公開するサーバーミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="4ca3e-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="4ca3e-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="4ca3e-126">AddApiAuthorization</span></span>

<span data-ttu-id="4ca3e-127">このヘルパーメソッドは、サポートされる構成を使用するようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="4ca3e-128">サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="4ca3e-129">同時に、最も一般的なシナリオでは不必要な複雑さを公開します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="4ca3e-130">そのため、適切な出発点と見なされる一連の規則と構成オプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="4ca3e-131">認証を変更する必要がある場合でも、ユーザーのニーズに合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="4ca3e-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="4ca3e-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="4ca3e-133">このヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="4ca3e-134">Id URL 空間 "/identity" のサブパスにルーティングされるすべての要求を Id で処理できるように、ポリシーが構成されています。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="4ca3e-135">は`JwtBearerHandler` 、他のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="4ca3e-136">さらに、このメソッドは`<<ApplicationName>>API` API リソースをの既定の`<<ApplicationName>>API`スコープに登録し、JWT ベアラートークンミドルウェアを構成して、アプリのために、サービスによって発行されたトークンを検証します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="4ca3e-137">SampleDataController</span><span class="sxs-lookup"><span data-stu-id="4ca3e-137">SampleDataController</span></span>

<span data-ttu-id="4ca3e-138">*Controllers\SampleDataController.cs*ファイルで、属性が`[Authorize]`クラスに適用されていることを確認します。これは、リソースにアクセスするための既定のポリシーに基づいてユーザーを承認する必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-138">In the *Controllers\SampleDataController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="4ca3e-139">既定の承認ポリシーは、前に説明したポリシースキームによって`AddIdentityServerJwt`設定される既定の認証スキームを使用するように構成され、このようなヘルパーメソッドによって構成されたは`JwtBearerHandler` 、の既定のハンドラーになります。アプリへの要求。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="4ca3e-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="4ca3e-140">ApplicationDbContext</span></span>

<span data-ttu-id="4ca3e-141">*Data\ApplicationDbContext.cs*ファイルで`DbContext`は、id で拡張`ApiAuthorizationDbContext`された例外 (から`IdentityDbContext`派生したクラス) を使用して、ユーザーのスキーマを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="4ca3e-142">データベーススキーマを完全に制御するには、使用可能な id `DbContext`クラスの1つを継承し、 `OnModelCreating`メソッドでを呼び出し`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`て、id スキーマを含めるようにコンテキストを構成します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="4ca3e-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="4ca3e-143">OidcConfigurationController</span></span>

<span data-ttu-id="4ca3e-144">*Controllers\OidcConfigurationController.cs*ファイルで、クライアントが使用する必要のある oidc パラメーターを提供するためにプロビジョニングされたエンドポイントを確認します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4ca3e-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="4ca3e-145">appsettings.json</span></span>

<span data-ttu-id="4ca3e-146">プロジェクトルートの*appsettings*ファイルには、構成されたクライアントの一覧`IdentityServer`を説明する新しいセクションがあります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="4ca3e-147">次の例には、1つのクライアントがあります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-147">In the following example, there's a single client.</span></span> <span data-ttu-id="4ca3e-148">クライアント名はアプリケーション名に対応し、OAuth `ClientId`パラメーターに規約によってマップされます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="4ca3e-149">プロファイルは、構成されているアプリの種類を示します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="4ca3e-150">サーバーの構成プロセスを簡略化する規則を実現するために、内部的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="4ca3e-151">「[アプリケーションプロファイル](#application-profiles)」セクションで説明されているように、使用可能なプロファイルがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="4ca3e-152">appsettings.開発. json</span><span class="sxs-lookup"><span data-stu-id="4ca3e-152">appsettings.Development.json</span></span>

<span data-ttu-id="4ca3e-153">Appsettings で *。プロジェクトルートの開発用 json*ファイルには、トークンの`IdentityServer`署名に使用されるキーについて説明するセクションがあります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="4ca3e-154">運用環境にデプロイする場合は、「[運用環境にデプロイする](#deploy-to-production)」セクションで説明されているように、アプリと共にキーをプロビジョニングしてデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="4ca3e-155">角度アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="4ca3e-155">General description of the Angular app</span></span>

<span data-ttu-id="4ca3e-156">角度テンプレートでの認証と API 承認のサポートは、独自の角度モジュールの*Clientapp-authorization*ディレクトリに存在します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="4ca3e-157">モジュールは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="4ca3e-158">3個のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="4ca3e-158">3 components:</span></span>
  * <span data-ttu-id="4ca3e-159">*login. component. ts*:アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="4ca3e-160">*logout. component. ts*:アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="4ca3e-161">*ログイン-メニュー. component. ts*:次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="4ca3e-162">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="4ca3e-163">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="4ca3e-164">ルートに追加`AuthorizeGuard`できるルートガード。ルートにアクセスする前にユーザーを認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="4ca3e-165">ユーザーが認証`AuthorizeInterceptor`されるときに、API を対象とする発信 http 要求にアクセストークンを関連付ける http インターセプター。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="4ca3e-166">認証プロセス`AuthorizeService`の下位レベルの詳細を処理し、認証されたユーザーに関する情報を、使用するアプリの残りの部分に公開するサービス。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="4ca3e-167">アプリの認証部分に関連付けられているルートを定義する角度モジュール。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="4ca3e-168">ログインメニューコンポーネント、インターセプター、ガード、およびアプリの残りの部分から使用するためのサービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="4ca3e-169">反応アプリの一般的な説明</span><span class="sxs-lookup"><span data-stu-id="4ca3e-169">General description of the React app</span></span>

<span data-ttu-id="4ca3e-170">応答テンプレートでの認証と API 承認のサポートは、 *ClientApp\src\components\api-authorization*ディレクトリにあります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="4ca3e-171">これは、次の要素で構成されています。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="4ca3e-172">4つのコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="4ca3e-172">4 components:</span></span>
  * <span data-ttu-id="4ca3e-173">*.Js*:アプリのログインフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="4ca3e-174">*Logout*:アプリのログアウトフローを処理します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="4ca3e-175">*Loginmenu js*:次のリンクのセットのいずれかを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="4ca3e-176">ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="4ca3e-177">ユーザーが認証されていない場合の登録とログインリンク。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="4ca3e-178">*AuthorizeRoute*:`Component`パラメーターで指定されたコンポーネントを表示する前に、ユーザーを認証する必要があるルートコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="4ca3e-179">認証プロセス`authService`の下位レベル`AuthorizeService`の詳細を処理し、認証されたユーザーに関する情報をアプリの残りの部分に公開する、クラスのエクスポートされたインスタンス。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="4ca3e-180">これで、ソリューションの主要なコンポーネントを確認できました。次は、アプリの個々のシナリオについて詳しく見ていきましょう。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="4ca3e-181">新しい API での承認が必要</span><span class="sxs-lookup"><span data-stu-id="4ca3e-181">Require authorization on a new API</span></span>

<span data-ttu-id="4ca3e-182">既定では、システムは新しい Api の承認を要求するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="4ca3e-183">これを行うには、新しいコントローラーを作成し`[Authorize]` 、コントローラークラスまたはコントローラー内の任意のアクションに属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="4ca3e-184">クライアント側のルートを保護する (角度)</span><span class="sxs-lookup"><span data-stu-id="4ca3e-184">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="4ca3e-185">クライアント側ルートの保護は、ルートを構成するときに実行するガードのリストに承認ガードを追加することによって行われます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-185">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="4ca3e-186">例として、主要なアプリの`fetch-data`角度モジュール内でルートがどのように構成されているかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-186">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="4ca3e-187">ルートを保護しても実際のエンドポイントが保護されないことに注意し`[Authorize]`てください (まだ属性が適用されている必要があります) が、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-187">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="4ca3e-188">API 要求の認証 (角度)</span><span class="sxs-lookup"><span data-stu-id="4ca3e-188">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="4ca3e-189">アプリと共にホストされる Api に対する要求の認証は、アプリによって定義された HTTP クライアントインターセプターを使用することによって自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-189">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="4ca3e-190">クライアント側のルートを保護する (応答)</span><span class="sxs-lookup"><span data-stu-id="4ca3e-190">Protect a client-side route (React)</span></span>

<span data-ttu-id="4ca3e-191">`AuthorizeRoute` プレーン`Route`コンポーネントの代わりにコンポーネントを使用して、クライアント側のルートを保護します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-191">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="4ca3e-192">たとえば、 `fetch-data` `App`コンポーネント内でルートがどのように構成されているかに注目してください。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-192">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="4ca3e-193">ルートの保護:</span><span class="sxs-lookup"><span data-stu-id="4ca3e-193">Protecting a route:</span></span>

* <span data-ttu-id="4ca3e-194">は実際のエンドポイントを保護しません`[Authorize]` (まだ属性が適用されている必要があります)。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-194">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="4ca3e-195">は、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-195">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="4ca3e-196">API 要求の認証 (応答)</span><span class="sxs-lookup"><span data-stu-id="4ca3e-196">Authenticate API requests (React)</span></span>

<span data-ttu-id="4ca3e-197">を使用して要求を認証するには`authService` 、 `AuthorizeService`まずからインスタンスをインポートします。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-197">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="4ca3e-198">アクセストークンはから`authService`取得され、次に示すように要求にアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-198">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="4ca3e-199">コンポーネントの反応では、通常、この作業は`componentDidMount`ライフサイクルメソッドで実行されるか、一部のユーザー操作の結果として行われます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-199">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="4ca3e-200">コンポーネントに authService をインポートする</span><span class="sxs-lookup"><span data-stu-id="4ca3e-200">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="4ca3e-201">アクセストークンを取得して応答にアタッチする</span><span class="sxs-lookup"><span data-stu-id="4ca3e-201">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="4ca3e-202">運用環境へのデプロイ</span><span class="sxs-lookup"><span data-stu-id="4ca3e-202">Deploy to production</span></span>

<span data-ttu-id="4ca3e-203">アプリを運用環境にデプロイするには、次のリソースをプロビジョニングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-203">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="4ca3e-204">Id ユーザーアカウントとサーバー権限を格納するデータベース。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-204">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="4ca3e-205">トークンの署名に使用する実稼働証明書。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-205">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="4ca3e-206">この証明書には特定の要件はありません。自己署名証明書、または CA 証明機関を通じてプロビジョニングされた証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-206">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="4ca3e-207">PowerShell や OpenSSL などの標準ツールを使用して生成できます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-207">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="4ca3e-208">ターゲットコンピューターの証明書ストアにインストールすることも、強力なパスワードを使用して *.pfx*ファイルとして展開することもできます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-208">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="4ca3e-209">例:Azure Websites へのデプロイ</span><span class="sxs-lookup"><span data-stu-id="4ca3e-209">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="4ca3e-210">このセクションでは、証明書ストアに格納されている証明書を使用して、Azure websites にアプリをデプロイする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-210">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="4ca3e-211">証明書ストアから証明書を読み込むようにアプリを変更するには、後の手順でを構成するときに、App Service プランが少なくとも標準レベルになっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-211">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="4ca3e-212">アプリの*appsettings*ファイルで、 `IdentityServer`セクションを変更して、キーの詳細を含めます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-212">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="4ca3e-213">Certificate の name プロパティは、証明書の識別サブジェクトに対応します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-213">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="4ca3e-214">ストアの場所は、証明書を読み込む場所 (`CurrentUser`また`LocalMachine`は) を表します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-214">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="4ca3e-215">ストア名は、証明書が格納されている証明書ストアの名前を表します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-215">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="4ca3e-216">この場合、個人ユーザーストアを指します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-216">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="4ca3e-217">Azure Websites にデプロイするには、「 [azure へのアプリのデプロイ](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)」の手順に従ってアプリをデプロイし、必要な azure リソースを作成して、アプリを運用環境にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-217">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="4ca3e-218">前述の手順に従うと、アプリは Azure にデプロイされますが、まだ機能していません。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-218">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="4ca3e-219">アプリによって使用される証明書は、まだセットアップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-219">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="4ca3e-220">使用する証明書のサムプリントを見つけて、「[証明書の読み込み](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)」で説明されている手順に従います。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-220">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="4ca3e-221">これらの手順では SSL について説明していますが、ポータルには **[プライベート証明書]** セクションがあります。このセクションでは、アプリで使用するプロビジョニング済みの証明書をアップロードできます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-221">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="4ca3e-222">この手順の後にアプリを再起動すると、アプリが機能するようになります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-222">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="4ca3e-223">その他の構成オプション</span><span class="sxs-lookup"><span data-stu-id="4ca3e-223">Other configuration options</span></span>

<span data-ttu-id="4ca3e-224">API 承認のサポートは、一連の規則、既定値、および拡張機能を使用して、サーバー上に構築されます。これにより、SPAs のエクスペリエンスが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-224">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="4ca3e-225">言うまでもありませんが、ASP.NET Core 統合によって実際のシナリオがカバーされていない場合は、サーバーの全機能をバックグラウンドで利用できます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-225">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="4ca3e-226">ASP.NET Core サポートは、すべてのアプリが組織によって作成および展開される "ファーストパーティ" アプリに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-226">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="4ca3e-227">そのため、同意やフェデレーションなどのサポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-227">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="4ca3e-228">これらのシナリオでは、ユーザーを使用して、そのドキュメントに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-228">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="4ca3e-229">アプリケーションプロファイル</span><span class="sxs-lookup"><span data-stu-id="4ca3e-229">Application profiles</span></span>

<span data-ttu-id="4ca3e-230">アプリケーションプロファイルは、そのパラメーターをさらに定義するアプリの事前定義された構成です。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-230">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="4ca3e-231">現時点では、次のプロファイルがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-231">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="4ca3e-232">`IdentityServerSPA` :サービスとしてホストされる SPA を1つの単位として表します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-232">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="4ca3e-233">`redirect_uri` の`/authentication/login-callback`既定値はです。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-233">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="4ca3e-234">`post_logout_redirect_uri` の`/authentication/logout-callback`既定値はです。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-234">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="4ca3e-235">スコープのセットには、 `openid`アプリ`profile`内の api に対して定義されている、、、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-235">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="4ca3e-236">許可される oidc 応答の種類`id_token token`のセットは、それぞれ個別に ( `token``id_token`、) です。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-236">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="4ca3e-237">許可される応答モード`fragment`はです。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-237">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="4ca3e-238">`SPA`:サーバーでホストされていない SPA を表します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-238">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="4ca3e-239">スコープのセットには、 `openid`アプリ`profile`内の api に対して定義されている、、、およびすべてのスコープが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-239">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="4ca3e-240">許可される oidc 応答の種類`id_token token`のセットは、それぞれ個別に ( `token``id_token`、) です。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-240">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="4ca3e-241">許可される応答モード`fragment`はです。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-241">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="4ca3e-242">`IdentityServerJwt` :サービスと共にホストされる API を表します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-242">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="4ca3e-243">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-243">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="4ca3e-244">`API`:は、サーバーでホストされていない API を表します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-244">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="4ca3e-245">アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-245">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="4ca3e-246">AppSettings を使用した構成</span><span class="sxs-lookup"><span data-stu-id="4ca3e-246">Configuration through AppSettings</span></span>

<span data-ttu-id="4ca3e-247">構成システムを使用して、または`Clients` `Resources`の一覧にアプリを追加して、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-247">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="4ca3e-248">次の例に`redirect_uri`示す`post_logout_redirect_uri`ように、各クライアントのおよびプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-248">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="4ca3e-249">リソースを構成するときは、次に示すように、リソースのスコープを構成できます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-249">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="4ca3e-250">コードを使用した構成</span><span class="sxs-lookup"><span data-stu-id="4ca3e-250">Configuration through code</span></span>

<span data-ttu-id="4ca3e-251">また、オプションを構成するためのアクションを実行するの`AddApiAuthorization`オーバーロードを使用して、コードを使用してクライアントとリソースを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="4ca3e-251">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="4ca3e-252">その他の資料</span><span class="sxs-lookup"><span data-stu-id="4ca3e-252">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
