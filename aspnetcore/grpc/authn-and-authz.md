---
title: GRPC での認証と承認 (ASP.NET Core)
author: jamesnk
description: GRPC で認証と承認を使用して ASP.NET Core する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/26/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 34f7f8a5a22159329b3d6c4524943434c460c7fb
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602428"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>GRPC での認証と承認 (ASP.NET Core)

[James のニュートン-キング](https://twitter.com/jamesnk)別

[サンプルコードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/)する[(ダウンロード方法)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>GRPC サービスを呼び出しているユーザーを認証する

gRPC を[ASP.NET Core 認証](xref:security/authentication/identity)と共に使用すると、ユーザーを各呼び出しに関連付けることができます。

Grpc と ASP.NET Core 認証を`Startup.Configure`使用するの例を次に示します。

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        routes.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> ASP.NET Core 認証ミドルウェアを登録する順序は重要です。 との`UseAuthentication` `UseAuthorization`後`UseRouting` には常に`UseEndpoints`を呼び出します。

認証がセットアップされると、を使用`ServerCallContext`して grpc サービスメソッドでユーザーにアクセスできるようになります。

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>ベアラートークン認証

クライアントは、認証用のアクセストークンを提供できます。 サーバーはトークンを検証し、それを使用してユーザーを識別します。

サーバーでは、ベアラートークン認証は[JWT ベアラーミドルウェア](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)を使用して構成されます。

.NET gRPC クライアントでは、トークンをヘッダーとして呼び出しと共に送信できます。

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

### <a name="client-certificate-authentication"></a>クライアント証明書の認証

クライアントは、認証用にクライアント証明書を提供することもできます。 [証明書の認証](https://tools.ietf.org/html/rfc5246#section-7.4.4)は、TLS レベルで実行されますが、その前に ASP.NET Core になります。 要求が ASP.NET Core 入力されると、[クライアント証明書の認証パッケージ](xref:security/authentication/certauth)によって、証明`ClaimsPrincipal`書をに解決できます。

> [!NOTE]
> クライアント証明書を受け入れるようにホストを構成する必要があります。 Kestrel、IIS、および Azure でのクライアント証明書の受け入れの詳細については、「[証明書を要求するようにホストを構成する](xref:security/authentication/certauth#configure-your-host-to-require-certificates)」を参照してください。

.Net grpc クライアントでは、クライアント証明書がに`HttpClientHandler`追加され、次に grpc クライアントの作成に使用されます。

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC client
    var httpClient = new HttpClient(handler);
    httpClient.BaseAddress = new Uri(baseAddress);

    return GrpcClient.Create<Ticketer.TicketerClient>(httpClient);
}
```

### <a name="other-authentication-mechanisms"></a>その他の認証メカニズム

GRPC では、ASP.NET Core サポートされている多くの認証メカニズムが使用できます。

* Azure Active Directory
* クライアント証明書
* IdentityServer
* JWT トークン
* OAuth 2.0
* OpenID Connect
* WS-Federation

サーバーでの認証の構成の詳細については、「 [ASP.NET Core 認証](xref:security/authentication/identity)」を参照してください。

認証を使用するように gRPC クライアントを構成することは、使用している認証メカニズムによって異なります。 前のベアラートークンとクライアント証明書の例では、grpc 呼び出しを使用して認証メタデータを送信するように gRPC クライアントを構成するいくつかの方法を示しています。

* 厳密に型指定され`HttpClient`た grpc クライアントは、を内部的に使用します。 認証は、で[`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)構成することも、にカスタム[`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler)インスタンス`HttpClient`を追加することによって構成することもできます。
* 各 grpc 呼び出しには、 `CallOptions`省略可能な引数があります。 カスタムヘッダーは、オプションの headers コレクションを使用して送信できます。

> [!NOTE]
> Windows 認証 (NTLM/Kerberos/Negotiate) を gRPC で使用することはできません。 gRPC には HTTP/2 が必要ですが、HTTP/2 は Windows 認証をサポートしていません。

## <a name="authorize-users-to-access-services-and-service-methods"></a>サービスとサービスメソッドへのアクセスをユーザーに承認する

既定では、サービス内のすべてのメソッドは、認証されていないユーザーが呼び出すことができます。 認証を要求するには、 [[承認]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)属性をサービスに適用します。

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

`[Authorize]`属性のコンストラクター引数とプロパティを使用して、特定の[承認ポリシー](xref:security/authorization/policies)に一致するユーザーのみにアクセスを制限できます。 たとえば、という`MyAuthorizationPolicy`カスタム承認ポリシーがある場合は、次のコードを使用して、そのポリシーに一致するユーザーだけがサービスにアクセスできるようにします。

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

個々のサービスメソッドに属性`[Authorize]`を適用することもできます。 現在のユーザーが、メソッドとクラスの**両方**に適用されているポリシーと一致しない場合、エラーが呼び出し元に返されます。

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>その他の資料

* [ASP.NET Core でのベアラートークン認証](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [ASP.NET Core でクライアント証明書認証を構成する](xref:security/authentication/certauth)
