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
# <a name="authentication-and-authorization-for-spas"></a>Spa の認証と承認

ASP.NET Core 3.0 以降では、API 認証のサポートを使用してシングル ページ アプリ (Spa) での認証を提供します。 認証およびユーザーを格納するための ASP.NET Core Identity が組み合わされます[IdentityServer](https://identityserver.io/) Open ID Connect を実装するためです。

認証パラメーターに追加された、 **Angular**と**React**プロジェクト テンプレートでの認証パラメーターに似ている**Web アプリケーション (モデル-ビュー-コント ローラー)** (MVC) および**Web アプリケーション**(Razor ページ) のプロジェクト テンプレート。 許可されているパラメーター値は**None**と**個々**します。 **React.js と Redux**プロジェクト テンプレートは、現時点での認証パラメーターをサポートしません。

## <a name="create-an-app-with-api-authorization-support"></a>API 認証のサポートをアプリを作成します。

ユーザーの認証と承認は、Angular と React の Spa の両方で使用できます。 コマンド シェルを開き、次のコマンドを実行します。

**Angular**:

```console
dotnet new angular -o <output_directory_name> -au Individual
```

**React**:

```console
dotnet new react -o <output_directory_name> -au Individual
```

上記のコマンドで ASP.NET Core アプリを作成し、 *ClientApp* SPA を含むディレクトリ。

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>アプリの ASP.NET Core コンポーネントの一般的な説明

次のセクションでは認証のサポートが含まれる場合、プロジェクトへの追加について説明します。

### <a name="startup-class"></a>Startup クラス

`Startup`クラスには、次の追加。

* 内で、`Startup.ConfigureServices`メソッド。
  * 既定の UI を持つ id:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * 追加された IdentityServer`AddApiAuthorization`ヘルパー メソッドそのセットアップの一部の既定設定 IdentityServer の上に ASP.NET Core の表記規則。

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 追加された認証`AddIdentityServerJwt`IdentityServer によって生成される JWT トークンを検証するアプリを構成するヘルパー メソッド。

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* 内で、`Startup.Configure`メソッド。
  * 要求の資格情報を検証および要求のコンテキストでユーザーを設定するため、認証ミドルウェア:

    ```csharp
    app.UseAuthentication();
    ```

  * Open ID Connect エンドポイントを公開する、IdentityServer ミドルウェア:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

このヘルパー メソッドでは、IdentityServer は、サポートされている構成を使用して構成します。 IdentityServer とは、アプリのセキュリティに関する注意事項を処理するための強力で拡張可能なフレームワークです。 同時に、最も一般的なシナリオは不必要に複雑さを公開します。 そのため、一連の規則と構成オプションは、適切な開始点と見なされるに提供されます。 認証のニーズの変化、IdentityServer の全機能を引き続きできるよう認証をニーズに合わせてカスタマイズになります。

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

このヘルパー メソッドは、既定の認証ハンドラーとしてアプリのポリシー設定を構成します。 Identity Id URL 空間で任意のサブパスにルーティングされるすべての要求を処理できるようにするポリシーが構成されている"/Identity"。 `JwtBearerHandler`他のすべての要求を処理します。 さらに、このメソッドは、登録、 `<<ApplicationName>>API` API リソースの IdentityServer との既定のスコープを持つ`<<ApplicationName>>API`し、アプリの IdentityServer によって発行されたトークンを検証する JWT ベアラ トークン ミドルウェアを構成します。

### <a name="sampledatacontroller"></a>SampleDataController

*Controllers\SampleDataController.cs*ファイルに注意してください、`[Authorize]`リソースにアクセスする既定のポリシーに基づいて、ユーザーを承認する必要があることを示すクラスに適用される属性。 既定の承認ポリシーによって設定される既定の認証スキームを使用するように構成しようとしました`AddIdentityServerJwt`が上記のように作成したポリシーの構成、`JwtBearerHandler`によってこのようなヘルパー メソッドの既定のハンドラーを構成します。アプリに要求します。

### <a name="applicationdbcontext"></a>ApplicationDbContext

*Data\ApplicationDbContext.cs*ファイルで同じことを確認`DbContext`を拡張する例外の併用の Id に`ApiAuthorizationDbContext`(クラスから派生`IdentityDbContext`) IdentityServer は、スキーマを含めることができます。

データベース スキーマの完全な制御を取得するには、使用可能な Id のいずれかから継承`DbContext`クラスし、構成を呼び出すことによって Id スキーマを含めるコンテキスト`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`上、`OnModelCreating`メソッド。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

*Controllers\OidcConfigurationController.cs*ファイルで、クライアントが使用する必要がある OIDC パラメーターを処理するためにプロビジョニングされたエンドポイントに注意してください。

### <a name="appsettingsjson"></a>appsettings.json

*Appsettings.json*ファイルをプロジェクトのルートが新しい`IdentityServer`セクションの一覧を記述するには、クライアントが構成されています。 次の例では、1 つのクライアントです。 クライアント名は、アプリ名に対応し、OAuth に規約によってマップされます`ClientId`パラメーター。 プロファイルでは、構成されているアプリの種類を示します。 サーバーの構成プロセスを簡略化されるドライブの表記規則を内部的に使用されます。 いくつかプロファイルが使用可能なで説明したように、[アプリケーション プロファイル](#application-profiles)セクション。

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings します。Development.json

*Appsettings します。Development.json*プロジェクトのルートのファイルがある、`IdentityServer`トークンの署名に使用するキーについて説明したセクション。 説明したように、キーがプロビジョニングして、アプリと共にデプロイする必要を運用環境にデプロイするとき、[を運用環境にデプロイ](#deploy-to-production)セクション。

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Angular アプリの一般的な説明

認証と承認の API をサポートして、Angular での Angular モジュールは独自のテンプレートが存在する、 *ClientApp\src\api 承認*ディレクトリ。 モジュールは、次の要素で構成されます。

* 3 つのコンポーネント:
  * *login.component.ts*:アプリのログイン フローを処理します。
  * *logout.component.ts*:アプリのログアウトのフローを処理します。
  * *login-menu.component.ts*:次のリンクのセットのいずれかを表示するウィジェット。
    * ユーザー プロファイルの管理とリンク、ユーザーが認証されるときにログアウトします。
    * 登録と、ユーザーが認証されないときのリンクでのログ。
* ルート guard`AuthorizeGuard`をルートに追加することができ、ルートにアクセスする前に認証するユーザーが必要です。
* HTTP インターセプター`AuthorizeInterceptor`ユーザーが認証されるときに、API を対象とする HTTP 要求を送信するアクセス トークンをアタッチします。
* サービス`AuthorizeService`を認証プロセスの低レベルの詳細を処理し、使用量のアプリの残りの部分に認証されたユーザーに関する情報を公開します。
* アプリの認証の部分に関連付けられているルートを定義する Angular モジュール。 ログイン] メニューの [コンポーネント、インターセプターの保護、およびサービスをアプリの他の部分で使用を公開します。

## <a name="general-description-of-the-react-app"></a>React のアプリの一般的な説明

認証と React テンプレートでの API の承認のサポートが存在する、 *ClientApp\src\components\api 承認*ディレクトリ。 次の要素で構成されます。

* 4 個のコンポーネント:
  * *Login.js*:アプリのログイン フローを処理します。
  * *Logout.js*:アプリのログアウトのフローを処理します。
  * *LoginMenu.js*:次のリンクのセットのいずれかを表示するウィジェット。
    * ユーザー プロファイルの管理とリンク、ユーザーが認証されるときにログアウトします。
    * 登録と、ユーザーが認証されないときのリンクでのログ。
  * *AuthorizeRoute.js*:示されているコンポーネントを表示する前に認証するユーザーを必要とするルート コンポーネント、`Component`パラメーター。
* エクスポートした`authService`クラスのインスタンス`AuthorizeService`を認証プロセスの低レベルの詳細を処理し、使用量のアプリの残りの部分に認証されたユーザーに関する情報を公開します。

を、ソリューションの主要なコンポーネントを確認するには、アプリの個々 のシナリオについて詳しく説明を実行できます。

## <a name="require-authorization-on-a-new-api"></a>新しい API の承認を要求します。

既定では、システムは簡単に新しい Api の承認を要求するように構成します。 これを行うには、新しいコント ローラーを作成し、追加、`[Authorize]`属性をコント ローラー クラスまたはコント ローラー内の任意のアクション。

## <a name="protect-a-client-side-route-angular"></a>クライアント側ルート (Angular) を保護します。

クライアント側ルートを保護すると、ルートを構成するときに実行する警備員の一覧に、承認の保護を追加することで行われます。 表示できる例として、方法、`fetch-data`ルートは、メイン アプリケーションの Angular モジュール内で構成されます。

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

ルートを保護すると、実際のエンドポイントが保護しないことは言うまでも重要です (これが必要、`[Authorize]`属性が適用されて) が、認証が行われないときに、特定のクライアント側のルートに移動することから、ユーザーのみができないようにします。

## <a name="authenticate-api-requests-angular"></a>API の要求 (Angular) の認証します。

アプリと共にホストされている Api に要求の認証は、アプリによって定義されている HTTP クライアントのインターセプターを使用して自動的に行われます。

## <a name="protect-a-client-side-route-react"></a>クライアント側ルート (React) を保護します。

クライアント側ルートを使用して、保護、 `AuthorizeRoute` 、プレーンではなくコンポーネント`Route`コンポーネント。 たとえば、方法、`fetch-data`内でルートが構成されて、`App`コンポーネント。

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

ルートの保護。

* 実際のエンドポイントを保護しません (これが必要、`[Authorize]`属性が適用されて)。
* のみ、ユーザー、認証が行われないときに、特定のクライアント側のルートに移動できません。

## <a name="authenticate-api-requests-react"></a>API の要求 (React) の認証します。

最初のインポートを行うを React での要求の認証、`authService`インスタンスから、`AuthorizeService`します。 アクセス トークンがから取得した、`authService`され次に示すように、要求にアタッチされます。 React コンポーネントでは、この作業は通常で実行、`componentDidMount`ライフ サイクル メソッドまたは一部のユーザー操作から結果として。

### <a name="import-the-authservice-into-your-component"></a>コンポーネントへのインポート、authService

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>取得し、アクセス トークンを応答にアタッチ

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

## <a name="deploy-to-production"></a>運用環境にデプロイします。

運用環境にアプリを展開するには、次のリソースをプロビジョニングする必要があります。

* Id のユーザー アカウントと、IdentityServer を格納するデータベースを許可します。
* トークンの署名に使用する運用証明書。
  * この証明書の特定の要件はありません。自己署名証明書または CA 機関を使用してプロビジョニング証明書を指定できます。
  * PowerShell または OpenSSL などの標準のツールによって生成できます。
  * ターゲット コンピューターの証明書ストアにインストールされているまたはとしてデプロイされていることができます、 *.pfx*強力なパスワードを持つファイル。

### <a name="example-deploy-to-azure-websites"></a>例:Azure Websites にデプロイします。

このセクションでは、証明書ストアに格納されている証明書を使用して Azure の web サイトにアプリのデプロイについて説明します。 証明書ストアから証明書の読み込みにアプリを変更するには、App Service プランに以上である、Standard レベル、後の手順で構成するときに必要があります。 アプリの*appsettings.json*ファイルで、変更、`IdentityServer`セクションにキーの詳細を含めます。

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

* 証明書の名前プロパティは、識別証明書の件名と一致します。
* ストアの場所から証明書を読み込む場所を表します (`CurrentUser`または`LocalMachine`)。
* ストアの名前は、証明書が格納されている証明書ストアの名前を表します。 この場合、これは、ユーザーの個人ストアを指します。

Azure Websites にデプロイするには、次の手順では、アプリをデプロイ[アプリを Azure にデプロイ](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)を必要な Azure リソースを作成し、運用環境にアプリをデプロイします。

上記の手順を実行した後、アプリは Azure にデプロイされますはまだ機能しません。 アプリによって使用される証明書は、設定する必要があります。 使用する証明書の拇印を見つけるしで説明されている手順に従います[証明書の読み込み](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)します。

中にこれらの手順には、SSL が言うまでは、**プライベート証明書**ポータルのセクションに、アプリで使用するプロビジョニング証明書をアップロードすることができます。

この手順の後、アプリを再起動し、機能が必要です。

## <a name="other-configuration-options"></a>他の構成オプション

API 認証のサポートは、一連の規則、既定値、および、Spa のエクスペリエンスを簡略化の機能強化を使用して、IdentityServer の上にビルドします。 もちろん、IdentityServer の能力を最大限はバック グラウンドで使用可能な ASP.NET Core 統合は、シナリオで説明されていない場合です。 ASP.NET Core のサポートは、「ファーストパーティ」アプリ、すべてのアプリを作成し、組織で展開する場所を重視されています。 そのため、同意またはフェデレーションなどのサポートが用意されていません。 これらのシナリオでは、IdentityServer を使用し、そのドキュメントに従ってください。

### <a name="application-profiles"></a>アプリケーション プロファイル

アプリケーション プロファイルは、さらに、パラメーターを定義するアプリの定義済みの構成を示します。 この時点で、次のプロファイルがサポートされています。

* `IdentityServerSPA`:1 つの単位として IdentityServer と共にホストされている SPA を表します。
  * `redirect_uri`の既定値は`/authentication/login-callback`します。
  * `post_logout_redirect_uri`の既定値は`/authentication/logout-callback`します。
  * スコープのセットが含まれています、 `openid`、 `profile`、Api アプリに対して定義されているすべてのスコープとします。
  * 許可されている OIDC 応答型のセットは、`id_token token`またはそれぞれ個別に (`id_token`、 `token`)。
  * 許可される応答モードは`fragment`します。
* `SPA` :IdentityServer とされていないホストされている SPA を表します。
  * スコープのセットが含まれています、 `openid`、 `profile`、Api アプリに対して定義されているすべてのスコープとします。
  * 許可されている OIDC 応答型のセットは、`id_token token`またはそれぞれ個別に (`id_token`、 `token`)。
  * 許可される応答モードは`fragment`します。
* `IdentityServerJwt`:IdentityServer とと共にホストされている API を表します。
  * 既定値は、アプリ名を 1 つのスコープを持つアプリが構成されます。
* `API`:IdentityServer でホストされていない API を表します。
  * 既定値は、アプリ名を 1 つのスコープを持つアプリが構成されます。

### <a name="configuration-through-appsettings"></a>AppSettings を使用した構成

一覧に追加することで、構成システムでアプリを構成する`Clients`または`Resources`します。

各クライアントの構成`redirect_uri`と`post_logout_redirect_uri`プロパティは、次の例に示すようにします。

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

リソースを構成するときに、次に示すように、リソースのスコープを構成できます。

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

クライアントとのオーバー ロードを使用してコードを使用したリソースを構成することも`AddApiAuthorization`オプションを構成するアクションを取得します。

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

## <a name="additional-resources"></a>その他の資料

* <xref:spa/angular>
* <xref:spa/react>
