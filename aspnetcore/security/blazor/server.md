---
title: コアBlazorサーバー アプリASP.NETセキュリティ保護
author: guardrex
description: サーバー アプリに対するセキュリティBlazor上の脅威を軽減する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626019"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>コアブレイザサーバーアプリASP.NETセキュア

[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)

Blazor Server アプリは、サーバーとクライアントが長期間の関係を維持する*ステートフル*なデータ処理モデルを採用しています。 持続状態は、潜在的に長寿命の接続にまたがる可能性がある[回線](xref:blazor/state-management)によって維持されます。

ユーザーが Blazor サーバー サイトにアクセスすると、サーバーはサーバーのメモリに回線を作成します。 この回線は、ユーザーが UI のボタンを選択したときなど、レンダリングするコンテンツをブラウザーに示し、イベントに応答します。 これらのアクションを実行するために、回線はユーザーのブラウザーで JavaScript 関数を呼び出し、サーバー上の .NET メソッドを呼び出します。 この双方向の JavaScript ベースの対話は[、JavaScript 相互運用機能 (JS 相互運用)](xref:blazor/call-javascript-from-dotnet)と呼ばれます。

JS 相互運用機能はインターネット上で発生し、クライアントはリモート ブラウザーを使用するため、Blazor Server アプリは Web アプリのセキュリティに関する懸念を共有します。 このトピックでは、Blazor Server アプリに対する一般的な脅威について説明し、インターネットに接続するアプリに焦点を当てた脅威軽減ガイダンスを提供します。

社内ネットワークやイントラネットなど、制約のある環境では、次のいずれかの軽減策のガイダンスを示します。

* 制約された環境では適用されません。
* 制限された環境ではセキュリティ リスクが低いため、実装にかかるコストに値しません。

## <a name="blazor-server-project-template"></a>ブラゾール サーバー プロジェクト テンプレート

Blazor サーバーのプロジェクト テンプレートは、プロジェクトの作成時に認証用に構成できます。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio のガイダンスに従ってください。

**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor サーバー アプリ]** テンプレートを選択した後、**[認証]** の下の **[変更]** を選択します。

ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。

* **認証なし**
* **個人のユーザー アカウント** &ndash; ユーザー アカウントは次に格納できます。
  * ASP.NET Core の [ID](xref:security/authentication/identity) システムを使用するアプリ内。
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c)を使用します。
* **職場または学校のアカウント**
* **[Windows 認証]**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio Code のガイダンスに従ってください。

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。

| 認証メカニズム                                                                 | `{AUTHENTICATION}` 値 |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| [認証なし]                                                                        | `None`                   |
| 個人<br>ASP.NET Core ID を使用するアプリに格納されているユーザー。                        | `Individual`             |
| 個人<br>[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー。 | `IndividualB2C`          |
| 職場または学校アカウント<br>単一のテナントに対する組織認証。            | `SingleOrg`              |
| 職場または学校アカウント<br>複数のテナントに対する組織認証です。           | `MultiOrg`               |
| Windows 認証                                                                   | `Windows`                |

このコマンドで、`{APP NAME}` プレースホルダーに指定された値の名前が付けられたフォルダーが作成され、そのフォルダー名がアプリ名として使用されます。 詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. この記事の「Mac 用の<xref:blazor/get-started>Visual Studio」のガイダンスに従ってください。

1. 新**しい Blazor サーバー アプリの構成**ステップで、[**認証**] ドロップダウンから **[個別認証 (アプリ内)] を**選択します。

1. アプリは、ASP.NETコア ID を使用してアプリに保存された個々のユーザー用に作成されます。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

この記事の .NET Core <xref:blazor/get-started> CLI ガイダンスに従って、認証メカニズムを使用して新しい Blazor Server プロジェクトを作成します。

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。

| 認証メカニズム                                                                 | `{AUTHENTICATION}` 値 |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| [認証なし]                                                                        | `None`                   |
| 個人<br>ASP.NET Core ID を使用するアプリに格納されているユーザー。                        | `Individual`             |
| 個人<br>[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー。 | `IndividualB2C`          |
| 職場または学校アカウント<br>単一のテナントに対する組織認証。            | `SingleOrg`              |
| 職場または学校アカウント<br>複数のテナントに対する組織認証です。           | `MultiOrg`               |
| Windows 認証                                                                   | `Windows`                |

このコマンドで、`{APP NAME}` プレースホルダーに指定された値の名前が付けられたフォルダーが作成され、そのフォルダー名がアプリ名として使用されます。 詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。

---

## <a name="pass-tokens-to-a-blazor-server-app"></a>Blazor サーバー アプリにトークンを渡す

通常の Razor ページまたは MVC アプリと同様に、Blazor サーバー アプリを認証します。 トークンをプロビジョニングして、認証 Cookie に保存します。 次に例を示します。

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

完全な`Startup.ConfigureServices`例を含むサンプル コードについては、「[サーバー側の Blazor アプリケーションへのトークンの引き渡し](https://github.com/javiercn/blazor-server-aad-sample)」を参照してください。

アクセス トークンと更新トークンを使用して、初期アプリの状態で渡すクラスを定義します。

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Di からトークンを解決するために Blazor アプリ内で使用できる**スコープトークン**プロバイダー サービスを定義します。

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

で`Startup.ConfigureServices`、次のサービスを追加します。

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

*_Host.cshtml*ファイルで、作成してインスタンスを`InitialApplicationState`作成し、パラメーターとしてアプリに渡します。

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

`App`コンポーネント (*App.razor*) でサービスを解決し、パラメータのデータで初期化します。

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

セキュアな API 要求を行うサービスで、トークンプロバイダーを注入し、API を呼び出すためのトークンを取得します。

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a>リソースの枯渇

クライアントがサーバーと対話し、サーバーが過剰なリソースを消費する場合、リソースの枯渇が発生する可能性があります。 過度なリソース消費は主に以下に影響します。

* [Cpu](#cpu)
* [メモリ](#memory)
* [クライアント接続](#client-connections)

通常、サービス拒否 (DoS) 攻撃は、アプリやサーバーのリソースを使い果たそうとします。 ただし、リソースの枯渇は、必ずしもシステムへの攻撃の結果ではありません。 たとえば、ユーザーの需要が高いため、有限のリソースが枯渇する可能性があります。 DoS については、[サービス拒否 (DoS) 攻撃](#denial-of-service-dos-attacks)のセクションで詳しく説明します。

また、データベースやファイル ハンドル (ファイルの読み取りと書き込みに使用) など、Blazor フレームワークの外部にあるリソースでも、リソースの枯渇が発生する可能性があります。 詳細については、「<xref:performance/performance-best-practices>」を参照してください。

### <a name="cpu"></a>CPU

CPU の枯渇は、1 つ以上のクライアントがサーバーに CPU の集中的な処理を強制的に実行させた場合に発生する可能性があります。

たとえば、*フィボナクッチ数*を計算する Blazor Server アプリを考えてみましょう。 フィボナッチ数はフィボナッチ数列から生成され、シーケンス内の各番号は先行する 2 つの数値の合計になります。 応答に到達するために必要な作業量は、シーケンスの長さと初期値のサイズによって異なります。 アプリがクライアントの要求に制限を設けなければ、CPU を大量に消費する計算が CPU 時間を制限し、他のタスクのパフォーマンスを低下させる可能性があります。 リソースの過剰消費は、可用性に影響を与えるセキュリティ上の問題です。

CPUの枯渇は、すべての一般向けアプリにとって懸念事項です。 通常の Web アプリでは、要求と接続はセーフガードとしてタイムアウトしますが、Blazor Server アプリは同じ保護機能を提供していません。 Blazor Server アプリは、CPU を大量に消費する可能性のある作業を実行する前に、適切なチェックと制限を含める必要があります。

### <a name="memory"></a>メモリ

1 つ以上のクライアントがサーバーに大量のメモリを強制的に消費させた場合に、メモリの枯渇が発生する可能性があります。

たとえば、項目のリストを受け入れて表示するコンポーネントを持つ Blazor サーバー側のアプリを考えてみます。 Blazor アプリが、許可されている項目の数やクライアントに戻される項目数に制限を設けなければ、メモリを消費する処理とレンダリングがサーバーのメモリを支配し、サーバーのパフォーマンスが低下する可能性があります。 サーバーがクラッシュしたり、クラッシュしたと思われる時点まで遅くなったりすることがあります。

サーバー上で発生する可能性のあるメモリ不足のシナリオに関連する項目の一覧を保守および表示する場合は、次のシナリオを検討してください。

* プロパティまたはフィールド内`List<MyItem>`の項目は、サーバーのメモリを使用します。 アプリで項目のリストの拡張が制限されていない場合、サーバーがメモリ不足になる危険性があります。 メモリ不足の場合、現在のセッションは終了 (クラッシュ) し、そのサーバー インスタンス内のすべての同時セッションがメモリ不足の例外を受け取ります。 このシナリオが発生しないようにするには、同時ユーザーに項目制限を課すデータ構造を使用する必要があります。
* 描画にページング スキームが使用されていない場合、サーバーは UI に表示されないオブジェクトに追加のメモリを使用します。 項目数に制限がない場合、メモリ要求によって使用可能なサーバー メモリが使い果たされる可能性があります。 このシナリオを回避するには、次のいずれかの方法を使用します。
  * 表示時にページ分割されたリストを使用します。
  * 最初の 100 ~ 1,000 個のアイテムのみを表示し、表示されたアイテム以外のアイテムを検索するために検索条件を入力する必要があります。
  * より高度なレンダリング シナリオを実現するには、*仮想化*をサポートするリストまたはグリッドを実装します。 仮想化を使用すると、リストは現在ユーザーに表示されている項目のサブセットのみをレンダリングします。 ユーザーが UI のスクロールバーを操作すると、コンポーネントは表示に必要な項目のみをレンダリングします。 表示に現在必要のない項目はセカンダリ・ストレージに保持できるため、理想的なアプローチです。 表示されていない項目はメモリに保持することもできますが、これはあまり理想的ではありません。

Blazor Server アプリは、WPF、Windows フォーム、または Blazor WebAssembly などのステートフル アプリ用の他の UI フレームワークと同様のプログラミング モデルを提供します。 主な違いは、いくつかの UI フレームワークでは、アプリによって消費されるメモリがクライアントに属し、その個々のクライアントにのみ影響を与える点です。 たとえば、Blazor WebAssembly アプリはクライアント上で完全に実行され、クライアントメモリリソースのみを使用します。 Blazor Server シナリオでは、アプリによって消費されるメモリはサーバーに属し、サーバー インスタンス上のクライアント間で共有されます。

サーバー側のメモリ要求は、すべての Blazor Server アプリに対する考慮事項です。 ただし、ほとんどの Web アプリはステートレスであり、要求の処理中に使用されるメモリは、応答が返されたときに解放されます。 一般的な推奨事項として、クライアント接続を保持する他のサーバー側アプリのように、クライアントが非バインドメモリの量を割り当てることを許可しないでください。 Blazor Server アプリケーションで消費されるメモリは、1 つの要求よりも長い時間持続します。

> [!NOTE]
> 開発中は、プロファイラーを使用するか、トレースをキャプチャしてクライアントのメモリ要求を評価できます。 プロファイラーまたはトレースは、特定のクライアントに割り当てられたメモリをキャプチャしません。 開発中に特定のクライアントのメモリ使用量をキャプチャするには、ダンプをキャプチャし、ユーザーの回線にルートされているすべてのオブジェクトのメモリ要求を調べます。

### <a name="client-connections"></a>クライアント接続

接続の枯渇は、1 つ以上のクライアントがサーバーへの同時接続を開きすぎて、他のクライアントが新しい接続を確立できない場合に発生する可能性があります。

Blazor クライアントは、セッションごとに 1 つの接続を確立し、ブラウザーウィンドウが開いている間は接続を開いたままにします。 すべての接続を維持するサーバー上の要求は、Blazor アプリに固有のものではありません。 接続の永続的な性質と Blazor Server アプリのステートフルな性質を考えると、接続の枯渇は、アプリの可用性に対するリスクが大きくなります。

既定では、Blazor Server アプリのユーザーごとの接続数に制限はありません。 アプリで接続制限が必要な場合は、次の方法を 1 つ以上実行します。

* 認証を要求する場合は、承認されていないユーザーがアプリに接続する機能が自然に制限されます。 このシナリオを有効にするには、ユーザーが新しいユーザーを必要に応じプロビジョニングできないようにする必要があります。
* ユーザーごとの接続数を制限します。 接続の制限は、次の方法で行うことができます。 正規ユーザーがアプリにアクセスできるように (たとえば、クライアントの IP アドレスに基づいて接続制限が確立された場合) に注意してください。
  * アプリ レベルで:
    * エンドポイント ルーティングの拡張性。
    * アプリに接続し、ユーザーごとのアクティブなセッションを追跡するために認証を要求します。
    * 制限に達した場合に新しいセッションを拒否します。
    * プロキシを使用してアプリへの WebSocket のプロキシ接続 (クライアントからアプリへの接続を多重化する[Azure SignalR サービス](/azure/azure-signalr/signalr-overview)など)。 これにより、単一のクライアントが確立できるよりも大きな接続容量をアプリに提供し、クライアントがサーバーへの接続を使い果たすことを防ぎます。
  * サーバー レベル: アプリの前でプロキシ/ゲートウェイを使用します。 たとえば[、Azure Front Door](/azure/frontdoor/front-door-overview)を使用すると、アプリへの Web トラフィックのグローバル ルーティングを定義、管理、および監視できます。

## <a name="denial-of-service-dos-attacks"></a>サービス拒否 (DoS) 攻撃

サービス拒否 (DoS) 攻撃には、クライアントが原因でサーバーが 1 つ以上のリソースを使い果たしてアプリを利用不能にします。 Blazor Server アプリには、いくつかの既定の制限が含まれ、DoS 攻撃から保護するために、他のASP.NETコアと SignalR の制限に依存しています。

| ブレイザサーバーアプリの制限                            | 説明 | Default |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | 特定のサーバーが一度にメモリ内に保持する切断回線の最大数。 | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | 切断された回線が取り壊されるまでメモリ内に保持される最大時間。 | 3 分 |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | 非同期 JavaScript 関数呼び出しがタイムアウトになるまでのサーバーの最大待機時間。 | 1 分 |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | サーバーが一定時間に回線あたりのメモリに保持し、堅牢な再接続をサポートするために、応答なしのレンダリング バッチの最大数。 制限に達すると、サーバーは、1 つ以上のバッチがクライアントによって確認されるまで、新しいレンダリング バッチの生成を停止します。 | 10 |


| シグナルとASP.NETコアの制限             | 説明 | Default |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | 個々のメッセージのメッセージ サイズ。 | 32 KB |

## <a name="interactions-with-the-browser-client"></a>ブラウザ(クライアント)との対話

クライアントは、JS 相互運用イベントのディスパッチとレンダリング完了を通じてサーバーと対話します。 JS 相互運用通信は、JavaScript と .NET の両方の方法で行われます。

* ブラウザイベントは、クライアントからサーバに非同期的に送出されます。
* サーバーは、必要に応じて UI の再レンダリングを非同期的に応答します。

### <a name="javascript-functions-invoked-from-net"></a>NET から呼び出された Java スクリプト関数

NET メソッドから JavaScript への呼び出しの場合:

* すべての呼び出しには、失敗した後に構成可能な<xref:System.OperationCanceledException>タイムアウトがあり、呼び出し元に a が返されます。
  * 呼び出し ( )`CircuitOptions.JSInteropDefaultCallTimeout`のデフォルトのタイムアウトは 1 分です。 この制限を構成するには、「」を参照してください<xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>。
  * キャンセル トークンを提供して、呼び出しごとにキャンセルを制御できます。 キャンセル トークンが提供されている場合は、可能な場合は既定の呼び出しタイムアウトに依存し、クライアントへの任意の呼び出しを時間に制限します。
* JavaScript 呼び出しの結果は信頼できません。 ブラウザーBlazorで実行されているアプリ クライアントは、呼び出す JavaScript 関数を検索します。 関数が呼び出され、結果またはエラーが生成されます。 悪意のあるクライアントは、次の処理を試みる可能性があります。
  * JavaScript 関数からエラーを返すことで、アプリで問題が発生します。
  * JavaScript 関数から予期しない結果を返すことで、サーバー上で意図しない動作を発生させます。

上記のシナリオに対する防御策として、次の対策を講じます。

* 呼び出し中に発生する可能性のあるエラーを考慮するために[、try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメント内で JS 相互運用呼び出しをラップします。 詳細については、「<xref:blazor/handle-errors#javascript-interop>」を参照してください。
* 何らかのアクションを実行する前に、エラー メッセージを含む JS 相互運用の呼び出しから返されるデータを検証します。

### <a name="net-methods-invoked-from-the-browser"></a>ブラウザから呼び出された .NET メソッド

JavaScript から .NET メソッドへの呼び出しを信頼しないでください。 .NET メソッドが JavaScript に公開されている場合は、.NET メソッドがどのように呼び出されるのかを考慮してください。

* JavaScript に公開されている .NET メソッドは、アプリのパブリック エンドポイントと同じように扱います。
  * 入力を検証します。
    * 値が期待される範囲内にあることを確認します。
    * ユーザーが要求したアクションを実行する権限を持っていることを確認します。
  * .NET メソッド呼び出しの一部として、リソースの過剰な量を割り当てないでください。 たとえば、CPU とメモリの使用に関するチェックや制限を実行します。
  * 静的メソッドとインスタンス メソッドは JavaScript クライアントに公開できることを考慮に入れます。 設計で適切な制約を使用して状態を共有する必要がある場合を除き、セッション間で状態を共有しないようにします。
    * 依存関係挿入 (DI)`DotNetReference`によって作成されたオブジェクトを通じて公開されるメソッドの場合、オブジェクトはスコープ指定されたオブジェクトとして登録する必要があります。 これは、サーバー アプリケーションが使用するすべてのBlazorDI サービスに適用されます。
    * 静的メソッドの場合、アプリがサーバー インスタンス上のすべてのユーザー間で明示的に状態を設計別で共有していない限り、クライアントにスコープを設定できない状態を確立しないようにします。
  * パラメーター内のユーザー指定のデータを JavaScript 呼び出しに渡さないようにします。 パラメーターでデータを渡す必要がある場合は、[クロスサイト スクリプティング (XSS)](#cross-site-scripting-xss)の脆弱性を導入せずに、JavaScript コードがデータの受け渡しを処理するようにしてください。 たとえば、要素のプロパティを設定して、ユーザーが指定したデータをドキュメント オブジェクト モデル (DOM) に`innerHTML`書き込まないでください。 コンテンツ[セキュリティ ポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) `eval`を使用して、無効にするなどの安全でない JavaScript プリミティブを使用することを検討してください。
* フレームワークのディスパッチの実装の上に .NET 呼び出しのカスタム ディスパッチを実装しないでください。 .NET メソッドをブラウザに公開することは高度なシナリオであり、一般的Blazorな開発には推奨されません。

### <a name="events"></a>イベント

イベントは、サーバー アプリケーションへのBlazorエントリ ポイントを提供します。 Web アプリのエンドポイントを保護する場合と同じ規則が、ServerBlazorアプリのイベント処理にも適用されます。 悪意のあるクライアントは、イベントのペイロードとして送信するデータを送信できます。

次に例を示します。

* の変更イベント`<select>`は、アプリがクライアントに提示したオプションの範囲内にない値を送信できます。
* クライアント`<input>`側の検証をバイパスして、任意のテキスト データをサーバーに送信できます。

アプリが処理するイベントのデータを検証する必要があります。 フレームワークBlazor[フォーム コンポーネント](xref:blazor/forms-validation)は、基本的な検証を実行します。 アプリでカスタム フォーム コンポーネントを使用する場合は、イベント データを適切に検証するカスタム コードを記述する必要があります。

Blazorサーバー イベントは非同期であるため、アプリが新しいレンダリングを生成して反応する時間がなくなる前に、複数のイベントをサーバーにディスパッチできます。 これには、考慮すべきセキュリティ上の影響があります。 アプリでのクライアント アクションの制限は、イベント ハンドラー内で実行する必要があり、現在レンダリングされているビューステートに依存しません。

ユーザーが最大 3 回のカウンタをインクリメントできるようにするカウンター コンポーネントを考えてみます。 カウンタをインクリメントするボタンは、条件に応じて`count`、 の値に基づいています。

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

クライアントは、フレームワークがこのコンポーネントの新しいレンダリングを生成する前に、1 つ以上のインクリメント イベントをディスパッチできます。 その結果、ボタンが`count`UI によって十分に迅速に削除されないため、ユーザーによって*3 回以上*インクリメントできます。 3 つの`count`増分の制限を達成する正しい方法を次の例に示します。

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

ハンドラー内に`if (count < 3) { ... }`チェックを追加すると、インクリメント`count`の決定は、現在のアプリの状態に基づいて決定されます。 この決定は、前の例のように UI の状態に基づいていません。

### <a name="guard-against-multiple-dispatches"></a>複数のディスパッチに対する防御

イベント コールバックが、外部サービスやデータベースからのデータのフェッチなど、実行時間の長い操作を非同期に呼び出す場合は、ガードを使用することを検討してください。 ガードは、操作が視覚的なフィードバックで進行中の間に、ユーザーが複数の操作をキューに入れないようにすることができます。 次のコンポーネント コード`isLoading`は`true`、`GetForecastAsync`サーバーからデータを取得する場合に設定します。 の`isLoading`間`true`は、ボタンは UI で無効になります。

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

前の例で示したガード パターンは、バックグラウンド操作がパターンと共に非同期に`async`-`await`実行される場合に機能します。

### <a name="cancel-early-and-avoid-use-after-dispose"></a>早期にキャンセルし、廃棄後の使用を避ける

[「複数のディスパッチに対するガード](#guard-against-multiple-dispatches)」セクションで説明されているようにガードを使用する以外に、<xref:System.Threading.CancellationToken>を使用して、コンポーネントが破棄されるときに長時間実行される操作を取り消すことを検討してください。 この方法には、コンポーネントでの*廃棄後の使用*を回避するという利点があります。

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>大量のデータを生成するイベントを回避する

や`oninput``onscroll`などの DOM イベントの中には、大量のデータを生成するものもあります。 これらのイベントは、サーバーBlazorアプリで使用しないでください。

## <a name="additional-security-guidance"></a>追加のセキュリティ ガイダンス

core アプリ ASP.NETのセキュリティ保護に関Blazorするガイダンスは、Server アプリに適用され、次のセクションで説明します。

* [ログ記録と機密データ](#logging-and-sensitive-data)
* [HTTPS を使用して転送中の情報を保護する](#protect-information-in-transit-with-https)
* [クロスサイト スクリプティング (XSS)](#cross-site-scripting-xss)
* [クロスオリジン保護](#cross-origin-protection)
* [クリックジャッキング](#click-jacking)
* [リダイレクトを開く](#open-redirects)

### <a name="logging-and-sensitive-data"></a>ログ記録と機密データ

クライアントとサーバー間の JS 相互運用機能の相互作用は、インスタンスを持つ<xref:Microsoft.Extensions.Logging.ILogger>サーバーのログに記録されます。 Blazor実際のイベントや JS 相互運用機能の入力や出力などの機密情報のログ記録を回避できます。

サーバーでエラーが発生すると、フレームワークはクライアントに通知し、セッションを削除します。 既定では、クライアントは、ブラウザーの開発者ツールで表示できる一般的なエラー メッセージを受け取ります。

クライアント側エラーにはコールスタックは含まれておらず、エラーの原因の詳細は提供されませんが、サーバーログにはこのような情報が含まれています。 開発の目的で、詳細なエラーを有効にすることで、重要なエラー情報をクライアントに提供できます。

次の場合に詳細なエラーを有効にします。

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`コンフィギュレーション キー。 たとえば、環境変数の`ASPNETCORE_DETAILEDERRORS`値を`true`に設定します。

> [!WARNING]
> インターネット上のクライアントにエラー情報を公開することは、常に避けるべきセキュリティリスクです。

### <a name="protect-information-in-transit-with-https"></a>HTTPS を使用して転送中の情報を保護する

BlazorサーバーはSignalR、クライアントとサーバー間の通信に使用します。 Blazorサーバーは通常、ネゴシエSignalRートするトランスポートを使用します。

Blazorサーバーは、サーバーとクライアントの間で送信されるデータの整合性と機密性を保証しません。 常に HTTPS を使用してください。

### <a name="cross-site-scripting-xss"></a>クロスサイト スクリプティング (XSS)

クロスサイト スクリプティング (XSS) を使用すると、承認されていないユーザーがブラウザのコンテキストで任意のロジックを実行できます。 侵害されたアプリは、クライアント上で任意のコードを実行する可能性があります。 この脆弱性は、サーバーに対して悪意のある多数のアクションを実行する可能性があります。

* 偽/無効イベントをサーバーにディスパッチします。
* ディスパッチが失敗した/無効なレンダリングの完了。
* レンダリングの完了をディスパッチしないようにします。
* 相互運用機能の呼び出しを JavaScript から .NET にディスパッチします。
* .NET から JavaScript への相互運用呼び出しの応答を変更します。
* .NET を JS 相互運用結果にディスパッチしないようにします。

ServerBlazorフレームワークでは、上記の脅威に対して保護するための手順を実行します。

* クライアントがレンダリング バッチを受信確認していない場合、新しい UI 更新の生成を停止します。 で`CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`構成されます。
* クライアントからの応答を受信せずに、1 分後に .NET から JavaScript への呼び出しをタイムアウトします。 で`CircuitOptions.JSInteropDefaultCallTimeout`構成されます。
* JS 相互運用中にブラウザから来るすべての入力に対して基本的な検証を実行します。
  * .NET 参照は有効であり、.NET メソッドで予期される型です。
  * データの形式が正しくありません。
  * メソッドの正しい引数の数がペイロードに存在します。
  * 引数または結果は、メソッドを呼び出す前に正しく逆シリアル化できます。
* ディスパッチされたイベントからブラウザーから来るすべての入力で基本的な検証を実行します。
  * イベントの種類が有効です。
  * イベントのデータは逆シリアル化できます。
  * イベントに関連付けられたイベント ハンドラーがあります。

フレームワークが実装するセーフガードに加えて、アプリは、開発者が脅威から保護し、適切なアクションを実行するコード化する必要があります。

* イベントを処理する場合は、常にデータを検証します。
* 無効なデータを受け取った場合は、適切なアクションを実行します。
  * データを無視して戻ります。 これにより、アプリは要求の処理を続行できます。
  * アプリが入力が非合法であり、正当なクライアントによって生成できなかった場合は、例外をスローします。 例外をスローすると、回線が引き裂かれ、セッションが終了します。
* ログに含まれるレンダリング バッチの完了によって提供されるエラー メッセージを信頼しないでください。 エラーはクライアントによって提供され、クライアントが侵害される可能性があるため、一般的に信頼できません。
* JavaScript メソッドと .NET メソッド間のどちらの方向にも、JS 相互運用呼び出しに関する入力を信頼しないでください。
* 引数または結果が正しく逆シリアル化されている場合でも、引数と結果の内容が有効であることを検証するアプリは、します。

XSS の脆弱性が存在するには、レンダリングされたページにユーザー入力をアプリに組み込む必要があります。 Blazorサーバー コンポーネントは *、.razor*ファイル内のマークアップが手続き型 C# ロジックに変換されるコンパイル時の手順を実行します。 実行時に、C# ロジックは、要素、テキスト、および子コンポーネントを記述する*レンダリング ツリー*を構築します。 これは、JavaScript 命令のシーケンスを介してブラウザーの DOM に適用されます (または、プリレンダリングの場合は HTML にシリアル化されます)。

* 通常の Razor 構文 (たとえば)`@someStringValue`を使用してレンダリングされたユーザー入力は、テキストを書き込むだけのコマンドを介して Razor 構文が DOM に追加されるため、XSS の脆弱性を公開しません。 値に HTML マークアップが含まれている場合でも、値は静的テキストとして表示されます。 プリレンダリング時に出力される HTML エンコードは、コンテンツも静的テキストとして表示されます。
* スクリプト タグは許可されず、アプリのコンポーネント レンダー ツリーに含めるべきではありません。 コンポーネントのマークアップにスクリプト タグが含まれている場合は、コンパイル エラーが生成されます。
* コンポーネントの作成者は、Razor を使用せずに C# でコンポーネントを作成できます。 コンポーネントの作成者は、出力を出力する際に正しい API を使用する必要があります。 たとえば、後者`builder.AddContent(0, someUserSuppliedString)`が XSS の脆弱性を引き起こす可能性があるとして、使用*し、使用しない*`builder.AddMarkupContent(0, someUserSuppliedString)`などです。

XSS 攻撃に対する保護の一環として、コンテンツ セキュリティ ポリシー [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)などの XSS の緩和策の実装を検討してください。

詳細については、「<xref:security/cross-site-scripting>」を参照してください。

### <a name="cross-origin-protection"></a>クロスオリジン保護

クロスオリジン攻撃には、別のオリジンのクライアントがサーバーに対してアクションを実行する必要があります。 悪意のあるアクションは、通常、GET要求またはフォームPOST(クロスサイトリクエストフォージェリ、CSRF)ですが、悪意のあるWebSocketを開くことも可能です。 Blazorサーバー アプリ[は、ハブ プロトコルを使用SignalRする他のアプリが提供するのと同じ保証を提供](xref:signalr/security)します。

* Blazorサーバー アプリは、それを防ぐために追加の対策を講じなければ、クロス オリジンにアクセスできます。 クロスオリジンアクセスを無効にするには、CORS ミドルウェアをパイプラインに追加してエンドポイントの CORS を無効`DisableCorsAttribute`にし、Blazorエンドポイントメタデータに を追加するか、[クロスオリジンリソース共有を構成してSignalR](xref:signalr/security#cross-origin-resource-sharing)許可されたオリジンのセットを制限します。
* CORS が有効になっている場合、CORS の構成によっては、アプリを保護するために追加の手順が必要になる場合があります。 CORS がグローバルに有効になっている場合は、呼び出しBlazor`hub.MapBlazorHub()`後にエンドポイント メタデータ`DisableCorsAttribute`にメタデータを追加することで、サーバー ハブの CORS を無効にすることができます。

詳細については、「<xref:security/anti-request-forgery>」を参照してください。

### <a name="click-jacking"></a>クリックジャッキング

クリックジャッキングでは、サイトを別のオリジン`<iframe>`のサイト内としてレンダリングし、ユーザーをだまして攻撃を受けているサイトでアクションを実行させることが必要です。

の内部でアプリがレンダリングされないようにするには、`<iframe>`コンテンツ[セキュリティ ポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) `X-Frame-Options`とヘッダーを使用します。 詳細については、 [MDN Web ドキュメント: X フレーム オプション](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)を参照してください。

### <a name="open-redirects"></a>リダイレクトを開く

サーバーBlazorアプリケーション セッションが開始されると、セッションの開始の一部として送信される URL の基本的な検証がサーバーによって実行されます。 フレームワークは、回線を確立する前に、ベース URL が現在の URL の親であることを確認します。 フレームワークによって追加のチェックは実行されません。

ユーザーがクライアント上のリンクを選択すると、リンクの URL がサーバーに送信され、どのアクションを実行するかが決まります。 たとえば、アプリはクライアント側のナビゲーションを実行したり、新しい場所に移動するようにブラウザーに指示したりできます。

コンポーネントは、 の使用を通じて、プログラムによるナビゲーション`NavigationManager`要求をトリガすることもできます。 このようなシナリオでは、アプリはクライアント側のナビゲーションを実行するか、新しい場所に移動するようにブラウザーに示す場合があります。

コンポーネントは次の項目を必要とします。

* ナビゲーション呼び出しの引数の一部としてユーザー入力を使用しないでください。
* 引数を検証して、ターゲットがアプリで許可されていることを確認します。

そうしないと、悪意のあるユーザーが強制的にブラウザを攻撃者が制御するサイトに移動する可能性があります。 このシナリオでは、攻撃者は、メソッドの呼び出しの一部として、ユーザー入力を`NavigationManager.Navigate`使用するアプリを騙します。

このアドバイスは、アプリの一部としてリンクをレンダリングする場合にも適用されます。

* 可能であれば、相対リンクを使用します。
* 絶対リンク先がページに含まれる前に有効であることを検証します。

詳細については、「<xref:security/preventing-open-redirects>」を参照してください。

## <a name="authentication-and-authorization"></a>認証と権限承認

認証と承認のガイダンスについては、を<xref:security/blazor/index>参照してください。

## <a name="security-checklist"></a>セキュリティ チェックリスト

次のセキュリティに関する考慮事項の一覧は、包括的ではありません。

* イベントの引数を検証します。
* JS 相互運用呼び出しからの入力と結果を検証します。
* .NET から JS 相互運用呼び出しに対してユーザー入力を使用 (または事前に検証) することは避けてください。
* クライアントが非バインドメモリを割り当てないようにします。
  * コンポーネント内のデータ。
  * `DotNetObject`クライアントに返される参照。
* 複数のディスパッチに対するガード。
* コンポーネントが破棄されるときに、長時間実行される操作をキャンセルします。
* 大量のデータを生成するイベントは避けてください。
* ユーザー入力を呼び出しの一`NavigationManager.Navigate`部として使用することは避け、最初に許可された発信元のセットに対して URL のユーザー入力を検証します。
* UI の状態に基づいて承認の決定を行うのではなく、コンポーネントの状態からのみ行います。
* コンテンツ[セキュリティ ポリシー (CSP) を](https://developer.mozilla.org/docs/Web/HTTP/CSP)使用して、XSS 攻撃から保護することを検討してください。
* クリックジャッキングから保護するために、CSP と[X フレーム オプション](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)の使用を検討してください。
* CORS を有効にする場合は CORS 設定が適切であることをBlazor確認するか、アプリの CORS を明示的に無効にします。
* Blazorアプリのサーバー側の制限が許容できないレベルのリスクを伴わずに許容可能なユーザー エクスペリエンスを提供することを確認するテスト。
