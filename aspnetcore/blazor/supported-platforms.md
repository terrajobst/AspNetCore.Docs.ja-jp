---
title: ASP.NET Core Blazor サポートされているプラットフォーム
author: guardrex
description: ASP.NET Core Blazorでサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/supported-platforms
ms.openlocfilehash: de51296cc8785474e1c1406cfd5d4e5bd4050172
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962738"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a>ASP.NET Core Blazor サポートされているプラットフォーム

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>ブラウザー要件

### <a name="opno-locblazor-webassembly"></a>Blazor webas

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

&dagger;Additional を追加する必要があります (たとえば、 [Polyfill.io](https://polyfill.io/v3/)バンドルを使用して約束を追加できます)。

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/hosting-models>
