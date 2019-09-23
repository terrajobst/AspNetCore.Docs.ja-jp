---
title: SignalR クライアントの機能
author: bradygaster
description: さまざまな ASP.NET Core SignalR クライアントでサポートされている機能について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 55086673e0c9f9b73f07730ea25c3fa322f7fd98
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187390"
---
# <a name="aspnet-core-signalr-client-features"></a>SignalR クライアント機能の ASP.NET Core

## <a name="feature-distribution"></a>機能の配布

次の表は、リアルタイムサポートを提供するクライアントの機能とサポートを示しています。

| 機能 | .NET Core | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| [サーバーからクライアントへのストリーミング](xref:signalr/streaming)          |✔|✔|✔|
| [クライアントとサーバー間のストリーミング](xref:signalr/streaming)          |✔|✔|✔|
| 自動再接続 ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection)、 [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |✔|✔| |

## <a name="additional-resources"></a>その他の技術情報

* [ASP.NET Core の SignalR 概要](xref:tutorials/signalr)
* [サポートされているプラットフォーム](xref:signalr/supported-platforms)
* [ハブ](xref:signalr/hubs)
* [JavaScript クライアント](xref:signalr/javascript-client)
