---
title: ASP.NET Core Blazor サーバーのホストとデプロイ
author: guardrex
description: ASP.NET Core を使用して Blazor サーバー アプリをホストおよびデプロイする方法について学習します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: b688d000f26c9b230d9fdee8423b3194145fe1aa
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317297"
---
# <a name="host-and-deploy-opno-locblazor-server"></a>Blazor サーバーをホストおよびデプロイする

作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>ホストの構成値

[Blazor サーバー アプリ](xref:blazor/hosting-models#blazor-server)では、[汎用ホスト構成値](xref:fundamentals/host/generic-host#host-configuration)を受け入れることができます。

## <a name="deployment"></a>配置

[Blazor サーバーのホスティング モデル](xref:blazor/hosting-models#blazor-server)を使用する場合、Blazor はサーバー上で ASP.NET Core アプリ内から実行されます。 UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。

ASP.NET Core アプリをホストできる Web サーバーが必要です。 Visual Studio には **Blazor サーバー アプリ** プロジェクト テンプレートが含まれています ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorserverside` テンプレート)。

## <a name="scalability"></a>スケーラビリティ

Blazor サーバー アプリで使用できるインフラストラクチャを最大限に活用できるようにデプロイを計画します。 Blazor サーバー アプリのスケーラビリティに対処するには、次のリソースを参照してください。

* [Blazor サーバー アプリの基礎](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>展開サーバー

1 台のサーバーのスケーラビリティ (スケールアップ) を検討する場合、アプリに使用できるメモリは、ユーザーの要求が増えたときにアプリによって使い果たされる最初のリソースになります。 サーバー上の使用可能なメモリは、以下の影響を受けます。

* サーバーがサポートできるアクティブ回線の数。
* クライアントでの UI の待機時間。

セキュリティで保護されたスケーラブルな Blazor サーバー アプリを構築するためのガイダンスについては、「<xref:security/blazor/server>」を参照してください。

各回線では、最小限の *Hello World* スタイルのアプリに約 250 KB のメモリが使用されます。 回線のサイズは、アプリのコードと各コンポーネントに関連付けられている状態の保守要件によって変わります。 アプリとインフラストラクチャの開発時にはリソースのニーズを測定することをお勧めしますが、展開ターゲットを計画する際に、次のベースラインを出発点にすることができます。アプリで 5,000 人の同時ユーザーをサポートすることを想定している場合は、アプリに対して少なくとも 1.3 GB のサーバー メモリ (またはユーザーあたり最大 273 KB) の予算を割り当てること検討してください。

### <a name="opno-locsignalr-configuration"></a>SignalR 構成

Blazor サーバー アプリでは、ブラウザーとの通信に ASP.NET Core SignalR が使用されます。 [SignalR のホストとスケーリングの条件](xref:signalr/publish-to-azure-web-app)は、Blazor サーバー アプリに適用されます。

Blazor は、待ち時間、信頼性、および[セキュリティ](xref:signalr/security)が低いために WebSocket を SignalR トランスポートとして使用する場合に最適です。 WebSocket が使用できない場合や、ロング ポーリングを使用するようにアプリが明示的に構成されている場合は、SignalR によってロング ポーリングが使用されます。 Azure App Service にデプロイする場合は、サービスの Azure portal 設定で WebSocket を使用するようにアプリを構成します。 Azure App Service 用にアプリを構成する方法の詳細については、[SignalR の発行ガイドライン](xref:signalr/publish-to-azure-web-app)を参照してください。

#### <a name="azure-opno-locsignalr-service"></a>Azure SignalR Service

Blazor サーバー アプリには [Azure SignalR Service](/azure/azure-signalr) を使用することをお勧めします。 このサービスでは、多数の同時 SignalR 接続に対して Blazor Server アプリをスケールアップできます。 さらに、SignalR サービスのグローバル リーチとハイパフォーマンスのデータ センターは、地理的条件による待機時間の短縮に役立ちます。 アプリの構成 (および必要に応じてプロビジョニング) を行うために、Azure SignalR Service によって次が実行されます。

1. サービスで "*スティッキー セッション*" をサポートできるようにします。それにより、クライアントは[事前レンダリングのときに同じサーバーにリダイレクトされます](xref:blazor/hosting-models#reconnection-to-the-same-server)。 `ServerStickyMode` オプションまたは構成値を `Required` に設定します。 通常、アプリでは次の方法の**いずれか 1 つ**を使用して構成を作成します。

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * 構成 (次の方法の**いずれか**を使用):
  
     * *appsettings.json*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * Azure portal で App Service の **[構成]**  >  **[アプリケーションの設定]** (**名前**: `Azure:SignalR:ServerStickyMode`、**値**: `Required`)。

1. Blazor サーバー アプリ用に、Visual Studio に Azure アプリ発行プロファイルを作成する。
1. プロファイルに **Azure SignalR Service** の依存関係を追加する。 Azure サブスクリプションに、アプリに割り当てる既存の Azure SignalR Service のインスタンスがない場合は、 **[新しい Azure SignalR Service のインスタンスを作成する]** を選択して新しいサービス インスタンスをプロビジョニングします。
1. アプリを Azure に発行する

#### <a name="iis"></a>IIS

IIS を使用すると、スティッキー セッションはアプリケーション要求ルーティングによって有効になります。 詳しくは、「[アプリケーション要求ルーティングを使用した HTTP 負荷分散](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)」をご覧ください。

#### <a name="kubernetes"></a>Kubernetes

次の[スティッキー セッションに対する Kubernetes 注釈](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)を使用して、イングレス定義を作成します。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

### <a name="measure-network-latency"></a>ネットワーク待機時間の測定

次の例に示すように、[JS 相互運用機能](xref:blazor/javascript-interop)を使用してネットワーク待機時間を測定できます。

```cshtml
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

妥当な UI エクスペリエンスを実現するために、UI の待機時間を 250 ミリ秒以下にすることをお勧めします。
