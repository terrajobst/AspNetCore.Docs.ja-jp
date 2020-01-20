---
title: ASP.NET Core Blazor サポートされているプラットフォーム
author: guardrex
description: ASP.NET Core Blazorでサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160133"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a>ASP.NET Core Blazor サポートされているプラットフォーム

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>ブラウザー要件

### <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

| ブラウザー                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | [現在]               |
| Mozilla Firefox                  | [現在]               |
| Google Chrome (Android を含む) | [現在]               |
| Safari (iOS を含む)            | [現在]               |
| Microsoft Internet Explorer      | サポートされていません&dagger; |

&dagger;Microsoft Internet Explorer では、 [Webassembly](https://webassembly.org)サポートされていません。

### <a name="opno-locblazor-server"></a>Blazor サーバー

| ブラウザー                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | [現在]    |
| Mozilla Firefox                  | [現在]    |
| Google Chrome (Android を含む) | [現在]    |
| Safari (iOS を含む)            | [現在]    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;追加の polyfills が必要です (たとえば、 [Polyfill.io](https://polyfill.io/v3/)バンドルを使用して追加することができます)。

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/hosting-models>
