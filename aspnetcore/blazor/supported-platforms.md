---
title: Blazor でサポートされているプラットフォームの ASP.NET Core
author: guardrex
description: ASP.NET Core Blazor のサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 4e86bd6967a747a59c99a515c1c838cc2c21770f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391215"
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
| Microsoft Internet Explorer      | サポートされていない @ no__t-0 |

&dagger;Microsoft Internet Explorer では、 [Webassembly](https://webassembly.org)サポートされていません。

### <a name="blazor-server"></a>Blazor サーバー

| ブラウザー                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | [現在]    |
| Mozilla Firefox                  | [現在]    |
| Google Chrome (Android を含む) | [現在]    |
| Safari (iOS を含む)            | [現在]    |
| Microsoft Internet Explorer      | 11 @ no__t-0 |

&dagger;Additional を追加する必要があります (たとえば、 [Polyfill.io](https://polyfill.io/v3/)バンドルを使用して約束を追加できます)。

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/hosting-models>
