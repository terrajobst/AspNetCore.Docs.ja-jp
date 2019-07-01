---
title: サーバー側 ASP.NET Core Blazor のホストと展開
author: guardrex
description: ASP.NET Core を使用して Blazor サーバー側アプリをホストおよびデプロイする方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8b332c2fb439e9832d604ed26f972b266eed2507
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406120"
---
# <a name="host-and-deploy-blazor-server-side"></a>サーバー側 Blazor をホストおよび展開する

作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>ホストの構成値

[サーバー側のホスティング モデル](xref:blazor/hosting-models#server-side)を使用するサーバー側アプリでは、[汎用ホスト構成値](xref:fundamentals/host/generic-host#host-configuration)を受け入れることができます。

## <a name="deployment"></a>配置

[サーバー側のホスティング モデル](xref:blazor/hosting-models#server-side)では、Blazor はサーバー上で ASP.NET Core アプリ内から実行されます。 UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。

ASP.NET Core アプリをホストできる Web サーバーが必要です。 Visual Studio には **Blazor (サーバー側)** プロジェクト テンプレートが含まれています ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorserverside` テンプレート)。

## <a name="connection-scale-out"></a>接続のスケールアウト

サーバー側 Blazor アプリでは、ユーザーごとに 1 つのアクティブな SignalR 接続が必要です。 サーバー側 Blazor を運用環境に展開するには、アプリで必要になるのと同じ数のコンカレント接続をサポートするためのソリューションが必要です。 [Azure SignalR Service](/azure/azure-signalr/) では接続のスケーリングを処理でき、サーバー側 Blazor アプリ用のスケーリング ソリューションとしてお勧めです。 詳細については、<xref:signalr/publish-to-azure-web-app> を参照してください。

## <a name="signalr-configuration"></a>SignalR の構成

最も一般的なサーバー側 Blazor のシナリオでは、SignalR は ASP.NET Core によって構成されます。 カスタムの高度なシナリオについては、「[その他の技術情報](#additional-resources)」セクションにある SignalR の記事をご覧ください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:signalr/introduction>
* [Azure SignalR Service のドキュメント](/azure/azure-signalr/)
* [クイック スタート:SignalR Service を使用してチャット ルームを作成する](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Azure App Service に ASP.NET Core プレビュー リリースを展開する](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
