---
title: ASP.NET Core のための gRPC での認証と承認
author: jamesnk
description: ASP.NET Core のために gRPC で認証と承認を使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: c0312b186bbb35e3b802984484b7213016d8bf04
ms.sourcegitcommit: 51c86c003ab5436598dbc42f26ea4a83a795fd6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2020
ms.locfileid: "78964443"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="2bc78-103">ASP.NET Core のための gRPC での認証と承認</span><span class="sxs-lookup"><span data-stu-id="2bc78-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="2bc78-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2bc78-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="2bc78-105">[サンプル コードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) ([ダウンロード方法](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2bc78-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="2bc78-106">gRPC サービスを呼び出しているユーザーを認証する</span><span class="sxs-lookup"><span data-stu-id="2bc78-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="2bc78-107">gRPC を [ASP.NET Core 認証](xref:security/authentication/identity)と共に使用して、ユーザーを各呼び出しに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="2bc78-108">以下に、gRPC と ASP.NET Core 認証を使用する `Startup.Configure` の例を示します。</span><span class="sxs-lookup"><span data-stu-id="2bc78-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> <span data-ttu-id="2bc78-109">ASP.NET Core 認証ミドルウェアを登録する順序が重要です。</span><span class="sxs-lookup"><span data-stu-id="2bc78-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="2bc78-110">必ず `UseRouting` の後に `UseAuthentication` と `UseAuthorization` を呼び出し、その後に `UseEndpoints` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2bc78-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="2bc78-111">呼び出し時にアプリによって使用される認証メカニズムは、構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2bc78-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="2bc78-112">認証の構成は、`Startup.ConfigureServices` に追加され、アプリで使用される認証メカニズムによって異なるものとなります。</span><span class="sxs-lookup"><span data-stu-id="2bc78-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="2bc78-113">ASP.NET Core アプリをセキュリティで保護する方法の例については、[認証サンプル](xref:security/authentication/samples)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bc78-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="2bc78-114">認証が設定されると、`ServerCallContext` を介して gRPC サービス メソッドでユーザーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="2bc78-115">ベアラー トークン認証</span><span class="sxs-lookup"><span data-stu-id="2bc78-115">Bearer token authentication</span></span>

<span data-ttu-id="2bc78-116">クライアントは認証のためのアクセス トークンを提供できます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="2bc78-117">サーバーはトークンを検証し、それを使用してユーザーを識別します。</span><span class="sxs-lookup"><span data-stu-id="2bc78-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="2bc78-118">サーバーでは、[JWT ベアラー ミドルウェア](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)を使用してベアラー トークン認証が構成されます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="2bc78-119">.NET gRPC クライアントでは、呼び出しで、トークンをヘッダーとして送信できます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-119">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

<span data-ttu-id="2bc78-120">チャネルで `ChannelCredentials` を構成することは、gRPC 呼び出しを使用してトークンをサービスに送信するもう 1 つの方法です。</span><span class="sxs-lookup"><span data-stu-id="2bc78-120">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="2bc78-121">gRPC 呼び出しが行われるたびに資格情報が実行されるので、コードを複数の場所に記述してユーザー自身でトークンを渡す必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="2bc78-121">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="2bc78-122">次の例の資格情報では、すべての gRPC 呼び出しでトークンを送信するようにチャネルを構成しています。</span><span class="sxs-lookup"><span data-stu-id="2bc78-122">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="2bc78-123">クライアント証明書認証</span><span class="sxs-lookup"><span data-stu-id="2bc78-123">Client certificate authentication</span></span>

<span data-ttu-id="2bc78-124">別の方法として、クライアントから認証のためにクライアント証明書を提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-124">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="2bc78-125">[証明書の認証](https://tools.ietf.org/html/rfc5246#section-7.4.4)は、ASP.NET Core に到達するずっと前に TLS レベルで実行されます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-125">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="2bc78-126">要求が ASP.NET Core に到達すると、[クライアント証明書の認証パッケージ](xref:security/authentication/certauth)によって、証明書を `ClaimsPrincipal` に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2bc78-126">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="2bc78-127">ホストは、クライアント証明書を受け入れるように構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2bc78-127">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="2bc78-128">Kestrel、IIS、および Azure でのクライアント証明書の受け付けについては、「[証明書を要求するようにホストを構成する](xref:security/authentication/certauth#configure-your-host-to-require-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bc78-128">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="2bc78-129">.NET gRPC クライアントでは、クライアント証明書は `HttpClientHandler` に追加されます。これがその後、gRPC クライアントの作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-129">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="2bc78-130">その他の認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="2bc78-130">Other authentication mechanisms</span></span>

<span data-ttu-id="2bc78-131">ASP.NET Core でサポートされている多くの認証メカニズムを、gRPC で使用できます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-131">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="2bc78-132">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="2bc78-132">Azure Active Directory</span></span>
* <span data-ttu-id="2bc78-133">クライアント証明書</span><span class="sxs-lookup"><span data-stu-id="2bc78-133">Client Certificate</span></span>
* <span data-ttu-id="2bc78-134">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="2bc78-134">IdentityServer</span></span>
* <span data-ttu-id="2bc78-135">JWT トークン</span><span class="sxs-lookup"><span data-stu-id="2bc78-135">JWT Token</span></span>
* <span data-ttu-id="2bc78-136">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="2bc78-136">OAuth 2.0</span></span>
* <span data-ttu-id="2bc78-137">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="2bc78-137">OpenID Connect</span></span>
* <span data-ttu-id="2bc78-138">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="2bc78-138">WS-Federation</span></span>

<span data-ttu-id="2bc78-139">サーバーで認証を構成することの詳細については、[ASP.NET Core 認証](xref:security/authentication/identity)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2bc78-139">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="2bc78-140">認証を使用するための gRPC クライアントの構成は、使おうとしている認証メカニズムによって異なります。</span><span class="sxs-lookup"><span data-stu-id="2bc78-140">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="2bc78-141">前述したベアラー トークンとクライアント証明書の例では、gRPC 呼び出しで認証メタデータを送信するように gRPC クライアントを構成できるいくつかの方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="2bc78-141">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="2bc78-142">厳密に型指定された gRPC クライアントは内部的に `HttpClient` を使用します。</span><span class="sxs-lookup"><span data-stu-id="2bc78-142">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="2bc78-143">認証は、[HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) で構成することも、カスタム [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) インスタンスを `HttpClient` に追加して構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-143">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="2bc78-144">それぞれの gRPC 呼び出しに、省略可能な `CallOptions` 引数があります。</span><span class="sxs-lookup"><span data-stu-id="2bc78-144">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="2bc78-145">オプションのヘッダー コレクションを使用して、カスタム ヘッダーを送信できます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-145">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="2bc78-146">gRPC で Windows 認証 (NTLM/Kerberos/ネゴシエート) を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="2bc78-146">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="2bc78-147">gRPC には HTTP/2 が必要で、HTTP/2 では Windows 認証がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="2bc78-147">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="2bc78-148">サービスやサービス メソッドにアクセスするユーザーを承認する</span><span class="sxs-lookup"><span data-stu-id="2bc78-148">Authorize users to access services and service methods</span></span>

<span data-ttu-id="2bc78-149">既定では、認証されていないユーザーが、サービスのすべてのメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-149">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="2bc78-150">認証を要求するには、サービスに [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="2bc78-150">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="2bc78-151">コンストラクターの引数と `[Authorize]` 属性のプロパティを使用して、特定の[承認ポリシー](xref:security/authorization/policies)に一致するユーザーのみにアクセスを制限できます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="2bc78-152">たとえば、`MyAuthorizationPolicy` というカスタム承認ポリシーがある場合は、次のコードを使用して、そのポリシーに一致するユーザーだけがサービスにアクセスできるようにします。</span><span class="sxs-lookup"><span data-stu-id="2bc78-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="2bc78-153">個々のサービス メソッドに `[Authorize]` 属性を適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-153">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="2bc78-154">現在のユーザーが、メソッドとクラスの**両方**に適用されているポリシーと一致しない場合は、呼び出し元にエラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="2bc78-154">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="2bc78-155">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2bc78-155">Additional resources</span></span>

* [<span data-ttu-id="2bc78-156">ASP.NET Core でのベアラー トークン認証</span><span class="sxs-lookup"><span data-stu-id="2bc78-156">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="2bc78-157">ASP.NET Core でクライアント証明書の認証を構成する</span><span class="sxs-lookup"><span data-stu-id="2bc78-157">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
