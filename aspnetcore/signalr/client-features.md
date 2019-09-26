---
title: SignalR クライアントの機能
author: bradygaster
description: さまざまな ASP.NET Core SignalR クライアントでサポートされている機能について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301187"
---
# <a name="aspnet-core-signalr-client-features"></a>SignalR クライアント機能の ASP.NET Core

## <a name="feature-distribution"></a>機能の配布

次の表は、リアルタイムサポートを提供するクライアントの機能とサポートを示しています。

| 機能 | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Azure SignalR サービスのサポート |✔|✔|✔|
| [サーバーからクライアントへのストリーミング](xref:signalr/streaming)          |✔|✔|✔|
| [クライアントとサーバー間のストリーミング](xref:signalr/streaming)          |✔|✔|✔|
| 自動再接続 ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection)、 [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |✔|✔| |
| Websocket トランスポート |✔|✔|✔|
| サーバー送信イベントトランスポート |✔|✔| |
| 長いポーリングトランスポート |✔|✔|✔|
| JSON ハブプロトコル |✔|✔|✔|
| MessagePack ハブ プロトコル |✔|✔| |

Java クライアントでの自動再接続のサポートは[、microsoft の問題追跡ツール](https://github.com/aspnet/AspNetCore/issues/8711)で追跡されます。

## <a name="additional-resources"></a>その他の技術情報

* [ASP.NET Core の SignalR 概要](xref:tutorials/signalr)
* [サポートされているプラットフォーム](xref:signalr/supported-platforms)
* [ハブ](xref:signalr/hubs)
* [JavaScript クライアント](xref:signalr/javascript-client)
