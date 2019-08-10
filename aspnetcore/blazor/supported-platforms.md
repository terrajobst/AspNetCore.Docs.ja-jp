---
title: Blazor でサポートされているプラットフォームの ASP.NET Core
author: guardrex
description: ASP.NET Core Blazor のサポートされているプラットフォームについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 01f3a55a8536feedf713e07ea3724a0bc51e7c63
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "68948252"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Blazor でサポートされているプラットフォームの ASP.NET Core

作成者: [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>ブラウザー要件

### <a name="blazor-client-side"></a>クライアント側 Blazor

| ブラウザー                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 現在               |
| Mozilla Firefox                  | 現在               |
| Google Chrome (Android を含む) | 現在               |
| Safari (iOS を含む)            | 現在               |
| [Microsoft Internet Explorer]      | サポートされていない&dagger; |

&dagger;Microsoft Internet Explorer では、 [Webassembly](https://webassembly.org)サポートされていません。

### <a name="blazor-server-side"></a>サーバー側 Blazor

| ブラウザー                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 現在    |
| Mozilla Firefox                  | 現在    |
| Google Chrome (Android を含む) | 現在    |
| Safari (iOS を含む)            | 現在    |
| [Microsoft Internet Explorer]      | 個&dagger; |

&dagger;追加の polyfills が必要です (たとえば、 [Polyfill.io](https://polyfill.io/v3/)バンドルを使用して追加することができます)。

## <a name="additional-resources"></a>その他の資料

* <xref:blazor/hosting-models>
