---
title: ASP.NET Core Blazor でサポートされているプラットフォーム
author: guardrex
description: ASP.NET Core Blazor でサポートされているプラットフォームについて学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647108"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor でサポートされているプラットフォーム

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>ブラウザーの要件

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| Browser                          | バージョン               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Current               |
| Mozilla Firefox                  | Current               |
| Google Chrome (Android を含む) | Current               |
| Safari (iOS を含む)            | Current               |
| Microsoft Internet Explorer      | サポートされていません&dagger; |

&dagger;Microsoft Internet Explorer は [WebAssembly](https://webassembly.org) をサポートしていません。

### <a name="blazor-server"></a>Blazor サーバー

| Browser                          | バージョン    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Current    |
| Mozilla Firefox                  | Current    |
| Google Chrome (Android を含む) | Current    |
| Safari (iOS を含む)            | Current    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;追加のポリフィルが必要です (たとえば、[Polyfill.io](https://polyfill.io/v3/) バンドルによって Promise を追加できます)。

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/hosting-models>
