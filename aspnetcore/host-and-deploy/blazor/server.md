---
title: ASP.NET Core Blazor サーバーのホストとデプロイ
author: guardrex
description: ASP.NET Core を使用して Blazor サーバー アプリをホストおよびデプロイする方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 866bb348180c872d8ab20787283cfb7217183a8d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "79025427"
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

1. サービスで "*スティッキー セッション*" をサポートできるようにします。それにより、クライアントは[事前レンダリングのときに同じサーバーにリダイレクトされます](xref:blazor/hosting-models#connection-to-the-server)。 `ServerStickyMode` オプションまたは構成値を `Required` に設定します。 通常、アプリでは次の方法の**いずれか 1 つ**を使用して構成を作成します。

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
1. Azure にアプリを公開します。

#### <a name="iis"></a>IIS

IIS を使用する場合は、次を有効にします。

* [IIS での WebSockets](xref:fundamentals/websockets#enabling-websockets-on-iis)
* [アプリケーション要求ルーティング処理でのスティッキー セッション](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)

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

#### <a name="linux-with-nginx"></a>Nginx を使用した Linux

SignalR WebSocket が正常に機能するには、プロキシの `Upgrade` と `Connection` のヘッダーが次の値に設定されていること、および `$connection_upgrade` が次のどちらかにマップされていることを確認します。

* アップグレード ヘッダーの値 (既定)。
* アップグレード ヘッダーが存在しないか、空である場合は `close`。

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

詳細については、次の記事を参照してください。

* [WebSocket プロキシとしての NGINX](https://www.nginx.com/blog/websocket-nginx/)
* [WebSocket プロキシ](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>ネットワーク待機時間の測定

次の例に示すように、[JS 相互運用機能](xref:blazor/call-javascript-from-dotnet)を使用してネットワーク待機時間を測定できます。

```razor
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
