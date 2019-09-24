---
title: Blazor でサポートされているプラットフォームの ASP.NET Core
author: guardrex
description: ASP.NET Core Blazor のサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/supported-platforms
ms.openlocfilehash: b769ee175cde7c9a613d7fb70949de129ca428d3
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211568"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Blazor でサポートされているプラットフォームの ASP.NET Core

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>ブラウザー要件

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| ブラウザー                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | [現在]               |
| Mozilla Firefox                  | [現在]               |
| Google Chrome (Android を含む) | [現在]               |
| Safari (iOS を含む)            | [現在]               |
| Microsoft Internet Explorer      | サポートされていない&dagger; |

&dagger;Microsoft Internet Explorer では、 [Webassembly](https://webassembly.org)サポートされていません。

### <a name="blazor-server"></a>Blazor サーバー

| ブラウザー                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | [現在]    |
| Mozilla Firefox                  | [現在]    |
| Google Chrome (Android を含む) | [現在]    |
| Safari (iOS を含む)            | [現在]    |
| Microsoft Internet Explorer      | 個&dagger; |

&dagger;追加の polyfills が必要です (たとえば、 [Polyfill.io](https://polyfill.io/v3/)バンドルを使用して追加することができます)。

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/hosting-models>
