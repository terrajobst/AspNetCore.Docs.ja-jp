---
title: GRPC での認証と承認 (ASP.NET Core)
author: jamesnk
description: GRPC で認証と承認を使用して ASP.NET Core する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/13/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: e8dd384ec43a66e56891925dcaa529085fa200c7
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999862"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="520b0-103">GRPC での認証と承認 (ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="520b0-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="520b0-104">[James のニュートン-キング](https://twitter.com/jamesnk)別</span><span class="sxs-lookup"><span data-stu-id="520b0-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="520b0-105">[サンプルコードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/)[する (ダウンロードする方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="520b0-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="520b0-106">GRPC サービスを呼び出しているユーザーを認証する</span><span class="sxs-lookup"><span data-stu-id="520b0-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="520b0-107">gRPC を[ASP.NET Core 認証](xref:security/authentication/identity)と共に使用すると、ユーザーを各呼び出しに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="520b0-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="520b0-108">GRPC と ASP.NET Core 認証を使用する @no__t 0 の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="520b0-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="520b0-109">ASP.NET Core 認証ミドルウェアを登録する順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="520b0-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="520b0-110">常に `UseAuthentication` を呼び出し、`UseRouting` と `UseEndpoints` の後に-1 を @no__t します。</span><span class="sxs-lookup"><span data-stu-id="520b0-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="520b0-111">呼び出し時にアプリが使用する認証メカニズムを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="520b0-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="520b0-112">認証の構成は @no__t 0 で追加されます。アプリが使用する認証メカニズムによって異なります。</span><span class="sxs-lookup"><span data-stu-id="520b0-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="520b0-113">ASP.NET Core アプリをセキュリティで保護する方法の例については、「[認証のサンプル](xref:security/authentication/samples)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="520b0-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="520b0-114">認証がセットアップされると、ユーザーには `ServerCallContext` を使用して gRPC サービスメソッドでアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="520b0-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="520b0-115">ベアラートークン認証</span><span class="sxs-lookup"><span data-stu-id="520b0-115">Bearer token authentication</span></span>

<span data-ttu-id="520b0-116">クライアントは、認証用のアクセストークンを提供できます。</span><span class="sxs-lookup"><span data-stu-id="520b0-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="520b0-117">サーバーはトークンを検証し、それを使用してユーザーを識別します。</span><span class="sxs-lookup"><span data-stu-id="520b0-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="520b0-118">サーバーでは、ベアラートークン認証は[JWT ベアラーミドルウェア](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="520b0-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="520b0-119">.NET gRPC クライアントでは、トークンをヘッダーとして呼び出しと共に送信できます。</span><span class="sxs-lookup"><span data-stu-id="520b0-119">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

<span data-ttu-id="520b0-120">チャネルで `ChannelCredentials` を構成することは、gRPC 呼び出しを使用してトークンをサービスに送信する別の方法です。</span><span class="sxs-lookup"><span data-stu-id="520b0-120">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="520b0-121">資格情報は gRPC 呼び出しが行われるたびに実行されます。これにより、トークンを自分で渡すためにコードを複数の場所に記述する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="520b0-121">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="520b0-122">次の例の資格情報は、すべての gRPC 呼び出しを使用してトークンを送信するようにチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="520b0-122">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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
    // Channels that aren't using TLS should use ChannelCredentials.Insecure instead.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="520b0-123">クライアント証明書の認証</span><span class="sxs-lookup"><span data-stu-id="520b0-123">Client certificate authentication</span></span>

<span data-ttu-id="520b0-124">クライアントは、認証用にクライアント証明書を提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="520b0-124">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="520b0-125">[証明書の認証](https://tools.ietf.org/html/rfc5246#section-7.4.4)は、TLS レベルで実行されますが、その前に ASP.NET Core になります。</span><span class="sxs-lookup"><span data-stu-id="520b0-125">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="520b0-126">要求が ASP.NET Core になると、[クライアント証明書の認証パッケージ](xref:security/authentication/certauth)によって、証明書を `ClaimsPrincipal` に解決できるようになります。</span><span class="sxs-lookup"><span data-stu-id="520b0-126">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="520b0-127">クライアント証明書を受け入れるようにホストを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="520b0-127">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="520b0-128">Kestrel、IIS、および Azure でのクライアント証明書の受け入れの詳細については、「[証明書を要求するようにホストを構成する](xref:security/authentication/certauth#configure-your-host-to-require-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="520b0-128">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="520b0-129">.NET gRPC クライアントでは、クライアント証明書が `HttpClientHandler` に追加され、次に gRPC クライアントの作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="520b0-129">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="520b0-130">その他の認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="520b0-130">Other authentication mechanisms</span></span>

<span data-ttu-id="520b0-131">GRPC では、ASP.NET Core サポートされている多くの認証メカニズムが使用できます。</span><span class="sxs-lookup"><span data-stu-id="520b0-131">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="520b0-132">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="520b0-132">Azure Active Directory</span></span>
* <span data-ttu-id="520b0-133">クライアント証明書</span><span class="sxs-lookup"><span data-stu-id="520b0-133">Client Certificate</span></span>
* <span data-ttu-id="520b0-134">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="520b0-134">IdentityServer</span></span>
* <span data-ttu-id="520b0-135">JWT トークン</span><span class="sxs-lookup"><span data-stu-id="520b0-135">JWT Token</span></span>
* <span data-ttu-id="520b0-136">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="520b0-136">OAuth 2.0</span></span>
* <span data-ttu-id="520b0-137">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="520b0-137">OpenID Connect</span></span>
* <span data-ttu-id="520b0-138">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="520b0-138">WS-Federation</span></span>

<span data-ttu-id="520b0-139">サーバーでの認証の構成の詳細については、「 [ASP.NET Core 認証](xref:security/authentication/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="520b0-139">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="520b0-140">認証を使用するように gRPC クライアントを構成することは、使用している認証メカニズムによって異なります。</span><span class="sxs-lookup"><span data-stu-id="520b0-140">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="520b0-141">前のベアラートークンとクライアント証明書の例では、grpc 呼び出しを使用して認証メタデータを送信するように gRPC クライアントを構成するいくつかの方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="520b0-141">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="520b0-142">厳密に型指定された gRPC クライアントは @no__t 0 を内部的に使用します。</span><span class="sxs-lookup"><span data-stu-id="520b0-142">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="520b0-143">認証は[`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)で構成することも、`HttpClient` にカスタム[の @no__t](/dotnet/api/system.net.http.httpmessagehandler)インスタンスを追加することによって構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="520b0-143">Authentication can be configured on [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="520b0-144">各 gRPC 呼び出しには、省略可能な @no__t 0 引数があります。</span><span class="sxs-lookup"><span data-stu-id="520b0-144">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="520b0-145">カスタムヘッダーは、オプションの headers コレクションを使用して送信できます。</span><span class="sxs-lookup"><span data-stu-id="520b0-145">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="520b0-146">Windows 認証 (NTLM/Kerberos/Negotiate) を gRPC で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="520b0-146">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="520b0-147">gRPC には HTTP/2 が必要ですが、HTTP/2 は Windows 認証をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="520b0-147">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="520b0-148">サービスとサービスメソッドへのアクセスをユーザーに承認する</span><span class="sxs-lookup"><span data-stu-id="520b0-148">Authorize users to access services and service methods</span></span>

<span data-ttu-id="520b0-149">既定では、サービス内のすべてのメソッドは、認証されていないユーザーが呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="520b0-149">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="520b0-150">認証を要求するには、 [[承認]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)属性をサービスに適用します。</span><span class="sxs-lookup"><span data-stu-id="520b0-150">To require authentication, apply the [[Authorize]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="520b0-151">@No__t-0 属性のコンストラクター引数とプロパティを使用して、特定の[承認ポリシー](xref:security/authorization/policies)に一致するユーザーのみにアクセスを制限できます。</span><span class="sxs-lookup"><span data-stu-id="520b0-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="520b0-152">たとえば、`MyAuthorizationPolicy` というカスタム承認ポリシーがある場合は、次のコードを使用して、そのポリシーに一致するユーザーだけがサービスにアクセスできることを確認します。</span><span class="sxs-lookup"><span data-stu-id="520b0-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="520b0-153">個々のサービスメソッドには、@no__t 0 属性も適用できます。</span><span class="sxs-lookup"><span data-stu-id="520b0-153">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="520b0-154">現在のユーザーが、メソッドとクラスの**両方**に適用されているポリシーと一致しない場合、エラーが呼び出し元に返されます。</span><span class="sxs-lookup"><span data-stu-id="520b0-154">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="520b0-155">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="520b0-155">Additional resources</span></span>

* [<span data-ttu-id="520b0-156">ASP.NET Core でのベアラートークン認証</span><span class="sxs-lookup"><span data-stu-id="520b0-156">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="520b0-157">ASP.NET Core でクライアント証明書認証を構成する</span><span class="sxs-lookup"><span data-stu-id="520b0-157">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
