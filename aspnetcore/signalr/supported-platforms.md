---
title: ASP.NET Core SignalR サポートされているプラットフォーム
author: bradygaster
description: ASP.NET Core SignalRでサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 054965921c87c1a9be27e5ddaa8a87b0fa1f4113
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146499"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a>ASP.NET Core SignalR サポートされているプラットフォーム

## <a name="server-system-requirements"></a>サーバー システムの要件

ASP.NET Core の SignalR は、ASP.NET Core がサポートするすべてのサーバープラットフォームをサポートします。

## <a name="javascript-client"></a>JavaScript クライアント

[JavaScript クライアント](xref:signalr/javascript-client)は、nodejs 8 以降のバージョンと次のブラウザーで実行されます。

| ブラウザー                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | 現在の&dagger; |
| Mozilla Firefox                 | 現在の&dagger; |
| Google Chrome。Android を含む | 現在の&dagger; |
| SafariiOS を含む            | 現在の&dagger; |
| Microsoft Internet Explorer     | 11              |

*現在*の &dagger;は、ブラウザーの最新バージョンを参照します。

## <a name="net-client"></a>.NET クライアント

[.Net クライアント](xref:signalr/dotnet-client)は、ASP.NET Core によってサポートされる任意のプラットフォームで実行されます。 たとえば、xamarin[開発者は](https://github.com/aspnet/Announcements/issues/305)、xamarin 8.4.0.1 以降と11.14.0.4 以降を使用して、android アプリをビルドするために SignalRを使用できます。

サーバーで IIS が実行されている場合、Websocket トランスポートでは Windows Server 2012 以降に IIS 8.0 以降が必要です。 その他のトランスポートはすべてのプラットフォームでサポートされています。

## <a name="java-client"></a>Java クライアント

[Java クライアント](xref:signalr/java-client)は、java 8 以降のバージョンをサポートしています。

## <a name="unsupported-clients"></a>サポートされていないクライアント

次のクライアントは使用できますが、試験的または非公式です。 現時点ではサポートされておらず、そうでない場合もあります。

* [C++client](https://github.com/aspnet/SignalR-Client-Cpp)

* [Swift クライアント](https://github.com/moozzyk/SignalR-Client-Swift)
