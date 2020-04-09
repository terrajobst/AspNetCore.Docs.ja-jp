---
title: ASP.NET Core Blazor WebAssembly をセキュリティで保護する
author: guardrex
description: シングル ページ アプリケーション (SPA) として Blazor WebAssemlby アプリをセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: be286d770cd8d6e5cf7885b91be8654f74ffd743
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80538981"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly をセキュリティで保護する

作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor WebAssembly アプリは、シングル ページ アプリケーション (SPA) と同じ方法でセキュリティ保護されます。 ユーザーを SPA で認証する方法はいくつかありますが、最も一般的で包括的な方法は、[Open ID Connect (OIDC)](https://openid.net/connect/) などの [OAuth 2.0 プロトコル](https://oauth.net/)に基づく実装を使用することです。

## <a name="authentication-library"></a>認証ライブラリ

Blazor WebAssembly は、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリを介して OIDC を使用したアプリの認証と認可をサポートしています。 ライブラリには、ASP.NET Core バックエンドに対してシームレスに認証を行うための一連のプリミティブが用意されています。 ライブラリは ASP.NET Core の ID を [Identity Server](https://identityserver.io/) 上に構築された API 認可サポートと統合します。 このライブラリは、OpenID Provider (OP) と呼ばれる OIDC をサポートするすべてのサードパーティ ID プロバイダー (IP) に対して認証できます。

Blazor WebAssembly の認証のサポートは、基になる認証プロトコルの詳細を処理するために使用される、*oidc-client.js* ライブラリの上に構築されています。

SameSite Cookie の使用など、SPA を認証するためのその他のオプションも存在します。 ただし、Blazor WebAssembly のエンジニアリング設計は、Blazor WebAssembly アプリでの認証に最適なオプションとして OAuth および OIDC にも採用されています。 [JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) に基づく[トークンベースの認証](xref:security/anti-request-forgery#token-based-authentication)は、機能とセキュリティ上の理由により、[Cookie ベースの認証](xref:security/anti-request-forgery#cookie-based-authentication) に基づいて選択されています。

* トークンベースのプロトコルを使用すると、トークンがすべての要求で送信されないため、攻撃対象領域が小さくなります。
* サーバー エンドポイントでは、トークンが明示的に送信されるため、[クロスサイト リクエスト フォージェリ (CSRF)](xref:security/anti-request-forgery) に対する保護が必要ありません。 これにより、MVC または Razor ページのアプリと共に、Blazor WebAssembly アプリをホストすることができます。
* トークンの権限は Cookie よりも狭くなります。 たとえば、該当する機能が明示的に実装されていない限り、トークンを使用してユーザー アカウントを管理したり、ユーザーのパスワードを変更したりできません。
* トークンの有効期間は短く、既定では 1 時間です。これにより、攻撃時間が制限されます。 トークンは、いつでも取り消すことができます。
* 自己完結型の JWT は、クライアントとサーバーの認証プロセスを保証します。 たとえば、クライアントには、受信したトークンが正当であり、指定した認証プロセスの一環として出力されたことを検出して検証する手段があります。 サード パーティが認証プロセスの途中でトークンを切り替えようとすると、クライアントは切り替えられたトークンを検出して使用しないようにすることができます。
* OAuth および OIDC を使用したトークンは、アプリが安全であることを確認するために、正しく動作しているユーザー エージェントに依存しません。
* OAuth や OIDC などのトークンベースのプロトコルでは、同じセキュリティ特性のセットを使用して、ホストされたアプリとスタンドアロンのアプリの認証と認可を行うことができます。

## <a name="authentication-process-with-oidc"></a>OIDC を使用した認証プロセス

`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリには、OIDC を使用した認証と認可を実装するためのいくつかのプリミティブが用意されています。 大まかにいうと、認証は次のようにして行われます。

* 匿名ユーザーがログイン ボタンを選択する、または [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性が適用されたページを要求すると、そのユーザーがアプリのログイン ページ (`/authentication/login`) にリダイレクトされます。
* ログイン ページで、認証ライブラリが承認エンドポイントへのリダイレクトを準備します。 承認エンドポイントは、Blazor WebAssembly の外部にあり、別のオリジンでホストすることができます。 エンドポイントは、ユーザーが認証されているかどうかを判断し、応答として 1 つ以上のトークンを発行する役割を担います。 認証ライブラリは、認証応答を受信するためのログイン コールバックを提供します。
  * ユーザーが認証されていない場合、そのユーザーは基になる認証システム (通常は ASP.NET Core の ID) にリダイレクトされます。
  * ユーザーが既に認証されている場合、承認エンドポイントは適切なトークンを生成し、ブラウザーをログイン コールバック エンドポイント (`/authentication/login-callback`) にリダイレクトします。
* Blazor WebAssembly アプリでログイン コールバック エンドポイント (`/authentication/login-callback`) が読み込まれると、認証応答が処理されます。
  * 認証プロセスが正常に完了した場合、ユーザーは認証され、必要に応じてユーザーが要求した元の保護された URL に戻されます。
  * 何らかの理由で認証プロセスが失敗した場合、ユーザーはログイン失敗ページ (`/authentication/login-failed`) に送信され、エラーが表示されます。

## <a name="support-prerendering-with-authentication"></a>認証を使用したプリレンダリングのサポート

ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。

* 承認が不要なパスをプリレンダリングする。
* 承認が必要なパスをプリレンダリングしない。

クライアント アプリの `Program` クラス (*Program.cs*) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        services.AddBaseAddressHttpClient();
        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

サーバー アプリの `Startup.Configure` メソッドで、`endpoints.MapFallbackToFile("index.html")` を `endpoints.MapFallbackToPage("/_Host")` に置き換えます。

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

サーバー アプリで、*Pages* フォルダーが存在しない場合は作成します。 サーバー アプリの *Pages* フォルダー内に *_Host.cshtml* ページを作成します。 クライアント アプリの *wwwroot/index.html* ファイルの内容を *Pages/_Host.cshtml* ファイル内に貼り付けます。 ファイルの内容を更新します。

* ファイルの先頭に、`@page "_Host"` を追加します。
* `<app>Loading...</app>` タグを次のように置き換えます。

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション

ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。 どれを選択するかは、シナリオによって異なります。

詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>ユーザーを認証して保護されたサードパーティ API のみを呼び出す

サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 このシナリオでは:

* アプリをホストしているサーバーは関与しません。
* サーバー上の API を保護することはできません。
* アプリでは、保護されたサードパーティ API のみを呼び出すことができます。

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す

サードパーティ ログイン プロバイダーで ID を構成します。 サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。

ユーザーがログインすると、認証プロセスの一環としてアクセス トークンと更新トークンが ID によって収集されます。 その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する

サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。 そこから、サードパーティのアクセス トークンを使用して、クライアント上の ID から直接、サードパーティ API リソースを呼び出します。

この方法はお勧めしません。 この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。 OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。 機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。

* サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。
* 同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う

クライアントからサーバー API への API 呼び出しを行います。 サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。

この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。

* サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。
* アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。
