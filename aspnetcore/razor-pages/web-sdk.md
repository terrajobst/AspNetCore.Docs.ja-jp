---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Microsoft .NET の概要について説明します。
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 49e21e1a432149409a01550452cedf4009dcfba7
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830646"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK

### <a name="overview"></a>の概要

`Microsoft.NET.Sdk.Web` は ASP.NET Core アプリを構築するための[MSBuild プロジェクト SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk)です。 ただし、この SDK を使用せずに ASP.NET Core アプリをビルドすることはできますが、Web SDK は次のようになります。

* ファーストクラスのエクスペリエンスを提供することに合わせて調整します。
* ほとんどのユーザーに推奨されるターゲット。

プロジェクト内の Web SDK を使用します。

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Web SDK を使用して有効にする機能:

* .NET Core 3.0 以降を対象とするプロジェクトは、次のように暗黙的に参照します。

  * [ASP.NET Core 共有フレームワーク](xref:fundamentals/metapackage-app)。
  * ASP.NET Core アプリを構築するために設計された[アナライザー](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) 。
* WebSDK を使用すると、発行プロファイルの使用を有効にする MSBuild ターゲットと WebDeploy を使用した発行を有効にすることができます。

### <a name="properties"></a>[プロパティ]

| property | 説明 |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | `Microsoft.AspNetCore.App` 共有フレームワークへの暗黙的な参照を無効にします。 |
| `DisableImplicitAspNetCoreAnalyzers` | ASP.NET Core アナライザーへの暗黙的な参照を無効にします。 |
| `DisableImplicitComponentsAnalyzers` | Blazor (サーバー) アプリケーションをビルドするときに、Razor コンポーネントアナライザーへの暗黙的な参照を無効にします。 |
