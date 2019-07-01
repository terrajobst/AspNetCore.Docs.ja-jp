---
title: ASP.NET 4.x と ASP.NET Core の選択
author: rick-anderson
description: ASP.NET Core とASP.NET 4.x の違いと、どちらかを選択する方法について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 05/02/2019
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 97d65df8df1e27694d818098958288874e8ab92e
ms.sourcegitcommit: 47cc13ab90913af9a2887cef0896bb4e9aba4dd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2019
ms.locfileid: "67399178"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>ASP.NET 4.x と ASP.NET Core の選択

ASP.NET Core は ASP.NET 4.x を再設計したものです。 この記事では、この 2 つの違いを一覧します。

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core は、Windows、macOS、または Linux で最新のクラウド ベースの Web アプリを構築するための、オープン ソースのクロスプラットフォーム フレームワークです。

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x は成熟したフレームワークであり、Windows 上でエンタープライズ グレードのサーバー ベース Web アプリを構築するために必要なサービスを提供します。

## <a name="framework-selection"></a>フレームワークの選択

次の表では、ASP.NET Core と ASP.NET 4.x を比較します。

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Windows、macOS、Linux が対象|Windows が対象|
|[Razor ページ](xref:razor-pages/index)は、ASP.NET Core 2.x の時点で Web UI を作成する場合に推奨される方法です。 [MVC](xref:mvc/overview)、[Web API](xref:tutorials/first-web-api)、[SignalR](xref:signalr/introduction) についても参照してください。|[Web フォーム](/aspnet/web-forms)、[SignalR](/aspnet/signalr)、[MVC](/aspnet/mvc)、[Web API](/aspnet/web-api/)、[WebHooks](/aspnet/webhooks/)、または [Web ページ](/aspnet/web-pages)を使います。|
|コンピューターごとに複数のバージョン|コンピューターごとに 1 つのバージョン|
|C# または F# を使い、[Visual Studio](https://visualstudio.microsoft.com/vs/)、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)、または [Visual Studio Code](https://code.visualstudio.com/) で 開発|C#、VB、または F# を使い、[Visual Studio](https://visualstudio.microsoft.com/vs/) で開発|
|ASP.NET 4.x より高いパフォーマンス|よいパフォーマンス|
|[.NET Framework または .NET Core ランタイムを選択します](/dotnet/standard/choosing-core-framework-server)|.NET Framework ランタイムを使います|

.NET Framework 上での ASP.NET Core 2.x のサポートについては、「[.NET Framework を対象とする ASP.NET Core](xref:index#target-framework)」を参照してください。

## <a name="aspnet-core-scenarios"></a>ASP.NET Core のシナリオ

* [Web サイト](xref:tutorials/first-mvc-app/index)
* [API](xref:tutorials/first-web-api)
* [リアルタイム](xref:signalr/index)
* [Azure に ASP.NET Core アプリをデプロイする](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET 4.x のシナリオ

* [Web サイト](/aspnet/mvc)
* [API](/aspnet/web-api)
* [リアルタイム](/aspnet/signalr)
* [Azure 内で ASP.NET 4.x Web アプリを作成する](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>その他の技術情報

* [ASP.NET の概要](/aspnet/overview)
* [ASP.NET Core の概要](xref:index)
* <xref:host-and-deploy/azure-apps/index>
