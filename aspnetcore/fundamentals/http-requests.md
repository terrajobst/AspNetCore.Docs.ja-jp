---
title: ASP.NET Core で IHttpClientFactory を使用して HTTP 要求を行う
author: stevejgordon
description: IHttpClientFactory インターフェイスを使用して、ASP.NET Core の論理 HttpClient インスタンスを管理する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 08/01/2019
uid: fundamentals/http-requests
ms.openlocfilehash: bcf2a2eaf6910222d274c38bac343c92fab9cb5b
ms.sourcegitcommit: b5e63714afc26e94be49a92619586df5189ed93a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739532"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>ASP.NET Core で IHttpClientFactory を使用して HTTP 要求を行う

寄稿者: [Glenn Condron](https://github.com/glennc)、[Ryan Nowak](https://github.com/rynowak)、[Steve Gordon](https://github.com/stevejgordon)

アプリ内で <xref:System.Net.Http.HttpClient> インスタンスを構成して作成するために、<xref:System.Net.Http.IHttpClientFactory> を登録して使用できます。 次のような利点があります。

* 論理 `HttpClient` インスタンスの名前付けと構成を一元化します。 たとえば、*github* クライアントを登録して、[GitHub](https://github.com/) にアクセスするように構成できます。 既定のクライアントは、他の目的に登録できます。
* `HttpClient` でのハンドラーのデリゲートにより送信ミドルウェアの概念を体系化し、Polly ベースのミドルウェアでそれを利用するための拡張機能を提供します。
* 基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。
* ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

::: moniker range="<= aspnetcore-2.2"

## <a name="prerequisites"></a>必須コンポーネント

.NET Framework をターゲットとするプロジェクトでは、[Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet パッケージのインストールが必要です。 .NET Core をターゲットとし、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するプロジェクトには、既に `Microsoft.Extensions.Http` パッケージが含まれています。

::: moniker-end

## <a name="consumption-patterns"></a>利用パターン

アプリで `IHttpClientFactory` を使用するには複数の方法があります。

* [基本的な使用方法](#basic-usage)
* [名前付きクライアント](#named-clients)
* [型指定されたクライアント](#typed-clients)
* [生成されたクライアント](#generated-clients)

これらの間に厳密な優劣はありません。 どの方法が最善かは、アプリの制約に依存します。

### <a name="basic-usage"></a>基本的な使用方法

`IHttpClientFactory` は、`Startup.ConfigureServices` メソッドの内部で `IServiceCollection` の `AddHttpClient` 拡張メソッドを呼び出すことによって登録できます。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

登録が済むと、コードは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用してサービスを挿入できる任意の場所で、`IHttpClientFactory` を受け取ることができます。 `IHttpClientFactory` を使用して、`HttpClient` インスタンスを作成できます。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

このように `IHttpClientFactory` を使用するのは、既存のアプリをリファクタリングする優れた方法です。 `HttpClient` の使用方法に影響はありません。 現在 `HttpClient` インスタンスが作成されている場所で、それを <xref:System.Net.Http.IHttpClientFactory.CreateClient*> の呼び出しに置き換えます。

### <a name="named-clients"></a>名前付きクライアント

それぞれ構成が異なる多数の `HttpClient` をアプリで個別に使用する必要がある場合は、**名前付きクライアント**を使用することができます。 名前付き `HttpClient` の構成は、`Startup.ConfigureServices` での登録時に指定できます。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

上記のコードでは、*github* という名前を指定して `AddHttpClient` を呼び出しています。 このクライアントには既定の構成がいくつか適用されています。つまり、GitHub API を使用するために必要なベース アドレスと 2 つのヘッダーです。

`CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが作成されて、構成アクションが呼び出されます。

名前付きクライアントを使用するには、文字列パラメーターを `CreateClient` に渡すことができます。 作成するクライアントの名前を指定します。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

上記のコードでは、要求でホスト名を指定する必要はありません。 クライアントに構成されているベース アドレスが使用されるため、パスだけを渡すことができます。

### <a name="typed-clients"></a>型指定されたクライアント

型指定されたクライアント:

* キーとして文字列を使用する必要なしに、名前付きクライアントと同じ機能を提供します。
* クライアントを使用するときに、IntelliSense とコンパイラのヘルプが提供されます。
* 特定の `HttpClient` を構成してそれと対話する 1 つの場所を提供します。 たとえば、単一の型指定されたクライアントを単一のバックエンド エンドポイントに対して使用し、そのエンドポイントを操作するすべてのロジックをカプセル化できます。
* DI に対応しており、アプリ内の必要な場所に挿入できます。

型指定されたクライアントは、コンストラクターで `HttpClient` パラメーターを受け取ります。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

上記のコードでは、構成が型指定されたクライアントに移動されています。 `HttpClient` オブジェクトは、パブリック プロパティとして公開されます。 `HttpClient` 機能を公開する API 固有のメソッドを定義することができます。 `GetAspNetDocsIssues` メソッドは、GitHub リポジトリで最新の未解決の問題をクエリして解析するために必要なコードをカプセル化します。

型指定されたクライアントを登録するには、ジェネリック <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 拡張メソッドを `Startup.ConfigureServices` 内で使用して、型指定されたクライアントのクラスを指定します。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

型指定されたクライアントは、DI で一時的として登録されます。 型指定されたクライアントは、直接挿入して使用できます。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

好みに応じて、型指定されたクライアントのコンストラクターではなく、`Startup.ConfigureServices` での登録時に型指定されたクライアントの構成を指定できます。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

型指定されたクライアントの内部に `HttpClient` を完全にカプセル化することができます。 プロパティとして公開するのではなく、`HttpClient` インスタンスを内部的に呼び出すパブリック メソッドとして提供することができます。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

上記のコードでは、`HttpClient` はプライベート フィールドとして格納されます。 外部呼び出しを行うすべてのアクセスは、`GetRepos` メソッドを通して行われます。

### <a name="generated-clients"></a>生成されたクライアント

`IHttpClientFactory` は、[Refit](https://github.com/paulcbetts/refit) などの他のサードパーティ製ライブラリと組み合わせて使用できます。 Refit は、.NET 用の REST ライブラリです。 REST API をライブ インターフェイスに変換します。 インターフェイスの実装は `RestService` によって動的に生成され、`HttpClient` を使用して外部 HTTP の呼び出しを行います。

インターフェイスと応答は、外部の API とその応答を表すように定義されます。

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

型指定されたクライアントを追加し、Refit を使用して実装を生成できます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

定義されたインターフェイスは、DI および Refit によって提供される実装で必要に応じて使用できます。

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>送信要求ミドルウェア

`HttpClient` は既に、送信 HTTP 要求用にリンクできるハンドラーのデリゲートの概念を備えています。 `IHttpClientFactory` を使用すると、各名前付きクライアントに適用するハンドラーを簡単に定義できます。 複数のハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築できます。 各ハンドラーは、送信要求の前と後に処理を実行できます。 このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。 このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。

ハンドラーを作成するには、<xref:System.Net.Http.DelegatingHandler> の派生クラスを定義します。 パイプライン内の次のハンドラーに要求を渡す前にコードを実行するように、`SendAsync` メソッドをオーバーライドします。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

上記のコードでは、基本的なハンドラーが定義されています。 このコードは、`X-API-KEY` ヘッダーが要求に含まれていたかどうかを確認します。 ヘッダーがない場合、HTTP 呼び出しを行わずに適切な応答を返すことができます。

登録の間に、1 つ以上のハンドラーを `HttpClient` の構成に追加することができます。 このタスクは、<xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> の拡張メソッドを使用して実行されます。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

::: moniker range=">= aspnetcore-2.2"

上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。 `IHttpClientFactory` では、ハンドラーごとに個別の DI スコープが作成されます。 ハンドラーは、任意のスコープのサービスに自由に依存することができます。 ハンドラーが依存するサービスは、ハンドラーが破棄されるときに破棄されます。

登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

上記のコードでは、`ValidateHeaderHandler` が DI で登録されます。 ハンドラーは、スコープではなく、一時的なサービスとして DI で登録する**必要があります**。 ハンドラーがスコープ付きサービスとして登録されており、ハンドラーが依存するサービスを破棄できる場合:

* ハンドラーのサービスは、ハンドラーがスコープ外に出る前に破棄されることがあります。
* 破棄されたハンドラー サービスが原因でハンドラーが失敗します。

登録が済むと、<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> を呼び出してハンドラーの型を渡すことができます。

::: moniker-end

実行する順序で、複数のハンドラーを登録することができます。 最後の `HttpClientHandler` が要求を実行するまで、各ハンドラーは次のハンドラーをラップします。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

次の方法のいずれかを使って、要求ごとの状態をメッセージ ハンドラーと共有します。

* `HttpRequestMessage.Properties` を使ってデータをハンドラーに渡します。
* `IHttpContextAccessor` を使って現在の要求にアクセスします。
* データを渡すカスタムの `AsyncLocal` ストレージ オブジェクトを作成します。

## <a name="use-polly-based-handlers"></a>Polly ベースのハンドラーを使用する

`IHttpClientFactory` は、[Polly](https://github.com/App-vNext/Polly) という名前の人気のあるサードパーティ製ライブラリと統合します。 Polly は、.NET 用の包括的な回復力および一時的エラー処理ライブラリです。 開発者は、自然でスレッド セーフな方法を使用して、Retry、Circuit Breaker、Timeout、Bulkhead Isolation、Fallback などのポリシーを表現できます。

構成されている `HttpClient` インスタンスで Polly ポリシーを使用できるようにするための、拡張メソッドが提供されています。 Polly の拡張機能は、次のようになっています。

* クライアントへの Polly ベースのハンドラーの追加がサポートされます。
* [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet パッケージのインストール後に使用できます。 このパッケージは、ASP.NET Core 共有フレームワークに含まれていません。

### <a name="handle-transient-faults"></a>一時的な障害を処理する

外部 HTTP を呼び出す際に発生する一般的な障害のほとんどは、一時的なものです。 `AddTransientHttpErrorPolicy` という便利な拡張メソッドが含まれており、一時的なエラーを処理するためのポリシーを定義できます。 この拡張メソッドで構成されたポリシーは、`HttpRequestException`、HTTP 5xx 応答、および HTTP 408 応答を処理します。

`AddTransientHttpErrorPolicy` 拡張メソッドは、`Startup.ConfigureServices` 内で使用できます。 この拡張メソッドは、可能性のある一時的障害を表すエラーを処理するように構成された `PolicyBuilder` オブジェクトへのアクセスを提供します。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

上記のコードでは、`WaitAndRetryAsync` ポリシーが定義されています。 失敗した要求は最大 3 回再試行され、再試行の間には 600 ミリ秒の遅延が設けられます。

### <a name="dynamically-select-policies"></a>ポリシーを動的に選択する

Polly ベースのハンドラーを追加するために使用できる追加の拡張メソッドが存在します。 そのような拡張メソッドの 1 つは `AddPolicyHandler` であり、複数のオーバーロードを持ちます。 1 つのオーバーロードでは、適用するポリシーを定義するときに要求を検査できます。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

上記のコードでは、送信要求が HTTP GET の場合は、10 秒のタイムアウトが適用されます。 他の HTTP メソッドの場合は、30 秒のタイムアウトが使用されます。

### <a name="add-multiple-polly-handlers"></a>複数の Polly ハンドラーを追加する

Polly ポリシーを入れ子にして拡張機能を提供するのが一般的です。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

前の例では、2 つのハンドラーが追加されています。 最初のハンドラーは、`AddTransientHttpErrorPolicy` 拡張メソッドを使用して再試行ポリシーを追加します。 失敗した要求は 3 回まで再試行されます。 `AddTransientHttpErrorPolicy` の 2 番目の呼び出しでは、サーキット ブレーカー ポリシーが追加されています。 試行が連続して 5 回失敗した場合、それ以上の外部要求は 30 秒間ブロックされます。 サーキット ブレーカー ポリシーはステートフルです。 このクライアントからのすべての呼び出しは、同じサーキット状態を共有します。

### <a name="add-policies-from-the-polly-registry"></a>Polly レジストリからポリシーを追加する

定期的に使用されるポリシーを管理するには、ポリシーを 1 回定義した後、`PolicyRegistry` でポリシーを登録します。 提供されている拡張メソッドを使用することで、レジストリからポリシーを使用してハンドラーを追加できます。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

上記のコードでは、`PolicyRegistry` が `ServiceCollection` に追加されるときに 2 つのポリシーが登録されています。 レジストリからポリシーを使用するには、適用するポリシーの名前を渡して `AddPolicyHandlerFromRegistry` メソッドを使用します。

`IHttpClientFactory` および Polly の統合について詳しくは、[Polly の Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory) をご覧ください。

## <a name="httpclient-and-lifetime-management"></a>HttpClient と有効期間の管理

`IHttpClientFactory` で `CreateClient` を呼び出すたびに、`HttpClient` の新しいインスタンスが返されます。 名前付きクライアントごとに <xref:System.Net.Http.HttpMessageHandler> が存在します。 ファクトリによって `HttpMessageHandler` インスタンスの有効期間が管理されます。

`IHttpClientFactory` は、リソースの消費量を減らすため、ファクトリによって作成された `HttpMessageHandler` のインスタンスをプールします。 新しい `HttpClient` インスタンスを作成するときに、プールの `HttpMessageHandler` インスタンスの有効期間が切れていない場合はそれを再利用する場合があります。

通常、各ハンドラーでは基になる HTTP 接続が独自に管理されるため、ハンドラーはプールすることが望まれます。 必要以上のハンドラーを作成すると、接続に遅延が発生する可能性があります。 また、一部のハンドラーは接続を無期限に開いており、DNS の変更にハンドラーが対応できないことがあります。

ハンドラーの既定の有効期間は 2 分です。 名前付きクライアントごとに、既定値をオーバーライドすることができます。 オーバーライドするには、クライアント作成時に返された `IHttpClientBuilder` で <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> を呼び出します。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

クライアントを破棄する必要はありません。 破棄すると送信要求がキャンセルされ、<xref:System.IDisposable.Dispose*> の呼び出し後には指定された `HttpClient` インスタンスを使用できなくなります。 `IHttpClientFactory` は、`HttpClient` インスタンスによって使用されたリソースの追跡と破棄を行います。 通常、`HttpClient` インスタンスは、破棄を必要としない .NET オブジェクトとして扱うことができます。

`IHttpClientFactory` の登場以前は、1 つの `HttpClient` インスタンスを長い期間使い続けるパターンが一般的に使用されていました。 `IHttpClientFactory` に移行した後は、このパターンは不要になります。

## <a name="logging"></a>ログの記録

`IHttpClientFactory` によって作成されたクライアントは、すべての要求のログ メッセージを記録します。 既定のログ メッセージを見るには、ログの構成で適切な情報レベルを有効にします。 要求ヘッダーのログなどの追加ログは、トレース レベルでのみ含まれます。

各クライアントに使用されるログのカテゴリには、クライアントの名前が含まれます。 たとえば、*MyNamedClient* という名前のクライアントは、カテゴリ `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` でメッセージをログに記録します。 *LogicalHandler* というサフィックスが付いたメッセージが、要求ハンドラーのパイプラインの外部で発生します。 要求では、パイプライン内の他のハンドラーが要求を処理する前に、メッセージがログに記録されます。 応答では、他のパイプライン ハンドラーが応答を受け取った後で、メッセージがログに記録されます。

ログ記録は、要求ハンドラーのパイプラインの内部でも行われます。 *MyNamedClient* の例では、それらのメッセージはログ カテゴリ `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` に対して記録されます。 要求では、他のすべてのハンドラーが実行した後、要求がネットワークに送信される直前に、これが行われます。 応答では、このログ記録には、ハンドラー パイプラインを通じ戻される前の応答の状態が含まれます。

パイプラインの外部と内部でログを有効にすると、他のパイプライン ハンドラーによる変更を検査できます。 これには、たとえば、要求ヘッダーや応答状態コードへの変更を含めることができます。

ログのカテゴリにクライアントの名前を含めると、必要に応じて、特定の名前付きクライアントでログをフィルター処理できます。

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler を構成する

クライアントによって使用される内部 `HttpMessageHandler` の構成を制御することが必要な場合があります。

名前付きクライアントまたは型指定されたクライアントを追加すると、`IHttpClientBuilder` が返されます。 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 拡張メソッドを使用して、デリゲートを定義することができます。 デリゲートは、そのクライアントによって使用されるプライマリ `HttpMessageHandler` の作成と構成に使用されます。

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>コンソール アプリで IHttpClientFactory を使用する

コンソール アプリで、次のパッケージ参照をプロジェクトに追加します。

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

次に例を示します。

* <xref:System.Net.Http.IHttpClientFactory> は[汎用ホスト](xref:fundamentals/host/generic-host)のサービス コンテナーに登録されます。
* `MyService` により、クライアント ファクトリ インスタンスがサービスから作成され、それが `HttpClient` の作成に使用されます。 `HttpClient` は Web ページを取得する目的で使用されます。
* `Main` により、サービスの `GetPage` メソッドを実行し、Web ページ コンテンツの最初の 500 文字をコンソールに書き込むためのスコープが作成されます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

::: moniker-end

## <a name="additional-resources"></a>その他の技術情報

* [HttpClientFactory を使用して回復力の高い HTTP 要求を実装する](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory ポリシーと Polly ポリシーで指数バックオフを含む HTTP 呼び出しの再試行を実装する](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [サーキット ブレーカー パターンを実装する](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
