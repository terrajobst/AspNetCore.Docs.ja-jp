---
title: Blazor でサポートされているプラットフォームの ASP.NET Core
author: guardrex
description: ASP.NET Core Blazor のサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 042fbb1b2c7f92b7dc6443319f3f195a12a55adc
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963880"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Blazor でサポートされているプラットフォームの ASP.NET Core

作成者: [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>ブラウザー要件

### <a name="blazor-webassembly"></a>Blazor webas

| ブラウザー                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 現在               |
| Mozilla Firefox                  | 現在               |
| Google Chrome (Android を含む) | 現在               |
| Safari (iOS を含む)            | 現在               |
| [Microsoft Internet Explorer]      | サポートされていない&dagger; |

&dagger;Microsoft Internet Explorer では、 [Webassembly](https://webassembly.org)サポートされていません。

### <a name="blazor-server"></a>Blazor サーバー

| ブラウザー                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 現在    |
| Mozilla Firefox                  | 現在    |
| Google Chrome (Android を含む) | 現在    |
| Safari (iOS を含む)            | 現在    |
| [Microsoft Internet Explorer]      | 個&dagger; |

&dagger;追加の polyfills が必要です (たとえば、 [Polyfill.io](https://polyfill.io/v3/)バンドルを使用して追加することができます)。

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/hosting-models>
