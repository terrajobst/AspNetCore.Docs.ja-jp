---
title: .NET Core での gRPC のトラブルシューティング
author: jamesnk
description: .NET Core で gRPC を使用している場合のエラーのトラブルシューティングを行います。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649364"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="440a1-103">.NET Core での gRPC のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="440a1-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="440a1-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="440a1-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="440a1-105">このドキュメントでは、.NET で gRPC アプリを開発する際によく発生する問題について説明します。</span><span class="sxs-lookup"><span data-stu-id="440a1-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="440a1-106">クライアントとサービスの SSL/TLS 構成が一致しない</span><span class="sxs-lookup"><span data-stu-id="440a1-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="440a1-107">GRPC テンプレートとサンプルでは、[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246) を使用して、既定で gRPC サービスをセキュリティ保護しています。</span><span class="sxs-lookup"><span data-stu-id="440a1-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="440a1-108">gRPC クライアントは、セキュリティ保護された gRPC サービスを正常に呼び出すために、セキュリティ保護された接続を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="440a1-109">アプリの起動時に書き込まれたログで、ASP.NET Core gRPC サービスが TLS を使用していることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="440a1-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="440a1-110">サービスは、HTTPS エンドポイントでリッスンします。</span><span class="sxs-lookup"><span data-stu-id="440a1-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="440a1-111">.NET Core クライアントは、サーバー アドレスで `https` を使用して、セキュリティ保護された接続で呼び出しを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="440a1-112">すべての gRPC クライアント実装で TLS をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="440a1-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="440a1-113">他の言語の gRPC クライアントでは、通常、`SslCredentials` によって構成されたチャネルが必要です。</span><span class="sxs-lookup"><span data-stu-id="440a1-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="440a1-114">`SslCredentials` は、クライアントで使用する証明書を指定し、安全でない資格情報の代わりにそれが使用される必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="440a1-115">さまざまな gRPC クライアント実装で TLS を使用するように構成する例については、[gRPC 認証に関するページ](https://www.grpc.io/docs/guides/auth/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="440a1-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="440a1-116">信頼されていないか無効な証明書で gRPC サービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="440a1-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="440a1-117">.NET gRPC クライアントでは、サービスに信頼された証明書が必要です。</span><span class="sxs-lookup"><span data-stu-id="440a1-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="440a1-118">信頼された証明書を使用せずに gRPC サービスを呼び出すと、次のエラーメッセージが返されます。</span><span class="sxs-lookup"><span data-stu-id="440a1-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="440a1-119">ハンドルされていない例外です。</span><span class="sxs-lookup"><span data-stu-id="440a1-119">Unhandled exception.</span></span> <span data-ttu-id="440a1-120">System.Net.Http.HttpRequestException:SSL 接続を確立できませんでした。内部例外を参照してください。</span><span class="sxs-lookup"><span data-stu-id="440a1-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="440a1-121">---> System.Security.Authentication.AuthenticationException:検証プロシージャによると、リモート証明書は無効です。</span><span class="sxs-lookup"><span data-stu-id="440a1-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="440a1-122">このエラーは、アプリをローカルでテストしていて、ASP.NET Core HTTPS 開発証明書が信頼されていない場合に表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="440a1-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="440a1-123">この問題を解決する手順については、「[Windows と macOS で ASP.NET Core HTTPS 開発証明書を信頼します](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="440a1-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="440a1-124">別のコンピューターで gRPC サービスを呼び出しており、その証明書を信頼できない場合、gRPC クライアントが無効な証明書を無視するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="440a1-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="440a1-125">次のコードでは [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) を使用して、信頼された証明書を使用しない呼び出しを許可しています。</span><span class="sxs-lookup"><span data-stu-id="440a1-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="440a1-126">信頼されていない証明書は、アプリの開発時にのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="440a1-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="440a1-127">実稼働アプリでは、常に有効な証明書を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="440a1-128">.NET Core クライアントで安全でない gRPC サービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="440a1-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="440a1-129">.NET Core クライアントで安全でない gRPC サービスを呼び出すには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="440a1-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="440a1-130">gRPC クライアントは、`System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` スイッチを `true` に設定し、サーバー アドレスで `http` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="440a1-131">MacOS で ASP.NET Core gRPC アプリを起動できない</span><span class="sxs-lookup"><span data-stu-id="440a1-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="440a1-132">Kestrel では、macOS や Windows 7 などの古い Windows バージョンでの TLS による HTTP/2 がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="440a1-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="440a1-133">ASP.NET Core gRPC テンプレートとサンプルでは、既定で TLS を使用しています。</span><span class="sxs-lookup"><span data-stu-id="440a1-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="440a1-134">gRPC サーバーを起動しようとすると、次のエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="440a1-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="440a1-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface:'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'. (IPv4 ループバック インターフェイスで https://localhost:5001 にバインドできません。'HTTP/2 over TLS は ALPN サポートがないため、macOS でサポートされていません。')</span><span class="sxs-lookup"><span data-stu-id="440a1-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="440a1-136">この問題を回避するには、TLS を*使用せずに* HTTP/2 を使用するように Kestrel と gRPC クライアントを構成します。</span><span class="sxs-lookup"><span data-stu-id="440a1-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="440a1-137">これは開発時にのみ実行してください。</span><span class="sxs-lookup"><span data-stu-id="440a1-137">You should only do this during development.</span></span> <span data-ttu-id="440a1-138">TLS を使用しないと、gRPC メッセージが暗号化されずに送信されます。</span><span class="sxs-lookup"><span data-stu-id="440a1-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="440a1-139">Kestrel では、*Program.cs* で TLS を使用せずに HTTP/2 エンドポイントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="440a1-140">HTTP/2 エンドポイントが TLS を使用せずに構成されている場合、エンドポイントの [ListenOptions.Protocol](xref:fundamentals/servers/kestrel#listenoptionsprotocols) を `HttpProtocols.Http2` に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="440a1-141">HTTP/2 のネゴシエートに TLS が必要であるため、`HttpProtocols.Http1AndHttp2` は使用できません。</span><span class="sxs-lookup"><span data-stu-id="440a1-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="440a1-142">TLS を使用しない場合、エンドポイントへのすべての接続が既定で HTTP/1.1 に設定され、gRPC の呼び出しが失敗します。</span><span class="sxs-lookup"><span data-stu-id="440a1-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="440a1-143">GRPC クライアントでも、TLS を使用しないように構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="440a1-144">詳細については、「[.NET Core クライアントで安全でない gRPC サービスを呼び出す](#call-insecure-grpc-services-with-net-core-client)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="440a1-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="440a1-145">TLS を使用しない HTTP/2 は、アプリの開発時にのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="440a1-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="440a1-146">実稼働アプリでは、常にトランスポート セキュリティを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-146">Production apps should always use transport security.</span></span> <span data-ttu-id="440a1-147">詳細については、[ gRPC for ASP.NET Core のセキュリティの考慮事項 ](xref:grpc/security#transport-security)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="440a1-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="440a1-148">gRPC C# アセットが .proto ファイルから生成されたコードでない</span><span class="sxs-lookup"><span data-stu-id="440a1-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="440a1-149">具象クライアントとサービス基本クラスの gRPC コード生成には、protobuf ファイルとツールをプロジェクトから参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="440a1-150">次のものを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-150">You must include:</span></span>

* <span data-ttu-id="440a1-151">`<Protobuf>` 項目グループで使用する *.proto* ファイル。</span><span class="sxs-lookup"><span data-stu-id="440a1-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="440a1-152">[インポートされた *.proto* ファイル](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) をプロジェクトで参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="440a1-153">gRPC ツール パッケージ [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) へのパッケージ参照。</span><span class="sxs-lookup"><span data-stu-id="440a1-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="440a1-154">gRPC C# アセットの生成の詳細については、「<xref:grpc/basics>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="440a1-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="440a1-155">既定で、`<Protobuf>` 参照によって、具象クライアントとサービス基本クラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="440a1-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="440a1-156">参照要素の `GrpcServices` 属性を使用して、C# アセットの生成を制限できます。</span><span class="sxs-lookup"><span data-stu-id="440a1-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="440a1-157">有効な `GrpcServices` オプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="440a1-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="440a1-158">`Both` (存在しない場合の既定値)</span><span class="sxs-lookup"><span data-stu-id="440a1-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="440a1-159">gRPC サービスをホストしている ASP.NET Core Web アプリには、生成されたサービス基本クラスのみが必要です。</span><span class="sxs-lookup"><span data-stu-id="440a1-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="440a1-160">gRPC 呼び出しを行う gRPC クライアント アプリでは、生成された具象クライアントのみが必要です。</span><span class="sxs-lookup"><span data-stu-id="440a1-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="440a1-161">WPF プロジェクトで .proto ファイルから gRPC C# アセットを生成できない</span><span class="sxs-lookup"><span data-stu-id="440a1-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="440a1-162">WPF プロジェクトには、gRPC コードの生成が正常に機能しなくなる[既知の問題](https://github.com/dotnet/wpf/issues/810)があります。</span><span class="sxs-lookup"><span data-stu-id="440a1-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="440a1-163">WPF プロジェクトで `Grpc.Tools` と *.proto* ファイルを参照して生成された gRPC 型は、使用すると、コンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="440a1-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="440a1-164">エラー CS0246:型または名前空間の名前 'tMyGrpcServices' が見つかりませんでした (using ディレクティブまたはアセンブリ参照が指定されていることを確認してください)</span><span class="sxs-lookup"><span data-stu-id="440a1-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="440a1-165">この問題は次の方法で回避できます。</span><span class="sxs-lookup"><span data-stu-id="440a1-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="440a1-166">新しい .NET Core クラス ライブラリ プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="440a1-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="440a1-167">新しいプロジェクトで、参照を追加して、[ *\*.proto* ファイルからの C# コード生成](xref:grpc/basics#generated-c-assets)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="440a1-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="440a1-168">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) パッケージにパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="440a1-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="440a1-169">`<Protobuf>` 項目グループに *\*.proto* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="440a1-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="440a1-170">WPF アプリケーションで、新しいプロジェクトに参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="440a1-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="440a1-171">WPF アプリケーションでは、新しいクラス ライブラリ プロジェクトから、gRPC によって生成された型を使用できます。</span><span class="sxs-lookup"><span data-stu-id="440a1-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
