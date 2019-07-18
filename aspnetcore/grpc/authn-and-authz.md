---
title: GRPC での認証と承認 (ASP.NET Core)
author: jamesnk
description: GRPC で認証と承認を使用して ASP.NET Core する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/07/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 49024295e4db7976924397bb24567d92d6298562
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308775"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="4a850-103">GRPC での認証と承認 (ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="4a850-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="4a850-104">[James のニュートン-キング](https://twitter.com/jamesnk)別</span><span class="sxs-lookup"><span data-stu-id="4a850-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="4a850-105">[サンプルコードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/)する[(ダウンロード方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4a850-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="4a850-106">GRPC サービスを呼び出しているユーザーを認証する</span><span class="sxs-lookup"><span data-stu-id="4a850-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="4a850-107">gRPC を[ASP.NET Core 認証](xref:security/authentication/identity)と共に使用すると、ユーザーを各呼び出しに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="4a850-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="4a850-108">Grpc と ASP.NET Core 認証を`Startup.Configure`使用するの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4a850-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="4a850-109">ASP.NET Core 認証ミドルウェアを登録する順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="4a850-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="4a850-110">との`UseAuthentication` `UseAuthorization`後`UseRouting` には常に`UseEndpoints`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4a850-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="4a850-111">認証がセットアップされると、を使用`ServerCallContext`して grpc サービスメソッドでユーザーにアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="4a850-111">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="4a850-112">ベアラートークン認証</span><span class="sxs-lookup"><span data-stu-id="4a850-112">Bearer token authentication</span></span>

<span data-ttu-id="4a850-113">クライアントは、認証用のアクセストークンを提供できます。</span><span class="sxs-lookup"><span data-stu-id="4a850-113">The client can provide an access token for authentication.</span></span> <span data-ttu-id="4a850-114">サーバーはトークンを検証し、それを使用してユーザーを識別します。</span><span class="sxs-lookup"><span data-stu-id="4a850-114">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="4a850-115">サーバーでは、ベアラートークン認証は[JWT ベアラーミドルウェア](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="4a850-115">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="4a850-116">.NET gRPC クライアントでは、トークンをヘッダーとして呼び出しと共に送信できます。</span><span class="sxs-lookup"><span data-stu-id="4a850-116">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="4a850-117">クライアント証明書の認証</span><span class="sxs-lookup"><span data-stu-id="4a850-117">Client certificate authentication</span></span>

<span data-ttu-id="4a850-118">クライアントは、認証用にクライアント証明書を提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="4a850-118">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="4a850-119">[証明書の認証](https://tools.ietf.org/html/rfc5246#section-7.4.4)は、TLS レベルで実行されますが、その前に ASP.NET Core になります。</span><span class="sxs-lookup"><span data-stu-id="4a850-119">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="4a850-120">要求が ASP.NET Core 入力されると、[クライアント証明書の認証パッケージ](xref:security/authentication/certauth)によって、証明`ClaimsPrincipal`書をに解決できます。</span><span class="sxs-lookup"><span data-stu-id="4a850-120">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="4a850-121">クライアント証明書を受け入れるようにホストを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a850-121">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="4a850-122">Kestrel、IIS、および Azure でのクライアント証明書の受け入れの詳細については、「[証明書を要求するようにホストを構成する](xref:security/authentication/certauth#configure-your-host-to-require-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a850-122">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="4a850-123">.Net grpc クライアントでは、クライアント証明書がに`HttpClientHandler`追加され、次に grpc クライアントの作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4a850-123">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="4a850-124">その他の認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="4a850-124">Other authentication mechanisms</span></span>

<span data-ttu-id="4a850-125">ベアラートークンとクライアント証明書の認証に加えて、OAuth、OpenID、Negotiate など、サポートされている ASP.NET Core すべての認証メカニズムが gRPC で動作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a850-125">In addition to bearer token and client certificate authentication, all ASP.NET Core supported authentication mechanisms such as OAuth, OpenID and Negotiate should work with gRPC.</span></span> <span data-ttu-id="4a850-126">サーバー側で認証を構成する方法の詳細については、 [ASP.NET Core 認証](xref:security/authentication/identity)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a850-126">Visit [ASP.NET Core authentication](xref:security/authentication/identity) for more information for configuring authentication on the server side.</span></span>

<span data-ttu-id="4a850-127">クライアント側の構成は、使用している認証メカニズムによって異なります。</span><span class="sxs-lookup"><span data-stu-id="4a850-127">Client side configuration will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="4a850-128">以前のベアラートークンとクライアント証明書の認証例では、grpc 呼び出しを使用して認証メタデータを送信するように gRPC クライアントを構成するいくつかの方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4a850-128">The previous bearer token and client certificate authentication examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="4a850-129">厳密に型指定され`HttpClient`た grpc クライアントは、を内部的に使用します。</span><span class="sxs-lookup"><span data-stu-id="4a850-129">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="4a850-130">認証は、で[`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)構成することも、にカスタム[`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler)インスタンス`HttpClient`を追加することによって構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="4a850-130">Authentication can be configured on [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="4a850-131">各 grpc 呼び出しには、 `CallOptions`省略可能な引数があります。</span><span class="sxs-lookup"><span data-stu-id="4a850-131">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="4a850-132">カスタムヘッダーは、オプションの headers コレクションを使用して送信できます。</span><span class="sxs-lookup"><span data-stu-id="4a850-132">Custom headers can be sent using the option's headers collection.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="4a850-133">サービスとサービスメソッドへのアクセスをユーザーに承認する</span><span class="sxs-lookup"><span data-stu-id="4a850-133">Authorize users to access services and service methods</span></span>

<span data-ttu-id="4a850-134">既定では、サービス内のすべてのメソッドは、認証されていないユーザーが呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="4a850-134">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="4a850-135">認証を要求するには、 [[承認]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)属性をサービスに適用します。</span><span class="sxs-lookup"><span data-stu-id="4a850-135">To require authentication, apply the [[Authorize]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="4a850-136">`[Authorize]`属性のコンストラクター引数とプロパティを使用して、特定の[承認ポリシー](xref:security/authorization/policies)に一致するユーザーのみにアクセスを制限できます。</span><span class="sxs-lookup"><span data-stu-id="4a850-136">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="4a850-137">たとえば、という`MyAuthorizationPolicy`カスタム承認ポリシーがある場合は、次のコードを使用して、そのポリシーに一致するユーザーだけがサービスにアクセスできるようにします。</span><span class="sxs-lookup"><span data-stu-id="4a850-137">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="4a850-138">個々のサービスメソッドに属性`[Authorize]`を適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="4a850-138">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="4a850-139">現在のユーザーが、メソッドとクラスの**両方**に適用されているポリシーと一致しない場合、エラーが呼び出し元に返されます。</span><span class="sxs-lookup"><span data-stu-id="4a850-139">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="4a850-140">その他の資料</span><span class="sxs-lookup"><span data-stu-id="4a850-140">Additional resources</span></span>

* [<span data-ttu-id="4a850-141">ASP.NET Core でのベアラートークン認証</span><span class="sxs-lookup"><span data-stu-id="4a850-141">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="4a850-142">ASP.NET Core でクライアント証明書認証を構成する</span><span class="sxs-lookup"><span data-stu-id="4a850-142">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
