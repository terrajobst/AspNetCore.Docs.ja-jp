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
# <a name="whats-new-in-aspnet-core-21"></a>ASP.NET Core 2.1 の新機能

この記事では、ASP.NET Core 2.1 の最も大きな変更点について説明します。また、その変更点のドキュメントへのリンクも示します。

## SignalR

ASP.NET Core 2.1 用に SignalR を書き直しました。 ASP.NET Core SignalR には、多くの改善点が含まれています。

* 簡略化されたスケール アウト モデル。
* jQuery への依存関係がない新しい JavaScript クライアント。
* MessagePack に基づく新しいコンパクトなバイナリ プロトコル。
* カスタム プロトコルのサポート。
* 新しいストリーミング応答モデル。
* ベア WebSocket に基づいたクライアントのサポート。

詳細については、[ASP.NET Core SignalR](xref:signalr/introduction) に関する記事を参照してください。

## <a name="razor-class-libraries"></a>Razor クラス ライブラリ

ASP.NET Core 2.1 を使用すると、Razor ベースの UI をビルドしてライブラリに含め、複数のプロジェクト間で共有することが容易になります。 新しい Razor SDK を使用すると、NuGet パッケージにパッケージ化できるクラス ライブラリ プロジェクトに Razor ファイルをビルドすることができます。 アプリにより、ライブラリ内のビューとページを自動的に検出して、オーバーライドすることができます。 ビルドに Razor コンパイルを統合すると、次のことが可能になります。

* アプリの起動時間が大幅に短縮されます。
* 実行時の Razor ビューとページへの高速更新は、反復開発ワークフローの一部として引き続き利用できます。

詳細については、[Razor クラス ライブラリ プロジェクトを使用した再利用可能 UI の作成](xref:razor-pages/ui-class)に関するページをご覧ください。

## <a name="identity-ui-library--scaffolding"></a>Identity の UI ライブラリとスキャフォールディング

ASP.NET Core 2.1 では、[ASP.NET Core Identity](xref:security/authentication/identity) を [Razor クラス ライブラリ](xref:razor-pages/ui-class)として提供しています。 Identity を含むアプリでは、新しい Identity スキャフォルダーを適用して、Identity Razor クラス ライブラリ (RCL) に含まれるソース コードを選択的に追加することができます。 コードを変更して動作を変更できるように、ソース コードを生成できます。 たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。 生成されたコードは、Identity RCL の同じコードよりも優先されます。

認証が**含まれていない**アプリでは、Identity スキャフォルダーを適用して RCL Identity パッケージを追加できます。 生成される Identity コードの選択オプションがあります。

詳細については、「[Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity)」 (ASP.NET Core プロジェクトの ID のスキャフォールディング) を参照してください。

## <a name="https"></a>HTTPS

セキュリティとプライバシーがより強化されたため、Web アプリの HTTPS を有効化することが重要です。 Web 上では、HTTPS の強制がますます厳しくなっています。 HTTPS を使用していないサイトは、安全でないと見なされます。 ブラウザー (Chromium、Mozilla) では、セキュリティで保護されたコンテキストから Web 機能を使用することを強制するようになってきています。 [GDPR](xref:security/gdpr) では、ユーザー プライバシーの保護に HTTPS を使用する必要があります。 運用環境では HTTPS の使用が非常に重要ですが、開発環境で HTTPS を使用すると、展開における問題 (セキュリティ保護されていないリンクなど) を防止するのに役立ちます。 ASP.NET Core 2.1 には、開発環境での HTTPS の使用と、運用環境での HTTPS の構成を容易にする多くの機能強化が含まれています。 詳細については、[HTTPS の適用](xref:security/enforcing-ssl)に関するページをご覧ください。

### <a name="on-by-default"></a>既定でオン

セキュリティで保護された Web サイトの開発を円滑にするため、HTTPS は既定でオンになっています。 2\.1 以降では、Kestrel は `https://localhost:5001` でリッスンします (ローカル開発証明書が存在する場合)。 開発証明書が作成されます。

* SDK を初めて使用するときに、.NET Core SDK の最初の実行エクスペリエンスの一部として。
* 新しい `dev-certs` ツールを使用して手動で。

`dotnet dev-certs https --trust` を実行して証明書を信頼します。

### <a name="https-redirection-and-enforcement"></a>HTTPS のリダイレクトと強制

通常、Web アプリは、HTTP と HTTPS の両方でリッスンする必要がありますが、その一方で、すべての HTTP トラフィックを HTTPS にリダイレクトする必要があります。 2\.1 では、構成またはバインドされたサーバー ポートの有無に基づいて、インテリジェントにリダイレクトする特殊な HTTPS リダイレクト ミドルウェアが導入されました。

[HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) を使用すると、HTTPS の使用をさらに強制することができます。 HSTS は、常に HTTPS 経由でサイトにアクセスするようにブラウザーに指示します。 ASP.NET Core 2.1 では、最長有効期間、サブドメイン、HSTS プリロード リストのオプションをサポートする HSTS ミドルウェアが追加されます。

### <a name="configuration-for-production"></a>運用環境の構成

運用環境では、HTTPS を明示的に構成する必要があります。 2\.1 では、Kestrel に HTTPS を構成するための既定の構成スキーマが追加されています。 以下を使用するようにアプリを構成できます。

* URL を含む複数のエンドポイント。 詳細については、[Kestrel Web サーバーの実装:エンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)に関するセクションを参照してください。
* ディスク上のファイルまたは証明書ストアから HTTPS に使用する証明書。

## <a name="gdpr"></a>GDPR

ASP.NET Core では、[EU の一般データ保護規制 (GDPR) ](https://www.eugdpr.org/)の要件の一部を満たすのに役立つ API とテンプレートが用意されています。 詳細については、[ASP.NET Core での GDPR のサポート](xref:security/gdpr)に関するページを参照してください。 [サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)では、ASP.NET Core 2.1 テンプレートに追加された GDPR 拡張ポイントと API の大部分について、使用方法を示し、テストできるようにしています。

## <a name="integration-tests"></a>統合テスト

テストの作成と実行を効率化する新しいパッケージが導入されました。 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) パッケージは、次のタスクを処理します。

* テスト対象のアプリから依存関係ファイル ( *\*.deps*) をプロジェクトの *bin* フォルダーにコピーします。
* テストを実行したときに、静的なファイルとページ/ビューが検出されるように、コンテンツ ルートをテスト対象のアプリのプロジェクト ルートに設定します。
* [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) クラスを提供して、[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) を使用してテスト対象のアプリのブートストラップを効率化します。

次のテストでは、[xUnit](https://xunit.github.io/) を使用して、インデックス ページが成功の状態コードと正しい Content-Type ヘッダーを読み込むことを確認します。

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

詳細については、[統合テスト](xref:test/integration-tests)のトピックを参照してください。

## <a name="apicontroller-actionresultt"></a>[ApiController]、ActionResult\<T>

ASP.NET Core 2.1 では、クリーンでわかりやすい Web API のビルドを簡単にする新しいプログラミング規則が追加されました。 `ActionResult<T>` は、アプリが引き続き応答の種類を示しながら、応答の種類またはその他のアクション結果を (IActionResult と同様に) 返せるようにするために追加された新しい型です。 `[ApiController]` 属性は、Web API に固有の規則と動作にオプトインする方法としても追加されています。

詳細については、「[ASP.NET Core で Web API を構築する](xref:web-api/index)」を参照してください。

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2.1 には、アプリでの `HttpClient` のインスタンスの構成と使用を容易にする新しい `IHttpClientFactory` サービスが含まれています。 `HttpClient` は既に、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。 ファクトリは次のことを行います。

* 名前付きクライアントごとの `HttpClient` のインスタンスの登録をより直観的にします。
* Polly ポリシーを Retry、CircuitBreakers などに使用できるようにする Polly ハンドラーを実装します。

詳細については、「[HTTP 要求の開始](xref:fundamentals/http-requests)」を参照してください。

## <a name="kestrel-transport-configuration"></a>Kestrel トランスポート構成

ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。 詳細については、[Kestrel Web サーバーの実装:トランスポート構成](xref:fundamentals/servers/kestrel#transport-configuration)に関する記事をご覧ください。

## <a name="generic-host-builder"></a>汎用ホスト ビルダー

汎用ホスト ビルダー (`HostBuilder`) が導入されました。 このビルダーは、HTTP 要求 (メッセージング、バック グラウンド タスクなど) を処理しないアプリで使用できます。

詳細については、「[.NET での汎用ホスト](xref:fundamentals/host/generic-host)」を参照してください。

## <a name="updated-spa-templates"></a>更新された SPA テンプレート

Angular、React、および Redux と組み合わせた React 用の単一ページ アプリケーション テンプレートが、標準のプロジェクト構造を使用して、各フレームワーク用のシステムをビルドするように更新されました。

Angular テンプレートは Angular CLI に基づいており、React テンプレートは create-react-app に基づいています。

詳細については次を参照してください:

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>Razor アセットの Razor Pages 検索

2\.1 では、Razor Pages は、次のディレクトリ内の Razor アセット (レイアウトや部分など) を次の順序で検索します。

1. 現在の Pages フォルダー
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="razor-pages-in-an-area"></a>区分での Razor Pages

Razor Pages が、[区分](xref:mvc/controllers/areas)をサポートするようになりました。 区分の例を表示するには、個々のユーザー アカウントで新しい Razor Pages Web アプリを作成します。 個々のユーザー アカウントを使用した Razor Pages Web アプリには、 */Areas/Identity/Pages* が含まれます。

## <a name="mvc-compatibility-version"></a>MVC 互換バージョン

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> メソッドを使用すると、ASP.NET Core MVC 2.1 以降に導入されている、互換性に影響する重大な変更をオプトインまたはオプトアウトすることができます。

詳細については、<xref:mvc/compatibility-version> を参照してください。

## <a name="migrate-from-20-to-21"></a>2\.0 から 2.1 への移行

「[ASP.NET Core 2.0 から 2.1 への移行](xref:migration/20_21)」を参照してください。

## <a name="additional-information"></a>追加情報

変更の全一覧については、「[ASP.NET Core 2.1 Release Notes](https://github.com/aspnet/Home/releases/tag/2.1.0)」 (ASP.NET Core 2.1 のリリース ノート) を参照してください。
