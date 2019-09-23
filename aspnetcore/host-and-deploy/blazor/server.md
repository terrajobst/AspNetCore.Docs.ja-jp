---
title: ASP.NET Core Blazor サーバーをホストして展開する
author: guardrex
description: ASP.NET Core を使用して Blazor サーバー アプリをホストおよび展開する方法について学習します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: a393d620924d847e674a09972515a8130a15fc6a
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70964228"
---
# <a name="host-and-deploy-blazor-server"></a>Blazor サーバーをホストして展開する

作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>ホストの構成値

[Blazor サーバー アプリ](xref:blazor/hosting-models#blazor-server)では、[汎用ホスト構成値](xref:fundamentals/host/generic-host#host-configuration)を受け入れることができます。

## <a name="deployment"></a>配置

[Blazor サーバーのホスティング モデル](xref:blazor/hosting-models#blazor-server)を使用する場合、Blazor はサーバー上で ASP.NET Core アプリ内から実行されます。 UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。

ASP.NET Core アプリをホストできる Web サーバーが必要です。 Visual Studio には **Blazor サーバー アプリ** プロジェクト テンプレートが含まれています ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorserverside` テンプレート)。

## <a name="scalability"></a>スケーラビリティ

Blazor サーバー アプリで使用できるインフラストラクチャを最大限に活用できるように展開を計画します。 Blazor サーバー アプリのスケーラビリティに対処するには、次のリソースを参照してください。

* [Blazor サーバー アプリの基礎](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>展開サーバー

1 台のサーバーのスケーラビリティ (スケールアップ) を検討する場合、アプリに使用できるメモリは、ユーザーの要求が増えたときにアプリによって使い果たされる最初のリソースになります。 サーバー上の使用可能なメモリは、以下の影響を受けます。

* サーバーがサポートできるアクティブ回線の数。
* クライアントでの UI の待機時間。

セキュリティで保護されたスケーラブルな Blazor サーバー アプリを構築するためのガイダンスについては、「<xref:security/blazor/server>」を参照してください。

各回線では、最小限の *Hello World* スタイルのアプリに約 250 KB のメモリが使用されます。 回線のサイズは、アプリのコードと各コンポーネントに関連付けられている状態の保守要件によって変わります。 アプリとインフラストラクチャの開発時にはリソースのニーズを測定することをお勧めしますが、展開ターゲットを計画する際に、次のベースラインを出発点にすることができます。アプリで 5,000 人の同時ユーザーをサポートすることを想定している場合は、アプリに対して少なくとも 1.3 GB のサーバー メモリ (またはユーザーあたり最大 273 KB) の予算を割り当てること検討してください。

### <a name="signalr-configuration"></a>SignalR の構成

Blazor サーバー アプリでは、ブラウザーとの通信に ASP.NET Core SignalR が使用されます。 [SignalR のホストとスケールの条件](xref:signalr/publish-to-azure-web-app)は、Blazor サーバー アプリに適用されます。

Blazor は、待ち時間、信頼性、および[セキュリティ](xref:signalr/security)が低いために WebSocket を SignalR トランスポートとして使用する場合に最適です。 WebSocket が使用できない場合や、ロング ポーリングを使用するようにアプリが明示的に構成されている場合は、SignalR によってロング ポーリングが使用されます。 Azure App Service にデプロイする場合は、サービスの Azure portal 設定で WebSocket を使用するようにアプリを構成します。 Azure App Service 用にアプリを構成する方法の詳細については、[SignalR の発行ガイドライン](xref:signalr/publish-to-azure-web-app)を参照してください。

Blazor サーバー アプリには [Azure SignalR Service](/azure/azure-signalr) を使用することをお勧めします。 このサービスでは、多数の同時 SignalR 接続に対して Blazor Server アプリをスケールアップできます。 さらに、SignalR サービスのグローバル リーチと高パフォーマンスのデータ センターは、地理的条件による待機時間の短縮に役立ちます。

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
