---
title: .NET Core での gRPC のトラブルシューティング
author: jamesnk
description: .NET Core で gRPC を使用しているときのエラーのトラブルシューティングを行います。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/26/2019
uid: grpc/troubleshoot
ms.openlocfilehash: 49bde2792f0fd7910de02d75f5f443000916dec7
ms.sourcegitcommit: de17150e5ec7507d7114dde0e5dbc2e45a66ef53
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70112751"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="d0ab6-103">.NET Core での gRPC のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="d0ab6-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="d0ab6-104">[James のニュートン-キング](https://twitter.com/jamesnk)別</span><span class="sxs-lookup"><span data-stu-id="d0ab6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d0ab6-105">このドキュメントでは、.NET で gRPC アプリを開発するときによく発生する問題について説明します。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="d0ab6-106">クライアントとサービスの SSL/TLS の構成が一致しません</span><span class="sxs-lookup"><span data-stu-id="d0ab6-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="d0ab6-107">GRPC テンプレートとサンプルでは、[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246)を使用して、既定で grpc サービスをセキュリティで保護しています。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="d0ab6-108">gRPC クライアントはセキュリティで保護された接続を使用して、セキュリティで保護された gRPC サービスを正常に呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="d0ab6-109">アプリの起動時に書き込まれたログで、ASP.NET Core gRPC サービスが TLS を使用していることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="d0ab6-110">サービスは、HTTPS エンドポイントでリッスンします。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="d0ab6-111">.Net Core クライアントは、サーバー `https`アドレスでを使用して、セキュリティで保護された接続で呼び出しを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

<span data-ttu-id="d0ab6-112">すべての gRPC クライアント実装は TLS をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="d0ab6-113">他の言語の gRPC クライアントには、通常、 `SslCredentials`で構成されたチャネルが必要です。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="d0ab6-114">`SslCredentials`クライアントが使用する証明書を指定します。この証明書は、セキュリティで保護されていない資格情報の代わりに使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="d0ab6-115">異なる gRPC クライアント実装で TLS を使用するように構成する例については、「 [Grpc 認証](https://www.grpc.io/docs/guides/auth/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="d0ab6-116">信頼されていない/無効な証明書を使用して gRPC サービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="d0ab6-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="d0ab6-117">.NET gRPC クライアントでは、サービスに信頼された証明書が必要です。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="d0ab6-118">信頼された証明書を使用せずに gRPC サービスを呼び出すと、次のエラーメッセージが返されます。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="d0ab6-119">ハンドルされていない例外です。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-119">Unhandled exception.</span></span> <span data-ttu-id="d0ab6-120">System.net.http.httprequestexception (システム):SSL 接続を確立できませんでした。内部例外を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="d0ab6-121">---> の認証を行います。 AuthenticationException:検証プロシージャによると、リモート証明書が無効です。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="d0ab6-122">このエラーは、アプリをローカルでテストしていて、ASP.NET Core HTTPS 開発証明書が信頼されていない場合に表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="d0ab6-123">この問題を解決する手順については、「 [Windows および macOS で ASP.NET CORE HTTPS 開発証明書を信頼](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="d0ab6-124">別のコンピューターで gRPC サービスを呼び出していて、その証明書を信頼できない場合、gRPC クライアントは無効な証明書を無視するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="d0ab6-125">次のコードでは、 [Httpclienthandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback)を使用して、信頼された証明書がない呼び出しを許可します。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) => true;

var httpClient = new HttpClient(httpClientHandler);
httpClient.BaseAddress = new Uri("https://localhost:5001");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

> [!WARNING]
> <span data-ttu-id="d0ab6-126">信頼されていない証明書は、アプリの開発中にのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="d0ab6-127">実稼働アプリケーションでは、常に有効な証明書を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="d0ab6-128">.NET Core クライアントを使用してセキュリティで保護される gRPC サービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="d0ab6-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="d0ab6-129">.NET Core クライアントでセキュリティで保護されていない gRPC サービスを呼び出すには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="d0ab6-130">Grpc クライアントでは、 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport`スイッチをに`true`設定し、サーバーアドレスでを使用`http`する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The address starts with "http://"
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="d0ab6-131">MacOS で gRPC アプリを開始できません ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0ab6-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="d0ab6-132">Kestrel では、macOS での TLS と Windows 7 などの古いバージョンの HTTP/2 はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="d0ab6-133">ASP.NET Core gRPC テンプレートとサンプルでは、既定で TLS が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="d0ab6-134">GRPC サーバーを起動しようとすると、次のエラーメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="d0ab6-135">IPv4 ループバックインターフェイスの https://localhost:5001 にバインドできません。' HTTP/2 over TLS は、ALPN サポートがないため、macOS ではサポートされていません。 '。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="d0ab6-136">この問題を回避するには、TLS を使用*せず*に HTTP/2 を使用するように Kestrel と grpc クライアントを構成します。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="d0ab6-137">これは開発時にのみ実行してください。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-137">You should only do this during development.</span></span> <span data-ttu-id="d0ab6-138">TLS を使用しないと、gRPC メッセージが暗号化なしで送信されます。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="d0ab6-139">Kestrel では、 *Program.cs*で TLS を使用せずに HTTP/2 エンドポイントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d0ab6-140">HTTP/2 エンドポイントが TLS を使用せずに構成されている場合、エンドポイントの`HttpProtocols.Http2` [listenoptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols)がに設定されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="d0ab6-141">`HttpProtocols.Http1AndHttp2`HTTP/2 のネゴシエートに TLS が必要であるため、を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="d0ab6-142">TLS を使用しない場合、エンドポイントへのすべての接続は既定の HTTP/1.1 に設定され、gRPC の呼び出しは失敗します。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="d0ab6-143">GRPC クライアントは、TLS を使用しないように構成する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="d0ab6-144">詳細については、「 [.Net Core クライアントを使用した安全でない gRPC サービスの呼び出し](#call-insecure-grpc-services-with-net-core-client)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="d0ab6-145">TLS を使用しない HTTP/2 は、アプリの開発中にのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="d0ab6-146">運用アプリでは、常にトランスポートセキュリティを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-146">Production apps should always use transport security.</span></span> <span data-ttu-id="d0ab6-147">詳細については、「 [gRPC のセキュリティに関する考慮事項 ASP.NET Core](xref:grpc/security#transport-security)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="d0ab6-148">grpc C#資産は、  *\*proto*ファイルから生成されたコードではありません</span><span class="sxs-lookup"><span data-stu-id="d0ab6-148">gRPC C# assets are not code generated from *\*.proto* files</span></span>

<span data-ttu-id="d0ab6-149">具象クライアントとサービス基底クラスの gRPC コード生成には、protobuf ファイルとツールをプロジェクトから参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="d0ab6-150">次のものを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-150">You must include:</span></span>

* <span data-ttu-id="d0ab6-151">`<Protobuf>`項目グループで使用する*プロトコルファイル。*</span><span class="sxs-lookup"><span data-stu-id="d0ab6-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="d0ab6-152">[インポートさ*れたプロトコル*ファイル](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)は、プロジェクトによって参照される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="d0ab6-153">GRPC ツールパッケージ[grpc](https://www.nuget.org/packages/Grpc.Tools/)に対するパッケージリファレンス。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="d0ab6-154">GRPC C#アセットの生成の詳細について<xref:grpc/basics>は、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="d0ab6-155">既定では、 `<Protobuf>`参照によって具象クライアントとサービス基本クラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="d0ab6-156">参照要素の属性`GrpcServices`は、資産の生成をC#制限するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="d0ab6-157">有効`GrpcServices`なオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="d0ab6-158">`Both`(存在しない場合の既定値)</span><span class="sxs-lookup"><span data-stu-id="d0ab6-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="d0ab6-159">GRPC サービスをホストしている ASP.NET Core web アプリには、生成されたサービス基本クラスのみが必要です。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="d0ab6-160">GRPC 呼び出しを行う gRPC クライアントアプリでは、具象クライアントのみが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d0ab6-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
