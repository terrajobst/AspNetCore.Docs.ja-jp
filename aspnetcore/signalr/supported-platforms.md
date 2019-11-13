---
title: ASP.NET Core SignalR サポートされているプラットフォーム
author: bradygaster
description: ASP.NET Core SignalRでサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 86ba5b1aec230d78c1a0e1709187e129df6cb4cc
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963736"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a>ASP.NET Core SignalR サポートされているプラットフォーム

## <a name="server-system-requirements"></a>サーバーシステムの要件

ASP.NET Core の SignalR は、ASP.NET Core がサポートするすべてのサーバープラットフォームをサポートします。

## <a name="javascript-client"></a>JavaScript クライアント

[JavaScript クライアント](https://www.npmjs.com/package/@aspnet/signalr)は、nodejs 8 以降のバージョンと次のブラウザーで実行されます。

| ブラウザー                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | 現在の&dagger; |
| Mozilla Firefox                 | 現在の&dagger; |
| Google Chrome。Android を含む | 現在の&dagger; |
| SafariiOS を含む            | 現在の&dagger; |
| Microsoft Internet Explorer     | 11              |

*現在*の &dagger;は、ブラウザーの最新バージョンを参照します。

## <a name="net-client"></a>.NET クライアント

[.Net クライアント](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)は、ASP.NET Core によってサポートされる任意のプラットフォームで実行されます。 たとえば、xamarin[開発者は](https://github.com/aspnet/Announcements/issues/305)、xamarin 8.4.0.1 以降と11.14.0.4 以降を使用して、android アプリをビルドするために SignalRを使用できます。

サーバーで IIS が実行されている場合、Websocket トランスポートでは Windows Server 2012 以降に IIS 8.0 以降が必要です。 その他のトランスポートはすべてのプラットフォームでサポートされています。

## <a name="java-client"></a>Java クライアント

[Java クライアント](https://search.maven.org/artifact/com.microsoft.aspnet/signalr)は、java 8 以降のバージョンをサポートしています。

## <a name="unsupported-clients"></a>サポートされていないクライアント

次のクライアントは使用できますが、試験的または非公式です。 現時点ではサポートされておらず、そうでない場合もあります。

* [C++client](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Swift クライアント](https://github.com/moozzyk/SignalR-Client-Swift)
