---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Microsoft .NET の概要について説明します。
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869767"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK

### <a name="overview"></a>の概要

`Microsoft.NET.Sdk.Web` は ASP.NET Core アプリを構築するための[MSBuild プロジェクト SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk)です。 ただし、この SDK を使用せずに ASP.NET Core アプリをビルドすることはできますが、Web SDK は次のようになります。

* ファーストクラスのエクスペリエンスを提供することに合わせて調整します。
* ほとんどのユーザーに推奨されるターゲット。

プロジェクト内の Web SDK を使用します。

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Web SDK を使用して有効にする機能:

* .NET Core 3.0 以降を対象とするプロジェクトは、次のように暗黙的に参照します。

  * [ASP.NET Core 共有フレームワーク](xref:fundamentals/metapackage-app)。
  * ASP.NET Core アプリを構築するために設計された[アナライザー](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) 。
* Web SDK は、Web Deploy を使用して発行プロファイルと発行を使用できるようにする MSBuild ターゲットをインポートします。

### <a name="properties"></a>[プロパティ]

| property | 説明 |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | `Microsoft.AspNetCore.App` 共有フレームワークへの暗黙的な参照を無効にします。 |
| `DisableImplicitAspNetCoreAnalyzers` | ASP.NET Core アナライザーへの暗黙的な参照を無効にします。 |
| `DisableImplicitComponentsAnalyzers` | Blazor (サーバー) アプリケーションをビルドするときに、Razor コンポーネントアナライザーへの暗黙的な参照を無効にします。 |
