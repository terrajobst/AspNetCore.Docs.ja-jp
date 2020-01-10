---
title: ASP.NET Core でのシングルページアプリの認証の概要
author: javiercn
description: ASP.NET Core アプリ内でホストされるシングルページアプリで Id を使用します。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 31a5e47d772e7416646c4d83c3209d7d2b254199
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829167"
---
# <a name="authentication-and-authorization-for-spas"></a>SPAs の認証と承認

ASP.NET Core 3.0 以降では、API 承認のサポートを使用して、シングルページアプリ (spa) で認証を提供します。 ユーザーを認証および格納するための ASP.NET Core Id は、Open ID Connect を実装するために、ユーザーと [IdentityServer](https://identityserver.io/) を組み合わせて使用されます。

**Angular** プロジェクトテンプレートと **React** プロジェクトテンプレートに、**Webアプリケーション(Model-View-Controller)** (MVC)と **Web アプリケーション**(Razor Pages)プロジェクトテンプレートの認証パラメータに似た認証パラメータが追加されました。 使用できるパラメータ値は、**None** および **Individual** です。 現時点では、 **React.js および Redux** プロジェクトテンプレートは認証パラメータをサポートしていません。

## <a name="create-an-app-with-api-authorization-support"></a>API authorization サポートを使用してアプリを作成する

ユーザーの認証と承認は、両方の角度で使用でき、SPAs として対応します。 コマンド シェルを開き、次のコマンドを実行します。

**角度**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**反応**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

前のコマンドは、 *ClientApp*ディレクトリに SPA を含む ASP.NET Core アプリを作成します。

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>アプリの ASP.NET Core コンポーネントの一般的な説明

次のセクションでは、認証のサポートが含まれている場合のプロジェクトへの追加について説明します。

### <a name="startup-class"></a>スタートアップ クラス

`Startup` クラスには、次の追加機能があります。

* `Startup.ConfigureServices` メソッド内:
  * 既定の UI を使用した id:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * 次のように、追加の `AddApiAuthorization` ヘルパーメソッドを使用して、サーバーの上に既定の ASP.NET Core 規則を設定します。

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 追加の `AddIdentityServerJwt` ヘルパーメソッドを使用した認証。次のように、アプリを構成して、サーバーによって生成された JWT トークンを検証します。

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure` メソッド内:
  * 要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア。

    ```csharp
    app.UseAuthentication();
    ```

  * Open ID Connect エンドポイントを公開するサーバーミドルウェア:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

このヘルパーメソッドは、サポートされる構成を使用するようにサーバーを構成します。 サービスは、アプリのセキュリティの問題を処理するための強力で拡張可能なフレームワークです。 同時に、最も一般的なシナリオでは不必要な複雑さを公開します。 そのため、適切な出発点と見なされる一連の規則と構成オプションが用意されています。 認証を変更する必要がある場合でも、ユーザーのニーズに合わせて認証をカスタマイズするために、ユーザーサーバーの能力を最大限に活用できます。

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

このヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。 Id URL 空間 "/identity" のサブパスにルーティングされるすべての要求を Id で処理できるように、ポリシーが構成されています。 `JwtBearerHandler` は、他のすべての要求を処理します。 さらに、このメソッドは、`<<ApplicationName>>API` API リソースを、既定のスコープである `<<ApplicationName>>API` を使用して登録し、JWT ベアラートークンミドルウェアを構成して、アプリのために、サービスによって発行されたトークンを検証します。

### <a name="weatherforecastcontroller"></a>WeatherForecastController

*Controllers\WeatherForecastController.cs*ファイルで、リソースにアクセスするための既定のポリシーに基づいてユーザーを承認する必要があることを示す `[Authorize]` 属性がクラスに適用されていることを確認します。 既定の承認ポリシーは、既定の認証スキームを使用するように構成されます。これは、前述のポリシースキームに `AddIdentityServerJwt` によって設定されます。このようなヘルパーメソッドによって構成された `JwtBearerHandler` は、アプリに対する要求の既定のハンドラーになります。

### <a name="applicationdbcontext"></a>ApplicationDbContext

*Data\ApplicationDbContext.cs*ファイルで、id に同じ `DbContext` が使用されていることに注意してください `ApiAuthorizationDbContext` (`IdentityDbContext`から派生したクラス) を使用して、ユーザーのスキーマを含めることができます。

データベーススキーマを完全に制御するには、使用可能な Id `DbContext` クラスの1つを継承し、`OnModelCreating` メソッドで `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` を呼び出して、Id スキーマを含めるようにコンテキストを構成します。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

*Controllers\OidcConfigurationController.cs*ファイルで、クライアントが使用する必要のある oidc パラメーターを提供するためにプロビジョニングされたエンドポイントを確認します。

### <a name="appsettingsjson"></a>appsettings.json

プロジェクトルートの*appsettings*ファイルには、構成されたクライアントの一覧を説明する新しい `IdentityServer` セクションがあります。 次の例には、1つのクライアントがあります。 クライアント名はアプリケーション名に対応し、OAuth `ClientId` パラメーターに規約によってマップされます。 プロファイルは、構成されているアプリの種類を示します。 サーバーの構成プロセスを簡略化する規則を実現するために、内部的に使用されます。 「[アプリケーションプロファイル](#application-profiles)」セクションで説明されているように、使用可能なプロファイルがいくつかあります。

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.json

プロジェクトルートの*appsettings.Development.json*ファイルには、トークンの署名に使用されるキーについて説明する `IdentityServer` セクションがあります。 運用環境にデプロイする場合は、「[運用環境にデプロイする](#deploy-to-production)」セクションで説明されているように、アプリと共にキーをプロビジョニングしてデプロイする必要があります。

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>角度アプリの一般的な説明

Angular テンプレートでの認証と API 承認のサポートは、*ClientApp\src\api-authorization* ディレクトリの、独自の Angular モジュールに存在します。 モジュールは、次の要素で構成されています。

* 3個のコンポーネント:
  * *login. component. ts*: アプリのログインフローを処理します。
  * *logout*: アプリのログアウトフローを処理します。
  * *login-menu. component. ts*: 次のリンクのセットのいずれかを表示するウィジェット。
    * ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。
    * ユーザーが認証されていない場合の登録とログインリンク。
* ルートに追加することができ、ルートにアクセスする前にユーザーを認証する必要があるルートガード `AuthorizeGuard`。
* ユーザーが認証されるときに、API を対象とする発信 HTTP 要求にアクセストークンを結び付ける HTTP インターセプター `AuthorizeInterceptor`。
* 認証プロセスの下位レベルの詳細を処理し、認証されたユーザーに関する情報をアプリの残りの部分に公開するサービス `AuthorizeService`。
* アプリの認証部分に関連付けられているルートを定義する角度モジュール。 ログインメニューコンポーネント、インターセプター、ガード、およびアプリの残りの部分から使用するためのサービスを公開します。

## <a name="general-description-of-the-react-app"></a>反応アプリの一般的な説明

応答テンプレートでの認証と API 承認のサポートは、 *ClientApp\src\components\api-authorization*ディレクトリにあります。 これは、次の要素で構成されています。

* 4つのコンポーネント:
  * *.Js*: アプリのログインフローを処理します。
  * *Logout*: アプリのログアウトフローを処理します。
  * *Loginmenu js*: 次のリンクのセットのいずれかを表示するウィジェット。
    * ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。
    * ユーザーが認証されていない場合の登録とログインリンク。
  * *AuthorizeRoute*: `Component` パラメーターに示されているコンポーネントを表示する前に、ユーザーを認証する必要があるルートコンポーネント。
* 認証プロセスの下位レベルの詳細を処理し、認証されたユーザーに関する情報をアプリの残りの部分に公開する、クラス `AuthorizeService` のエクスポートされた `authService` インスタンス。

これで、ソリューションの主要なコンポーネントを確認できました。次は、アプリの個々のシナリオについて詳しく見ていきましょう。

## <a name="require-authorization-on-a-new-api"></a>新しい API での承認が必要

既定では、システムは新しい Api の承認を要求するように構成されています。 これを行うには、新しいコントローラーを作成し、コントローラークラスまたはコントローラー内の任意のアクションに `[Authorize]` 属性を追加します。

## <a name="customize-the-api-authentication-handler"></a>API 認証ハンドラーをカスタマイズする

API の JWT ハンドラーの構成をカスタマイズするには、その <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> インスタンスを構成します。

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

API の JWT ハンドラーは、`JwtBearerEvents`を使用して認証プロセスを制御できるようにするイベントを発生させます。 API 認証のサポートを提供するために、`AddIdentityServerJwt` 独自のイベントハンドラーを登録します。

イベントの処理をカスタマイズするには、必要に応じて追加のロジックを使用して既存のイベントハンドラーをラップします。 例:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

前のコードでは、`OnTokenValidated` イベントハンドラーはカスタム実装に置き換えられています。 この実装は次のとおりです。

1. API 承認サポートによって提供される元の実装を呼び出します。
1. 独自のカスタムロジックを実行します。

## <a name="protect-a-client-side-route-angular"></a>クライアント側のルートを保護する (角度)

クライアント側ルートの保護は、ルートを構成するときに実行するガードのリストに承認ガードを追加することによって行われます。 例として、`fetch-data` ルートがメインアプリの角度モジュール内でどのように構成されているかを確認できます。

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

ルートを保護しても実際のエンドポイントが保護されないことに注意してください (これには `[Authorize]` 属性が適用されている必要があります) が、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにすることをお勧めします。

## <a name="authenticate-api-requests-angular"></a>API 要求の認証 (角度)

アプリと共にホストされる Api に対する要求の認証は、アプリによって定義された HTTP クライアントインターセプターを使用することによって自動的に行われます。

## <a name="protect-a-client-side-route-react"></a>クライアント側のルートを保護する (応答)

プレーン `Route` コンポーネントの代わりに `AuthorizeRoute` コンポーネントを使用して、クライアント側のルートを保護します。 たとえば、`App` コンポーネント内で `fetch-data` ルートがどのように構成されているかに注目してください。

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

ルートの保護:

* では、実際のエンドポイントは保護されません (`[Authorize]` 属性も適用する必要があります)。
* は、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。

## <a name="authenticate-api-requests-react"></a>API 要求の認証 (応答)

React を含む要求の認証は、最初に `AuthorizeService`から `authService` インスタンスをインポートすることによって行われます。 次に示すように、アクセストークンは `authService` から取得され、要求にアタッチされます。 コンポーネントの処理では、通常、この作業は `componentDidMount` ライフサイクルメソッドで実行されるか、一部のユーザー操作の結果として行われます。

### <a name="import-the-authservice-into-your-component"></a>コンポーネントに authService をインポートする

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>アクセストークンを取得して応答にアタッチする

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

## <a name="deploy-to-production"></a>運用環境に展開する

アプリを運用環境にデプロイするには、次のリソースをプロビジョニングする必要があります。

* Id ユーザーアカウントとサーバー権限を格納するデータベース。
* トークンの署名に使用する実稼働証明書。
  * この証明書には特定の要件はありません。自己署名証明書、または CA 証明機関を通じてプロビジョニングされた証明書を使用できます。
  * PowerShell や OpenSSL などの標準ツールを使用して生成できます。
  * ターゲットコンピューターの証明書ストアにインストールすることも、強力なパスワードを使用して *.pfx*ファイルとして展開することもできます。

### <a name="example-deploy-to-azure-websites"></a>例: Azure Websites へのデプロイ

このセクションでは、証明書ストアに格納されている証明書を使用して、Azure websites にアプリをデプロイする方法について説明します。 証明書ストアから証明書を読み込むようにアプリを変更するには、後の手順でを構成するときに、App Service プランが少なくとも標準レベルになっている必要があります。 アプリの*appsettings*ファイルで、`IdentityServer` セクションを変更して、キーの詳細を含めます。

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

* Certificate の name プロパティは、証明書の識別サブジェクトに対応します。
* ストアの場所は、証明書の読み込み元 (`CurrentUser` または `LocalMachine`) を表します。
* ストア名は、証明書が格納されている証明書ストアの名前を表します。 この場合、個人ユーザーストアを指します。

Azure Websites にデプロイするには、「 [azure へのアプリのデプロイ](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)」の手順に従ってアプリをデプロイし、必要な azure リソースを作成して、アプリを運用環境にデプロイします。

前述の手順に従うと、アプリは Azure にデプロイされますが、まだ機能していません。 アプリによって使用される証明書は、まだセットアップする必要があります。 使用する証明書のサムプリントを見つけて、「[証明書の読み込み](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)」で説明されている手順に従います。

これらの手順では SSL について説明していますが、ポータルには **[プライベート証明書]** セクションがあります。このセクションでは、アプリで使用するプロビジョニング済みの証明書をアップロードできます。

この手順の後にアプリを再起動すると、アプリが機能するようになります。

## <a name="other-configuration-options"></a>その他の構成オプション

API 承認のサポートは、一連の規則、既定値、および拡張機能を使用して、サーバー上に構築されます。これにより、SPAs のエクスペリエンスが簡単になります。 言うまでもありませんが、ASP.NET Core 統合によって実際のシナリオがカバーされていない場合は、サーバーの全機能をバックグラウンドで利用できます。 ASP.NET Core サポートは、すべてのアプリが組織によって作成および展開される "ファーストパーティ" アプリに重点を置いています。 そのため、同意やフェデレーションなどのサポートは提供されていません。 これらのシナリオでは、ユーザーを使用して、そのドキュメントに従ってください。

### <a name="application-profiles"></a>アプリケーション プロファイル

アプリケーションプロファイルは、そのパラメーターをさらに定義するアプリの事前定義された構成です。 現時点では、次のプロファイルがサポートされています。

* `IdentityServerSPA`: 1 つの単位としてホストされる SPA サーバーを表します。
  * `redirect_uri` の既定値は `/authentication/login-callback`です。
  * `post_logout_redirect_uri` の既定値は `/authentication/logout-callback`です。
  * スコープのセットには、アプリ内の Api に対して定義されている、`openid`、`profile`、およびすべてのスコープが含まれます。
  * 許可される OIDC 応答の種類のセットは、`id_token token` またはそれぞれ個別に (`id_token`、`token`) 使用されます。
  * 許可される応答モードは `fragment`です。
* `SPA`: は、サーバーでホストされていない SPA を表します。
  * スコープのセットには、アプリ内の Api に対して定義されている、`openid`、`profile`、およびすべてのスコープが含まれます。
  * 許可される OIDC 応答の種類のセットは、`id_token token` またはそれぞれ個別に (`id_token`、`token`) 使用されます。
  * 許可される応答モードは `fragment`です。
* `IdentityServerJwt`: サービスと共にホストされる API を表します。
  * アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。
* `API`: は、サーバーでホストされていない API を表します。
  * アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。

### <a name="configuration-through-appsettings"></a>AppSettings を使用した構成

構成システムを使用してアプリを構成するには、`Clients` または `Resources`の一覧にアプリを追加します。

次の例に示すように、各クライアントの `redirect_uri` と `post_logout_redirect_uri` プロパティを構成します。

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

リソースを構成するときは、次に示すように、リソースのスコープを構成できます。

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

### <a name="configuration-through-code"></a>コードを使用した構成

また、オプションを構成するアクションを実行する `AddApiAuthorization` のオーバーロードを使用して、コードを使用してクライアントとリソースを構成することもできます。

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

## <a name="additional-resources"></a>その他の技術情報

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
