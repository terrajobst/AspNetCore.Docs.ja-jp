---
title: ASP.NET Core でのシングルページアプリの認証の概要
author: javiercn
description: ASP.NET Core アプリ内でホストされるシングルページアプリで Id を使用します。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 08/05/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 4f6e3a4922c0a8a74b0e13edf1f00fe5f7bb76ba
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082331"
---
# <a name="authentication-and-authorization-for-spas"></a>SPAs の認証と承認

ASP.NET Core 3.0 以降では、API 承認のサポートを使用して、シングルページアプリ (spa) で認証を提供します。 ユーザーを認証および格納するための ASP.NET Core Id は、Open ID Connect を実装するために、ユーザーと [IdentityServer](https://identityserver.io/) を組み合わせて使用されます。

認証パラメーターが、 **Web アプリケーション (モデルビューコントローラー)** (MVC) と**web アプリケーション**(Razor Pages) の認証パラメーターに似た**角度**で、**応答**するプロジェクトテンプレートに追加されました。プロジェクトテンプレート。 許可されるパラメーター値は、 **None**および**個人**です。 この時点では、対応する **.js および Redux**プロジェクトテンプレートで認証パラメーターがサポートされていません。

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

### <a name="startup-class"></a>Startup クラス

クラス`Startup`には、次の追加機能があります。

* `Startup.ConfigureServices`メソッド内:
  * 既定の UI を使用した id:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * 次のように、 `AddApiAuthorization`既定の ASP.NET Core 規則を設定する追加のヘルパーメソッドを使用して、サーバーに追加することができます。

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * 次のように`AddIdentityServerJwt`追加のヘルパーメソッドを使用して認証します。これにより、アプリは、サービスによって生成された JWT トークンを検証します。

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure`メソッド内:
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

このヘルパーメソッドは、アプリのポリシースキームを既定の認証ハンドラーとして構成します。 Id URL 空間 "/identity" のサブパスにルーティングされるすべての要求を Id で処理できるように、ポリシーが構成されています。 は`JwtBearerHandler` 、他のすべての要求を処理します。 さらに、このメソッドは`<<ApplicationName>>API` API リソースをの既定の`<<ApplicationName>>API`スコープに登録し、JWT ベアラートークンミドルウェアを構成して、アプリのために、サービスによって発行されたトークンを検証します。

### <a name="weatherforecastcontroller"></a>WeatherForecastController

*Controllers\WeatherForecastController.cs*ファイルで、属性が`[Authorize]`クラスに適用されていることを確認します。これは、リソースにアクセスするための既定のポリシーに基づいてユーザーを承認する必要があることを示します。 既定の承認ポリシーは、前に説明したポリシースキームによって`AddIdentityServerJwt`設定される既定の認証スキームを使用するように構成され、このようなヘルパーメソッドによって構成されたは`JwtBearerHandler` 、の既定のハンドラーになります。アプリへの要求。

### <a name="applicationdbcontext"></a>ApplicationDbContext

*Data\ApplicationDbContext.cs*ファイルで`DbContext`は、id で拡張`ApiAuthorizationDbContext`された例外 (から`IdentityDbContext`派生したクラス) を使用して、ユーザーのスキーマを含めることができます。

データベーススキーマを完全に制御するには、使用可能な id `DbContext`クラスの1つを継承し、 `OnModelCreating`メソッドでを呼び出し`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`て、id スキーマを含めるようにコンテキストを構成します。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

*Controllers\OidcConfigurationController.cs*ファイルで、クライアントが使用する必要のある oidc パラメーターを提供するためにプロビジョニングされたエンドポイントを確認します。

### <a name="appsettingsjson"></a>appsettings.json

プロジェクトルートの*appsettings*ファイルには、構成されたクライアントの一覧`IdentityServer`を説明する新しいセクションがあります。 次の例には、1つのクライアントがあります。 クライアント名はアプリケーション名に対応し、OAuth `ClientId`パラメーターに規約によってマップされます。 プロファイルは、構成されているアプリの種類を示します。 サーバーの構成プロセスを簡略化する規則を実現するために、内部的に使用されます。 「[アプリケーションプロファイル](#application-profiles)」セクションで説明されているように、使用可能なプロファイルがいくつかあります。

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.開発. json

Appsettings で *。プロジェクトルートの開発用 json*ファイルには、トークンの`IdentityServer`署名に使用されるキーについて説明するセクションがあります。 運用環境にデプロイする場合は、「[運用環境にデプロイする](#deploy-to-production)」セクションで説明されているように、アプリと共にキーをプロビジョニングしてデプロイする必要があります。

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>角度アプリの一般的な説明

角度テンプレートでの認証と API 承認のサポートは、独自の角度モジュールの*Clientapp-authorization*ディレクトリに存在します。 モジュールは、次の要素で構成されています。

* 3個のコンポーネント:
  * *login. component. ts*:アプリのログインフローを処理します。
  * *logout. component. ts*:アプリのログアウトフローを処理します。
  * *ログイン-メニュー. component. ts*:次のリンクのセットのいずれかを表示するウィジェット。
    * ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。
    * ユーザーが認証されていない場合の登録とログインリンク。
* ルートに追加`AuthorizeGuard`できるルートガード。ルートにアクセスする前にユーザーを認証する必要があります。
* ユーザーが認証`AuthorizeInterceptor`されるときに、API を対象とする発信 http 要求にアクセストークンを関連付ける http インターセプター。
* 認証プロセス`AuthorizeService`の下位レベルの詳細を処理し、認証されたユーザーに関する情報を、使用するアプリの残りの部分に公開するサービス。
* アプリの認証部分に関連付けられているルートを定義する角度モジュール。 ログインメニューコンポーネント、インターセプター、ガード、およびアプリの残りの部分から使用するためのサービスを公開します。

## <a name="general-description-of-the-react-app"></a>反応アプリの一般的な説明

応答テンプレートでの認証と API 承認のサポートは、 *ClientApp\src\components\api-authorization*ディレクトリにあります。 これは、次の要素で構成されています。

* 4つのコンポーネント:
  * *.Js*:アプリのログインフローを処理します。
  * *Logout*:アプリのログアウトフローを処理します。
  * *Loginmenu js*:次のリンクのセットのいずれかを表示するウィジェット。
    * ユーザーが認証されると、ユーザープロファイルの管理とログアウトのリンクがあります。
    * ユーザーが認証されていない場合の登録とログインリンク。
  * *AuthorizeRoute*:`Component`パラメーターで指定されたコンポーネントを表示する前に、ユーザーを認証する必要があるルートコンポーネント。
* 認証プロセス`authService`の下位レベル`AuthorizeService`の詳細を処理し、認証されたユーザーに関する情報をアプリの残りの部分に公開する、クラスのエクスポートされたインスタンス。

これで、ソリューションの主要なコンポーネントを確認できました。次は、アプリの個々のシナリオについて詳しく見ていきましょう。

## <a name="require-authorization-on-a-new-api"></a>新しい API での承認が必要

既定では、システムは新しい Api の承認を要求するように構成されています。 これを行うには、新しいコントローラーを作成し`[Authorize]` 、コントローラークラスまたはコントローラー内の任意のアクションに属性を追加します。

## <a name="protect-a-client-side-route-angular"></a>クライアント側のルートを保護する (角度)

クライアント側ルートの保護は、ルートを構成するときに実行するガードのリストに承認ガードを追加することによって行われます。 例として、主要なアプリの`fetch-data`角度モジュール内でルートがどのように構成されているかを確認できます。

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

ルートを保護しても実際のエンドポイントが保護されないことに注意し`[Authorize]`てください (まだ属性が適用されている必要があります) が、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。

## <a name="authenticate-api-requests-angular"></a>API 要求の認証 (角度)

アプリと共にホストされる Api に対する要求の認証は、アプリによって定義された HTTP クライアントインターセプターを使用することによって自動的に行われます。

## <a name="protect-a-client-side-route-react"></a>クライアント側のルートを保護する (応答)

`AuthorizeRoute` プレーン`Route`コンポーネントの代わりにコンポーネントを使用して、クライアント側のルートを保護します。 たとえば、 `fetch-data` `App`コンポーネント内でルートがどのように構成されているかに注目してください。

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

ルートの保護:

* は実際のエンドポイントを保護しません`[Authorize]` (まだ属性が適用されている必要があります)。
* は、ユーザーが認証されていないときに、特定のクライアント側ルートに移動できないようにします。

## <a name="authenticate-api-requests-react"></a>API 要求の認証 (応答)

を使用して要求を認証するには`authService` 、 `AuthorizeService`まずからインスタンスをインポートします。 アクセストークンはから`authService`取得され、次に示すように要求にアタッチされます。 コンポーネントの反応では、通常、この作業は`componentDidMount`ライフサイクルメソッドで実行されるか、一部のユーザー操作の結果として行われます。

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

## <a name="deploy-to-production"></a>運用環境へのデプロイ

アプリを運用環境にデプロイするには、次のリソースをプロビジョニングする必要があります。

* Id ユーザーアカウントとサーバー権限を格納するデータベース。
* トークンの署名に使用する実稼働証明書。
  * この証明書には特定の要件はありません。自己署名証明書、または CA 証明機関を通じてプロビジョニングされた証明書を使用できます。
  * PowerShell や OpenSSL などの標準ツールを使用して生成できます。
  * ターゲットコンピューターの証明書ストアにインストールすることも、強力なパスワードを使用して *.pfx*ファイルとして展開することもできます。

### <a name="example-deploy-to-azure-websites"></a>例:Azure Websites へのデプロイ

このセクションでは、証明書ストアに格納されている証明書を使用して、Azure websites にアプリをデプロイする方法について説明します。 証明書ストアから証明書を読み込むようにアプリを変更するには、後の手順でを構成するときに、App Service プランが少なくとも標準レベルになっている必要があります。 アプリの*appsettings*ファイルで、 `IdentityServer`セクションを変更して、キーの詳細を含めます。

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
* ストアの場所は、証明書を読み込む場所 (`CurrentUser`また`LocalMachine`は) を表します。
* ストア名は、証明書が格納されている証明書ストアの名前を表します。 この場合、個人ユーザーストアを指します。

Azure Websites にデプロイするには、「 [azure へのアプリのデプロイ](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)」の手順に従ってアプリをデプロイし、必要な azure リソースを作成して、アプリを運用環境にデプロイします。

前述の手順に従うと、アプリは Azure にデプロイされますが、まだ機能していません。 アプリによって使用される証明書は、まだセットアップする必要があります。 使用する証明書のサムプリントを見つけて、「[証明書の読み込み](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)」で説明されている手順に従います。

これらの手順では SSL について説明していますが、ポータルには **[プライベート証明書]** セクションがあります。このセクションでは、アプリで使用するプロビジョニング済みの証明書をアップロードできます。

この手順の後にアプリを再起動すると、アプリが機能するようになります。

## <a name="other-configuration-options"></a>その他の構成オプション

API 承認のサポートは、一連の規則、既定値、および拡張機能を使用して、サーバー上に構築されます。これにより、SPAs のエクスペリエンスが簡単になります。 言うまでもありませんが、ASP.NET Core 統合によって実際のシナリオがカバーされていない場合は、サーバーの全機能をバックグラウンドで利用できます。 ASP.NET Core サポートは、すべてのアプリが組織によって作成および展開される "ファーストパーティ" アプリに重点を置いています。 そのため、同意やフェデレーションなどのサポートは提供されていません。 これらのシナリオでは、ユーザーを使用して、そのドキュメントに従ってください。

### <a name="application-profiles"></a>アプリケーションプロファイル

アプリケーションプロファイルは、そのパラメーターをさらに定義するアプリの事前定義された構成です。 現時点では、次のプロファイルがサポートされています。

* `IdentityServerSPA` :サービスとしてホストされる SPA を1つの単位として表します。
  * `redirect_uri` の`/authentication/login-callback`既定値はです。
  * `post_logout_redirect_uri` の`/authentication/logout-callback`既定値はです。
  * スコープのセットには、 `openid`アプリ`profile`内の api に対して定義されている、、、およびすべてのスコープが含まれます。
  * 許可される oidc 応答の種類`id_token token`のセットは、それぞれ個別に ( `token``id_token`、) です。
  * 許可される応答モード`fragment`はです。
* `SPA`:サーバーでホストされていない SPA を表します。
  * スコープのセットには、 `openid`アプリ`profile`内の api に対して定義されている、、、およびすべてのスコープが含まれます。
  * 許可される oidc 応答の種類`id_token token`のセットは、それぞれ個別に ( `token``id_token`、) です。
  * 許可される応答モード`fragment`はです。
* `IdentityServerJwt` :サービスと共にホストされる API を表します。
  * アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。
* `API`:は、サーバーでホストされていない API を表します。
  * アプリは、アプリ名を既定とする1つのスコープを持つように構成されています。

### <a name="configuration-through-appsettings"></a>AppSettings を使用した構成

構成システムを使用して、または`Clients` `Resources`の一覧にアプリを追加して、アプリを構成します。

次の例に`redirect_uri`示す`post_logout_redirect_uri`ように、各クライアントのおよびプロパティを構成します。

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

また、オプションを構成するためのアクションを実行するの`AddApiAuthorization`オーバーロードを使用して、コードを使用してクライアントとリソースを構成することもできます。

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
* <xref:security/authentication/scaffold-identity>
