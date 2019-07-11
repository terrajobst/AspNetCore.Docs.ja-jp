---
title: ASP.NET Core で統合テスト
author: guardrex
description: 統合テストによってデータベース、ファイル システム、ネットワークなどのインフラストラクチャ レベルで、アプリのコンポーネントがどのように正しく機能するようになるかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/05/2019
uid: test/integration-tests
ms.openlocfilehash: a4e22e53b4658a7c6da3c9e15671a355b212f559
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815368"
---
# <a name="integration-tests-in-aspnet-core"></a>ASP.NET Core で統合テスト

によって[Luke Latham](https://github.com/guardrex)と[Steve Smith](https://ardalis.com/)

統合テストでは、データベース、ファイル システム、ネットワークなど、アプリのサポート インフラストラクチャを含むレベルでアプリのコンポーネントが正しく動作することを確認します。 ASP.NET Core では、テスト web ホストとメモリ内のテスト サーバーを単体テスト フレームワークを使用して、統合テストをサポートします。

このトピックでは、単体テストの基本的な知識を前提とします。 テストの概念にあまり馴染みがない場合、[.NET Core と .NET Standard の単体テスト](/dotnet/core/testing/) のトピックとそこでリンクされているコンテンツを参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプル アプリは、Razor ページ アプリで Razor ページの基本的な知識を前提としています。 Razor ページに不慣れな場合は、次のトピックを参照してください。

* [Razor ページを始める](xref:razor-pages/index)
* [Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start)
* [Razor ページの単体テスト](xref:test/razor-pages-tests)

> [!NOTE]
> Spa をテストするには、お勧めするツールなど[Selenium](https://www.seleniumhq.org/)ブラウザーを自動化することができます。

## <a name="introduction-to-integration-tests"></a>統合テストの概要

広範なレベルでのアプリのコンポーネントを評価する統合テスト[単体テスト](/dotnet/core/testing/)します。 単体テストは、個々 のクラスのメソッドなどの分離のソフトウェア コンポーネントのテストに使用されます。 統合テストでは、完全に要求を処理するために必要なすべてのコンポーネントを含めることも、予想結果を生成するために、2 つ以上のアプリ コンポーネントが共同作業を確認します。

これらのより広範なテストは、アプリのインフラストラクチャと多くの場合、次のコンポーネントを含む、フレームワーク全体をテストに使用されます。

* データベース
* ファイル システム
* ネットワーク アプライアンス
* 要求-応答のパイプライン

単体テストと呼ばれる使用加工コンポーネント*fakes*または*モック オブジェクト*、インフラストラクチャ コンポーネントの代わりにします。

単体テストとは対照的統合をテストします。

* 運用環境でアプリを使用して、実際のコンポーネントを使用します。
* 多くのコードとデータ処理が必要です。
* 実行にかかます。

そのため、最も重要なインフラストラクチャへの統合テストの使用を制限します。 場合は、動作をテストするには、単体テストまたは統合テストを使用して、単体テストを選択します。

> [!TIP]
> データベースやファイル システムでのデータとファイルのアクセスの順列で可能なすべての統合テストを記述しません。 アプリ間で配置を数に関係なくは、データベースとファイル システム、フォーカスのある一連の読み取り、書き込み、更新、および削除の統合テストは、十分にテスト データベースのことし、ファイル システムのコンポーネントと対話します。 これらのコンポーネントと対話する日常的なテスト メソッドのロジックの単位を使用をテストします。 単体テストでインフラストラクチャを使用して fakes/モック テストの実行を高速化の結果。

> [!NOTE]
> テスト対象のプロジェクトが頻繁に呼び出される統合テストの説明で、*テスト対象のシステム*、または略して"SUT"。

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core 統合テスト

ASP.NET Core で統合テストでは、次の項目が必要です。

* 含まれてし、テストを実行するテスト プロジェクトが使用されます。 テスト プロジェクトと呼ばれる、テスト対象の ASP.NET Core プロジェクトを参照している、*テスト対象のシステム*(SUT)。 _このトピック全体では、"SUT"を使用してテスト済みのアプリを参照してください。_
* テスト プロジェクトでは、SUT のテスト web ホストを作成し、テスト サーバーのクライアントを使用して、要求と SUT への応答を処理します。
* テスト ランナーを使用して、テスト結果、テストとレポートを実行できます。

統合テストを含む、一般的なイベントのシーケンスのに従って*配置*、 *Act*、および*Assert*テスト ステップ。

1. SUT の web ホストが構成されます。
1. テスト サーバーのクライアントを作成すると、アプリに要求を送信します。
1. *配置*テスト ステップが実行されます。テスト アプリでは、要求を準備します。
1. *Act*テスト ステップが実行されます。クライアントは、要求を送信し、応答を受け取ります。
1. *Assert*テスト ステップが実行されます。*実際*として応答が検証された、*渡す*または*失敗*に基づいて、*予想*応答。
1. すべてのテストが実行されるまで、処理が続きます。
1. テスト結果が報告されます。

通常、テスト web ホストではテスト用のアプリの通常の web ホストの実行よりも構成されますが異なります。 たとえば、別のデータベースまたは別のアプリ設定は、テストに使用可能性があります。

テスト web ホストのメモリ内のテスト サーバーなどのインフラストラクチャ コンポーネント ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) が提供されているかによって管理される、 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)パッケージ。 このパッケージの使用には、テストの作成と実行が効率化されます。

`Microsoft.AspNetCore.Mvc.Testing`パッケージは、次のタスクを処理します。

* 依存関係ファイルのコピー ( *\*.deps*) にテスト プロジェクトの SUT から*bin*ディレクトリ。
* 静的ファイルとページ/ビューは、テストの実行時に検出できるように、コンテンツのルートを SUT のプロジェクトのルートに設定します。
* 提供、 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)で SUT をブートス トラップを効率化するクラス`TestServer`します。

[単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)ドキュメントが名前のテストにテストと方法に関する推奨事項を実行して、クラスをテストする方法の詳細な手順と共に、テスト プロジェクトとテスト ランナーを設定する方法について説明します。

> [!NOTE]
> アプリのテスト プロジェクトを作成する場合は、別々 のプロジェクトに統合テストから単体テストを区切ります。 テストのインフラストラクチャ コンポーネントが、単体テストに含まれていない誤ってを維持します。 単体テストや統合テストの分離により、テストのどのセットに対してが実行を制御します。

これは、Razor ページ アプリのテストの構成と MVC アプリの違いはほとんどありません。 唯一の違いは、テストの名前付け方法です。 Razor ページ アプリでページのエンドポイントのテストの名前は通常、ページ モデル クラスの後 (たとえば、`IndexPageTests`インデックス ページのコンポーネントの統合をテストする)。 MVC アプリでテストは通常は別に整理されたコント ローラー クラスとテスト コント ローラーにちなんだ名前 (たとえば、 `HomeControllerTests` Home コント ローラー用のコンポーネントの統合をテストする)。

## <a name="test-app-prerequisites"></a>アプリの前提条件をテストします。

テスト プロジェクトが必要です。

* 次のパッケージを参照します。
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Web SDK をプロジェクト ファイルで指定 (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。 Web SDK は、参照するときに必要な[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)します。

これらの前提条件がわかるように、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)します。 検査、 *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*ファイル。 サンプル アプリでは、 [xUnit](https://xunit.github.io/)テスト フレームワークと[AngleSharp](https://anglesharp.github.io/)サンプル アプリを参照しているため、パーサー ライブラリ。

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT 環境

場合 SUT の[環境](xref:fundamentals/environments)開発環境は既定で、設定されていません。

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>既定値 WebApplicationFactory の基本的なテスト

[WebApplicationFactory&lt;TEntryPoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)を作成するために使用する[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)統合テスト用です。 `TEntryPoint` SUT のエントリ ポイント クラスは、通常、`Startup`クラス。

テスト クラスの実装を*クラス フィクスチャ*インターフェイス ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) を示す、クラスは、テストが含まれ、クラスのテストの間で共有されたオブジェクトのインスタンスを提供します。

### <a name="basic-test-of-app-endpoints"></a>アプリのエンドポイントの基本的なテスト

次のテスト クラス、`BasicTests`を使用して、 `WebApplicationFactory` SUT を起動して、提供する、 [HttpClient](/dotnet/api/system.net.http.httpclient)テスト メソッドに`Get_EndpointsReturnSuccessAndCorrectContentType`します。 応答ステータス コードが成功したかどうか、メソッドを確認します (状態コードが 200-299 の範囲) と`Content-Type`ヘッダーが`text/html; charset=utf-8`のいくつかのアプリ ページ。

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)のインスタンスを作成します`HttpClient`を自動的にリダイレクトに依存して cookie を処理します。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

既定では、不要な cookie は維持されません要求間で、 [GDPR の承認ポリシー](xref:security/gdpr)を有効にします。 TempData プロバイダーによって使用されるものなどの不要な cookie を保持するために、テストで重要としてマークします。 Cookie 重要としてマークする方法の詳細については、次を参照してください。 [Essential cookie](xref:security/gdpr#essential-cookies)します。

### <a name="test-a-secure-endpoint"></a>セキュリティで保護されたエンドポイントをテストします。

別のテストで、`BasicTests`クラスは、セキュリティで保護されたエンドポイントがアプリのログイン ページに未認証のユーザーをリダイレクトすることを確認します。

SUT で、`/SecurePage`ページ使用して、 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)を適用する規則、 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)ページにします。 詳細については、次を参照してください。 [Razor ページの承認規則](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)します。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

`Get_SecurePageRequiresAnAuthenticatedUser` 、テスト、 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)を設定してリダイレクトを許可しないように設定されている[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)に`false`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

リダイレクトを実行するクライアントを禁止することにより、次のチェックを変更できます。

* SUT によって返されるステータス コードをチェックして、予想されるに対して[HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)結果、可能性のあるログイン ページへのリダイレクトの後に最終的な状態コードではなく[HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* `Location`で始まることを確認する応答ヘッダーのヘッダーの値がオンになって`http://localhost/Identity/Account/Login`、いない最終的なログイン ページの応答、場所、`Location`ヘッダーが存在するでしょう。

詳細については`WebApplicationFactoryClientOptions`を参照してください、[クライアント オプション](#client-options)セクション。

## <a name="customize-webapplicationfactory"></a>WebApplicationFactory をカスタマイズします。

継承することによってテスト クラスとは無関係に web ホストの構成を作成できます`WebApplicationFactory`1 つまたは複数のカスタム ファクトリを作成します。

1. 継承`WebApplicationFactory`オーバーライドと[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)します。 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)サービス コレクションの構成を可能に[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   データベースでシード処理で、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)によって実行されます、`InitializeDbForTests`メソッド。 メソッドが記載されて、[統合サンプルをテストします。組織のアプリをテスト](#test-app-organization)セクション。

2. ユーザー設定を使用して、`CustomWebApplicationFactory`テスト クラスにします。 次のコードの例では、工場で、`IndexPageTests`クラス。

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   防ぐために、サンプル アプリのクライアントが構成されている、`HttpClient`次のリダイレクトから。 説明したように、[セキュリティで保護されたエンドポイントをテスト](#test-a-secure-endpoint) セクションで、これにより、アプリの初回の応答の結果を確認するテストです。 最初の応答は、リダイレクトでこれらのテストの多くで、`Location`ヘッダー。

3. 一般的なテストを使用して、`HttpClient`と、要求と応答を処理するヘルパー メソッド。

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

SUT への POST 要求は、アプリのによって自動的に行われた偽造防止チェックを満たす必要があります[データ保護の偽造防止システム](xref:security/data-protection/introduction)します。 テストの POST 要求を配置するために、テスト アプリが必要です。

1. ページの要求を行います。
1. 偽造防止 cookie と、応答からの要求検証トークンを解析します。
1. インプレース偽造防止 cookie と要求の検証を含む POST 要求トークンを作成します。

`SendAsync`ヘルパー拡張メソッド (*Helpers/HttpClientExtensions.cs*) および`GetDocumentAsync`ヘルパー メソッド (*Helpers/HtmlHelpers.cs*) で、 [のサンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)を使用して、 [AngleSharp](https://anglesharp.github.io/)偽造防止チェックを次の方法で処理するためにパーサー。

* `GetDocumentAsync` &ndash; 受信、 [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)を返します、`IHtmlDocument`します。 `GetDocumentAsync` 準備するファクトリを使用して、*仮想応答*元に基づいて`HttpResponseMessage`します。 詳細については、次を参照してください。、 [AngleSharp ドキュメント](https://github.com/AngleSharp/AngleSharp#documentation)します。
* `SendAsync` 拡張メソッド、 `HttpClient` compose、 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)を呼び出すと[SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) SUT に要求を送信します。 オーバー ロード`SendAsync`HTML フォームを受け入れる (`IHtmlFormElement`) および次。
  * フォームのボタンの送信 (`IHtmlElement`)
  * フォーム値のコレクション (`IEnumerable<KeyValuePair<string, string>>`)
  * 送信ボタン (`IHtmlElement`) の値を形成し、(`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/)サード パーティがこのトピックおよびサンプル アプリケーションのデモンストレーションを目的として使用されるライブラリを解析します。 AngleSharp はサポートされているがないか、ASP.NET Core アプリの統合をテストするために必要です。 その他のパーサーができますなど、 [Html 機敏性パック (HAP)](https://html-agility-pack.net/)します。 別の方法では、偽造防止システムの要求検証トークンと偽造防止 cookie を直接処理するコードを作成します。

## <a name="customize-the-client-with-withwebhostbuilder"></a>WithWebHostBuilder でクライアントをカスタマイズします。

追加の構成がテスト メソッド内で必要な場合[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)新たに作成します`WebApplicationFactory`で、 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)するが、構成でさらにカスタマイズします。

`Post_DeleteMessageHandler_ReturnsRedirectToRoot`テストのメソッド、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)の使用方法を示します`WithWebHostBuilder`します。 このテストは、SUT でフォームの送信をトリガーすることによって、データベース内のレコードの削除を実行します。

別のテストのため、`IndexPageTests`クラスは、すべてのデータベース内のレコードを削除し、前に実行される可能性が操作を実行、`Post_DeleteMessageHandler_ReturnsRedirectToRoot`メソッド、データベースのレコードが削除 SUT に存在することを確認するには、このテスト メソッドでシード処理します。 選択すると、`deleteBtn1`のボタン、 `messages` SUT でフォームが SUT への要求でシミュレートされました。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>クライアント オプション

次の表は、既定[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)作成するときに使用可能な`HttpClient`インスタンス。

| オプション | 説明 | 既定値 |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | 取得または設定するかどうか`HttpClient`インスタンスは、リダイレクト応答に自動的に従う必要があります。 | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | 取得または設定のベース アドレス`HttpClient`インスタンス。 | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 取得または設定するかどうか`HttpClient`インスタンスは、cookie を処理する必要があります。 | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | リダイレクト応答の最大数の設定を取得または`HttpClient`インスタンスが従う必要があります。 | 7 |

作成、`WebApplicationFactoryClientOptions`クラスに渡すと、 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)メソッド (既定値はコード例に示した)。

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>モック サービスを挿入します。

サービスへの呼び出しでのテストでオーバーライドできます[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)ホスト ビルダーにします。 **モック サービスを挿入する SUT があります、`Startup`クラス、`Startup.ConfigureServices`メソッド。**

SUT サンプルには、見積もりが返すスコープ化されたサービスが含まれています。 見積もりは、インデックス ページが要求されたときに、インデックス ページ上の非表示フィールドに埋め込まれます。

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

SUT アプリの実行時に、次のマークアップが生成されます。

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

統合テストでは、サービスおよび見積もりの挿入をテストするには、モック サービスは、テストによって SUT に組み込まれます。 モック サービスが、アプリの置き換えられます`QuoteService`テスト アプリによって提供されるサービスという`TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` 呼び出されると、スコープ化されたサービスが登録されているとします。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

テストの実行中に生成されたマークアップの反映によって提供される見積もりテキスト`TestQuoteService`、そのため、アサーションのパス。

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>テスト インフラストラクチャが、アプリのコンテンツ ルート パスを推論する方法

`WebApplicationFactory`コンス トラクターを検索して、アプリのコンテンツ ルート パスの推測を[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)と等しいキーを使用して、統合テストを含むアセンブリを`TEntryPoint`アセンブリ`System.Reflection.Assembly.FullName`. 正しいキーを持つ属性が見つからない場合に`WebApplicationFactory`ソリューション ファイルの検索にフォールバック ( *\*.sln*) し、追加、`TEntryPoint`ソリューション ディレクトリにアセンブリ名。 アプリのルート ディレクトリ (コンテンツ ルート パス) を使用して、ビューとコンテンツ ファイルを検出します。

ほとんどの場合、必要はありません、アプリのコンテンツ ルートを明示的に設定するとしての検索ロジックが実行時に、通常の適切なコンテンツのルートを検索します。 コンテンツのルートが見つからないの特殊なシナリオでは、アプリのコンテンツ ルートは、明示的にまたはカスタム ロジックを使用して指定できます、組み込みの検索アルゴリズムを使用します。 これらのシナリオでは、アプリのコンテンツ ルートを設定するには、呼び出し、`UseSolutionRelativeContentRoot`から拡張メソッド、 [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost)パッケージ。 ソリューションの相対パスと省略可能なソリューション ファイルの名前または glob パターンを指定 (既定 = `*.sln`)。

呼び出す、 [UseSolutionRelativeContentRoot](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.usesolutionrelativecontentroot)メソッドを使用して拡張*1 つ*の次の方法。

* テスト クラスを構成するときに`WebApplicationFactory`、カスタム構成での提供、 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder):

   ```csharp
   public IndexPageTests(
       WebApplicationFactory<RazorPagesProject.Startup> factory)
   {
       var _factory = factory.WithWebHostBuilder(builder =>
       {
           builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

           ...
       });
   }
   ```

* カスタムのテスト クラスを構成するときに`WebApplicationFactory`、継承`WebApplicationFactory`オーバーライドと[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost):

   ```csharp
   public class CustomWebApplicationFactory<TStartup>
       : WebApplicationFactory<RazorPagesProject.Startup>
   {
       protected override void ConfigureWebHost(IWebHostBuilder builder)
       {
           builder.ConfigureServices(services =>
           {
               builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

               ...
           });
       }
   }
   ```

## <a name="disable-shadow-copying"></a>シャドウ コピーを無効にします。

シャドウ コピーと、出力ディレクトリとは異なるディレクトリで実行するテストが発生します。 正常に動作するテストでは、シャドウ コピーする必要があります無効になります。 [サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)xUnit を使用し、シャドウ コピーを含めることで xunit を無効にする*xunit.runner.json*適切な構成設定ファイル。 詳細については、次を参照してください。 [JSON で xUnit を構成する](https://xunit.github.io/docs/configuring-with-json.html)します。

追加、 *xunit.runner.json*以下の内容のテスト プロジェクトのルートにファイル。

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>オブジェクトの破棄

テストの後、`IClassFixture`実装は、実行される[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)と[HttpClient](/dotnet/api/system.net.http.httpclient) xUnit 破棄時に破棄は、 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). 開発者によってインスタンス化されたオブジェクトは、破棄を必要とする場合での dispose、`IClassFixture`実装します。 詳細については、次を参照してください。 [Dispose メソッドの実装](/dotnet/standard/garbage-collection/implementing-dispose)します。

## <a name="integration-tests-sample"></a>統合テストのサンプル

[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)は 2 つのアプリで構成されます。

| アプリ | プロジェクト ディレクトリ | 説明 |
| --- | ----------------- | ----------- |
| メッセージ アプリ (SUT) | *src/RazorPagesProject* | により、ユーザーの追加、削除のいずれか、すべてを削除、およびメッセージを分析できます。 |
| テスト アプリ | *tests/RazorPagesProject.Tests* | 統合テスト SUT するために使用します。 |

などの IDE、組み込みのテスト機能を使用して、テストを実行できる[Visual Studio](https://visualstudio.microsoft.com)します。 使用して場合[Visual Studio Code](https://code.visualstudio.com/)またはコマンド プロンプトで次のコマンドを実行するコマンドライン、 *tests/RazorPagesProject.Tests*ディレクトリ。

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>メッセージ アプリ (SUT) 組織

SUT は、次の特性を持つ、Razor ページ メッセージ システムを示します。

* アプリのインデックス ページ (*Pages/Index.cshtml*と*Pages/Index.cshtml.cs*) UI とページ モデルのメソッドを追加、削除、およびメッセージ (メッセージあたりの平均の単語) の分析の制御を提供します.
* メッセージは、`Message`クラス (*Data/Message.cs*) 2 つのプロパティを持つ: `Id` (キー) と`Text`(メッセージ)。 `Text`プロパティは必須であり 200 文字に制限されます。
* 使用してメッセージを保存する[Entity Framework のメモリ内データベース](/ef/core/providers/in-memory/)&#8224;します。
* アプリのデータベース コンテキスト クラス内でデータ アクセス層 (DAL) に含まれる`AppDbContext`(*Data/AppDbContext.cs*)。
* データベースがアプリの起動時に空の場合、メッセージ ストアは、3 つのメッセージで初期化されます。
* アプリを`/SecurePage`を認証されたユーザーのみアクセスできます。

&#8224;EF トピック[InMemory のテスト](/ef/core/miscellaneous/testing/in-memory)MSTest を使用したテストにメモリ内データベースを使用する方法について説明します。 このトピックでは、 [xUnit](https://xunit.github.io/)テスト フレームワーク。 テストの概念と別のテスト フレームワーク間でのテストの実装は似ていますが、同一ではないです。

アプリは、リポジトリ パターンを使用しないしの有効な例はありませんが、 [Unit of Work (UoW) パターン](https://martinfowler.com/eaaCatalog/unitOfWork.html)、Razor ページは、これらのパターンの開発をサポートしています。 詳細については、次を参照してください。[インフラストラクチャの永続レイヤーの設計](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)と[テスト コント ローラー ロジック](/aspnet/core/mvc/controllers/testing)(このサンプルは、リポジトリ パターンを実装)。

### <a name="test-app-organization"></a>組織のアプリをテストします。

テスト アプリが内部でコンソール アプリ、 *tests/RazorPagesProject.Tests*ディレクトリ。

| テスト アプリのディレクトリ | 説明 |
| ------------------ | ----------- |
| *BasicTests* | *BasicTests.cs*ルーティング、未認証のユーザーによってセキュリティで保護されたページにアクセスして、GitHub のユーザー プロファイルの取得およびプロファイルのユーザーのログインを確認用のテスト メソッドが含まれています。 |
| *IntegrationTests* | *IndexPageTests.cs*カスタムを使用して、インデックス ページに対する統合テストを含む`WebApplicationFactory`クラス。 |
| *ヘルパー/ユーティリティ* | <ul><li>*Utilities.cs*が含まれています、`InitializeDbForTests`メソッドがテスト データでデータベースをシードするために使用します。</li><li>*HtmlHelpers.cs* 、AngleSharp を返すメソッドを提供します。`IHtmlDocument`テスト メソッドで使用します。</li><li>*HttpClientExtensions.cs*のオーバー ロードを用意`SendAsync`SUT に要求を送信します。</li></ul> |

テスト フレームワークは[xUnit](https://xunit.github.io/)します。 使用して統合テストが行われ、 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)が含まれています、 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)します。 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)テスト ホストとテスト サーバーを構成するパッケージが使用される、`TestHost`と`TestServer`パッケージは、テスト アプリのプロジェクト ファイルの直接のパッケージ参照を必要としないまたはテスト アプリ開発者用の構成。

**テスト用データベースをシード処理**

統合テストは、通常、テストの実行前に、データベース内の小規模なデータセットを要求します。 など、データベースは削除要求を成功させるには少なくとも 1 つのレコードがある必要であるために、削除はデータベース レコードの削除の呼び出しをテストします。

サンプル アプリで次の 3 つのメッセージを使用したデータベースをシード処理*Utilities.cs*テストで実行するときに使用できること。

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a>その他の技術情報

* [単体テスト](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Razor ページの単体テスト](xref:test/razor-pages-tests)
* [ミドルウェア](xref:fundamentals/middleware/index)
* [テスト コントローラー](xref:mvc/controllers/testing)
