---
title: ASP.NET Core 2.1 の新機能
author: isaac2004
description: ASP.NET Core 2.1 の新機能について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: d969b4caab44e3e50b3a0202b25864921d6d01dc
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880857"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="7ea26-103">ASP.NET Core 2.1 の新機能</span><span class="sxs-lookup"><span data-stu-id="7ea26-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="7ea26-104">この記事では、ASP.NET Core 2.1 の最も大きな変更点について説明します。また、その変更点のドキュメントへのリンクも示します。</span><span class="sxs-lookup"><span data-stu-id="7ea26-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

<span data-ttu-id="7ea26-105">ASP.NET Core 2.1 用に SignalR を書き直しました。</span><span class="sxs-lookup"><span data-stu-id="7ea26-105">SignalR has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="7ea26-106">ASP.NET Core SignalR には、多くの改善点が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="7ea26-107">簡略化されたスケール アウト モデル。</span><span class="sxs-lookup"><span data-stu-id="7ea26-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="7ea26-108">jQuery への依存関係がない新しい JavaScript クライアント。</span><span class="sxs-lookup"><span data-stu-id="7ea26-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="7ea26-109">MessagePack に基づく新しいコンパクトなバイナリ プロトコル。</span><span class="sxs-lookup"><span data-stu-id="7ea26-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="7ea26-110">カスタム プロトコルのサポート。</span><span class="sxs-lookup"><span data-stu-id="7ea26-110">Support for custom protocols.</span></span>
* <span data-ttu-id="7ea26-111">新しいストリーミング応答モデル。</span><span class="sxs-lookup"><span data-stu-id="7ea26-111">A new streaming response model.</span></span>
* <span data-ttu-id="7ea26-112">ベア WebSocket に基づいたクライアントのサポート。</span><span class="sxs-lookup"><span data-stu-id="7ea26-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="7ea26-113">詳細については、[ASP.NET Core SignalR](xref:signalr/introduction) に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="7ea26-114">Razor クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="7ea26-114">Razor class libraries</span></span>

<span data-ttu-id="7ea26-115">ASP.NET Core 2.1 を使用すると、Razor ベースの UI をビルドしてライブラリに含め、複数のプロジェクト間で共有することが容易になります。</span><span class="sxs-lookup"><span data-stu-id="7ea26-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="7ea26-116">新しい Razor SDK を使用すると、NuGet パッケージにパッケージ化できるクラス ライブラリ プロジェクトに Razor ファイルをビルドすることができます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="7ea26-117">アプリにより、ライブラリ内のビューとページを自動的に検出して、オーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="7ea26-118">ビルドに Razor コンパイルを統合すると、次のことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="7ea26-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="7ea26-119">アプリの起動時間が大幅に短縮されます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="7ea26-120">実行時の Razor ビューとページへの高速更新は、反復開発ワークフローの一部として引き続き利用できます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="7ea26-121">詳細については、[Razor クラス ライブラリ プロジェクトを使用した再利用可能 UI の作成](xref:razor-pages/ui-class)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="7ea26-122">Identity の UI ライブラリとスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="7ea26-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="7ea26-123">ASP.NET Core 2.1 では、[ASP.NET Core Identity](xref:security/authentication/identity) を [Razor クラス ライブラリ](xref:razor-pages/ui-class)として提供しています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="7ea26-124">Identity を含むアプリでは、新しい Identity スキャフォルダーを適用して、Identity Razor クラス ライブラリ (RCL) に含まれるソース コードを選択的に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="7ea26-125">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="7ea26-126">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="7ea26-127">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="7ea26-128">認証が**含まれていない**アプリでは、Identity スキャフォルダーを適用して RCL Identity パッケージを追加できます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="7ea26-129">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="7ea26-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="7ea26-130">詳細については、「[Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity)」 (ASP.NET Core プロジェクトの ID のスキャフォールディング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="7ea26-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="7ea26-131">HTTPS</span></span>

<span data-ttu-id="7ea26-132">セキュリティとプライバシーがより強化されたため、Web アプリの HTTPS を有効化することが重要です。</span><span class="sxs-lookup"><span data-stu-id="7ea26-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="7ea26-133">Web 上では、HTTPS の強制がますます厳しくなっています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="7ea26-134">HTTPS を使用していないサイトは、安全でないと見なされます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="7ea26-135">ブラウザー (Chromium、Mozilla) では、セキュリティで保護されたコンテキストから Web 機能を使用することを強制するようになってきています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="7ea26-136">[GDPR](xref:security/gdpr) では、ユーザー プライバシーの保護に HTTPS を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7ea26-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="7ea26-137">運用環境では HTTPS の使用が非常に重要ですが、開発環境で HTTPS を使用すると、展開における問題 (セキュリティ保護されていないリンクなど) を防止するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="7ea26-138">ASP.NET Core 2.1 には、開発環境での HTTPS の使用と、運用環境での HTTPS の構成を容易にする多くの機能強化が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="7ea26-139">詳細については、[HTTPS の適用](xref:security/enforcing-ssl)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="7ea26-140">既定でオン</span><span class="sxs-lookup"><span data-stu-id="7ea26-140">On by default</span></span>

<span data-ttu-id="7ea26-141">セキュリティで保護された Web サイトの開発を円滑にするため、HTTPS は既定でオンになっています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="7ea26-142">2\.1 以降では、Kestrel は `https://localhost:5001` でリッスンします (ローカル開発証明書が存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="7ea26-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="7ea26-143">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-143">A development certificate is created:</span></span>

* <span data-ttu-id="7ea26-144">SDK を初めて使用するときに、.NET Core SDK の最初の実行エクスペリエンスの一部として。</span><span class="sxs-lookup"><span data-stu-id="7ea26-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="7ea26-145">新しい `dev-certs` ツールを使用して手動で。</span><span class="sxs-lookup"><span data-stu-id="7ea26-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="7ea26-146">`dotnet dev-certs https --trust` を実行して証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="7ea26-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="7ea26-147">HTTPS のリダイレクトと強制</span><span class="sxs-lookup"><span data-stu-id="7ea26-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="7ea26-148">通常、Web アプリは、HTTP と HTTPS の両方でリッスンする必要がありますが、その一方で、すべての HTTP トラフィックを HTTPS にリダイレクトする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7ea26-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="7ea26-149">2\.1 では、構成またはバインドされたサーバー ポートの有無に基づいて、インテリジェントにリダイレクトする特殊な HTTPS リダイレクト ミドルウェアが導入されました。</span><span class="sxs-lookup"><span data-stu-id="7ea26-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="7ea26-150">[HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) を使用すると、HTTPS の使用をさらに強制することができます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="7ea26-151">HSTS は、常に HTTPS 経由でサイトにアクセスするようにブラウザーに指示します。</span><span class="sxs-lookup"><span data-stu-id="7ea26-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="7ea26-152">ASP.NET Core 2.1 では、最長有効期間、サブドメイン、HSTS プリロード リストのオプションをサポートする HSTS ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="7ea26-153">運用環境の構成</span><span class="sxs-lookup"><span data-stu-id="7ea26-153">Configuration for production</span></span>

<span data-ttu-id="7ea26-154">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7ea26-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="7ea26-155">2\.1 では、Kestrel に HTTPS を構成するための既定の構成スキーマが追加されています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="7ea26-156">以下を使用するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="7ea26-157">URL を含む複数のエンドポイント。</span><span class="sxs-lookup"><span data-stu-id="7ea26-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="7ea26-158">詳細については、[Kestrel Web サーバーの実装:エンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="7ea26-159">ディスク上のファイルまたは証明書ストアから HTTPS に使用する証明書。</span><span class="sxs-lookup"><span data-stu-id="7ea26-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="7ea26-160">GDPR</span><span class="sxs-lookup"><span data-stu-id="7ea26-160">GDPR</span></span>

<span data-ttu-id="7ea26-161">ASP.NET Core では、[EU の一般データ保護規制 (GDPR) ](https://www.eugdpr.org/)の要件の一部を満たすのに役立つ API とテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="7ea26-162">詳細については、[ASP.NET Core での GDPR のサポート](xref:security/gdpr)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="7ea26-163">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)では、ASP.NET Core 2.1 テンプレートに追加された GDPR 拡張ポイントと API の大部分について、使用方法を示し、テストできるようにしています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-163">A [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="7ea26-164">統合テスト</span><span class="sxs-lookup"><span data-stu-id="7ea26-164">Integration tests</span></span>

<span data-ttu-id="7ea26-165">テストの作成と実行を効率化する新しいパッケージが導入されました。</span><span class="sxs-lookup"><span data-stu-id="7ea26-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="7ea26-166">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) パッケージは、次のタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="7ea26-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="7ea26-167">テスト対象のアプリから依存関係ファイル ( *\*.deps*) をプロジェクトの *bin* フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="7ea26-167">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="7ea26-168">テストを実行したときに、静的なファイルとページ/ビューが検出されるように、コンテンツ ルートをテスト対象のアプリのプロジェクト ルートに設定します。</span><span class="sxs-lookup"><span data-stu-id="7ea26-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="7ea26-169">[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) クラスを提供して、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を使用してテスト対象のアプリのブートストラップを効率化します。</span><span class="sxs-lookup"><span data-stu-id="7ea26-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="7ea26-170">次のテストでは、[xUnit](https://xunit.github.io/) を使用して、インデックス ページが成功の状態コードと正しい Content-Type ヘッダーを読み込むことを確認します。</span><span class="sxs-lookup"><span data-stu-id="7ea26-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

<span data-ttu-id="7ea26-171">詳細については、[統合テスト](xref:test/integration-tests)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="7ea26-172">[ApiController]、ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="7ea26-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="7ea26-173">ASP.NET Core 2.1 では、クリーンでわかりやすい Web API のビルドを簡単にする新しいプログラミング規則が追加されました。</span><span class="sxs-lookup"><span data-stu-id="7ea26-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="7ea26-174">`ActionResult<T>` は、アプリが引き続き応答の種類を示しながら、応答の種類またはその他のアクション結果を (IActionResult と同様に) 返せるようにするために追加された新しい型です。</span><span class="sxs-lookup"><span data-stu-id="7ea26-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="7ea26-175">`[ApiController]` 属性は、Web API に固有の規則と動作にオプトインする方法としても追加されています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="7ea26-176">詳細については、「[ASP.NET Core で Web API を構築する](xref:web-api/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="7ea26-177">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="7ea26-177">IHttpClientFactory</span></span>

<span data-ttu-id="7ea26-178">ASP.NET Core 2.1 には、アプリでの `HttpClient` のインスタンスの構成と使用を容易にする新しい `IHttpClientFactory` サービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="7ea26-179">`HttpClient` は既に、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="7ea26-180">ファクトリは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="7ea26-180">The factory:</span></span>

* <span data-ttu-id="7ea26-181">名前付きクライアントごとの `HttpClient` のインスタンスの登録をより直観的にします。</span><span class="sxs-lookup"><span data-stu-id="7ea26-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="7ea26-182">Polly ポリシーを Retry、CircuitBreakers などに使用できるようにする Polly ハンドラーを実装します。</span><span class="sxs-lookup"><span data-stu-id="7ea26-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="7ea26-183">詳細については、「[HTTP 要求の開始](xref:fundamentals/http-requests)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="7ea26-184">Kestrel トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="7ea26-184">Kestrel transport configuration</span></span>

<span data-ttu-id="7ea26-185">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="7ea26-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="7ea26-186">詳細については、[Kestrel Web サーバーの実装:トランスポート構成](xref:fundamentals/servers/kestrel#transport-configuration)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="7ea26-187">汎用ホスト ビルダー</span><span class="sxs-lookup"><span data-stu-id="7ea26-187">Generic host builder</span></span>

<span data-ttu-id="7ea26-188">汎用ホスト ビルダー (`HostBuilder`) が導入されました。</span><span class="sxs-lookup"><span data-stu-id="7ea26-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="7ea26-189">このビルダーは、HTTP 要求 (メッセージング、バック グラウンド タスクなど) を処理しないアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="7ea26-190">詳細については、「[.NET での汎用ホスト](xref:fundamentals/host/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="7ea26-191">更新された SPA テンプレート</span><span class="sxs-lookup"><span data-stu-id="7ea26-191">Updated SPA templates</span></span>

<span data-ttu-id="7ea26-192">Angular、React、および Redux と組み合わせた React 用の単一ページ アプリケーション テンプレートが、標準のプロジェクト構造を使用して、各フレームワーク用のシステムをビルドするように更新されました。</span><span class="sxs-lookup"><span data-stu-id="7ea26-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="7ea26-193">Angular テンプレートは Angular CLI に基づいており、React テンプレートは create-react-app に基づいています。</span><span class="sxs-lookup"><span data-stu-id="7ea26-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="7ea26-194">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="7ea26-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="7ea26-195">Razor アセットの Razor Pages 検索</span><span class="sxs-lookup"><span data-stu-id="7ea26-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="7ea26-196">2\.1 では、Razor Pages は、次のディレクトリ内の Razor アセット (レイアウトや部分など) を次の順序で検索します。</span><span class="sxs-lookup"><span data-stu-id="7ea26-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="7ea26-197">現在の Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="7ea26-197">Current Pages folder.</span></span>
1. <span data-ttu-id="7ea26-198">*/Pages/Shared/*</span><span class="sxs-lookup"><span data-stu-id="7ea26-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="7ea26-199">*/Views/Shared/*</span><span class="sxs-lookup"><span data-stu-id="7ea26-199">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="7ea26-200">区分での Razor Pages</span><span class="sxs-lookup"><span data-stu-id="7ea26-200">Razor Pages in an area</span></span>

<span data-ttu-id="7ea26-201">Razor Pages が、[区分](xref:mvc/controllers/areas)をサポートするようになりました。</span><span class="sxs-lookup"><span data-stu-id="7ea26-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="7ea26-202">区分の例を表示するには、個々のユーザー アカウントで新しい Razor Pages Web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="7ea26-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="7ea26-203">個々のユーザー アカウントを使用した Razor Pages Web アプリには、 */Areas/Identity/Pages* が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="7ea26-204">MVC 互換バージョン</span><span class="sxs-lookup"><span data-stu-id="7ea26-204">MVC compatibility version</span></span>

<span data-ttu-id="7ea26-205"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> メソッドを使用すると、ASP.NET Core MVC 2.1 以降に導入されている、互換性に影響する重大な変更をオプトインまたはオプトアウトすることができます。</span><span class="sxs-lookup"><span data-stu-id="7ea26-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="7ea26-206">詳細については、<xref:mvc/compatibility-version> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="7ea26-207">2\.0 から 2.1 への移行</span><span class="sxs-lookup"><span data-stu-id="7ea26-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="7ea26-208">「[ASP.NET Core 2.0 から 2.1 への移行](xref:migration/20_21)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="7ea26-209">追加情報</span><span class="sxs-lookup"><span data-stu-id="7ea26-209">Additional information</span></span>

<span data-ttu-id="7ea26-210">変更の全一覧については、「[ASP.NET Core 2.1 Release Notes](https://github.com/aspnet/Home/releases/tag/2.1.0)」 (ASP.NET Core 2.1 のリリース ノート) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7ea26-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/aspnet/Home/releases/tag/2.1.0).</span></span>
