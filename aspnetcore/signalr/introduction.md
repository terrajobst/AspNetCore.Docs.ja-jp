---
title: ASP.NET Core SignalR の概要
author: bradygaster
description: ASP.NET Core SignalR ライブラリを通じて、リアルタイムの機能をアプリに簡単に追加する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 7108d9f223db78937dd1203a1cb4b890006b20ec
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963937"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a>ASP.NET Core SignalR の概要

## <a name="what-is-opno-locsignalr"></a>SignalRとは

ASP.NET Core SignalR は、リアルタイムの web 機能をアプリに簡単に追加できるオープンソースライブラリです。 リアルタイム web 機能を使用すると、サーバー側のコードでクライアントにコンテンツを瞬時にプッシュできます。

SignalRの候補として適しています。

* サーバーからの高頻度の更新を必要とするアプリ。 例として、ゲーム、ソーシャルネットワーク、投票、オークション、地図、GPS アプリがあります。
* ダッシュボードと監視アプリ。 例としては、会社のダッシュボード、インスタント販売の更新、旅行の通知などがあります。
* コラボレーションアプリ。 共同作業用アプリの例としては、ホワイトボードアプリとチームミーティングソフトウェアがあります。
* 通知が必要なアプリ。 ソーシャルネットワーク、電子メール、チャット、ゲーム、旅行アラート、およびその他の多くのアプリが通知を使用します。

SignalR には、サーバー間の[リモートプロシージャ呼び出し (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)を作成するための API が用意されています。 Rpc は、サーバー側 .NET Core コードからクライアントで JavaScript 関数を呼び出します。

ASP.NET Core の SignalR の機能をいくつか次に示します。

* 接続管理を自動的に処理します。
* 接続されているすべてのクライアントに同時にメッセージを送信します。 たとえば、チャットルームなどです。
* 特定のクライアントまたはクライアントグループにメッセージを送信します。
* 増加するトラフィックを処理するようにスケーリングします。

ソースは、 [GitHub のSignalR リポジトリ](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)でホストされています。

## <a name="transports"></a>トランスポート

SignalR は、リアルタイム通信を処理するためのいくつかの手法をサポートしています。

* [WebSocket](https://tools.ietf.org/html/rfc7118)
* サーバーから送信されたイベント
* 長いポーリング

SignalR は、サーバーとクライアントの機能内にある最適なトランスポート方法を自動的に選択します。

## <a name="hubs"></a>ハブ

SignalR は、*ハブ*を使用してクライアントとサーバー間の通信を行います。

ハブは、クライアントとサーバーが相互にメソッドを呼び出すことができるようにする、高レベルのパイプラインです。 SignalR は、コンピューターの境界を越えたディスパッチを自動的に処理するので、クライアントはサーバーでメソッドを呼び出すことができ、その逆も可能です。 厳密に型指定されたパラメーターをメソッドに渡すことができます。これにより、モデルバインドが有効になります。 SignalR には、JSON に基づくテキストプロトコルと[Messagepack](https://msgpack.org/)に基づくバイナリプロトコルという2つの組み込みのハブプロトコルが用意されています。  MessagePack では、通常、JSON と比較してより小さなメッセージが作成されます。 以前のブラウザーでは、MessagePack プロトコルのサポートを提供するために、 [Xhr レベル 2](https://caniuse.com/#feat=xhr2)がサポートされている必要があります。

ハブは、クライアント側のメソッドの名前とパラメーターを含むメッセージを送信することによって、クライアント側のコードを呼び出します。 メソッドパラメーターとして送信されるオブジェクトは、構成されたプロトコルを使用して逆シリアル化されます。 クライアントは、クライアント側コードのメソッドと名前を一致させようとします。 クライアントが一致を検出すると、メソッドを呼び出し、逆シリアル化されたパラメーターデータに渡します。

## <a name="additional-resources"></a>その他の技術情報

* [ASP.NET Core の SignalR の概要](xref:tutorials/signalr)
* [サポートされているプラットフォーム](xref:signalr/supported-platforms)
* [ハブ](xref:signalr/hubs)
* [JavaScript クライアント](xref:signalr/javascript-client)
