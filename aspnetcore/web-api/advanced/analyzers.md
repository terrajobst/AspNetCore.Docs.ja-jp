---
title: Web API アナライザーを使用する
author: pranavkm
description: ASP.NET Core MVC Web API アナライザー パッケージについて説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
uid: web-api/advanced/analyzers
ms.openlocfilehash: 7b6a7328deb8718a2a1c67c104cec359a4f13497
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082522"
---
# <a name="use-web-api-analyzers"></a>Web API アナライザーを使用する

ASP.NET Core 2.2 以降には、Web API プロジェクトでの使用を目的とした MVC アナライザー パッケージが用意されています。 アナライザーは、[Web API 規約](xref:web-api/advanced/conventions)の設定中に <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> の注釈が付けられたコントローラーで動作します。

アナライザー パッケージでは、次のようなコントローラーのアクションが通知されます。

* 宣言されていない状態コードを返す。
* 宣言されていない成功の結果を返す。
* 返されない状態コードを文書化する。
* 明示的なモデル検証チェックを含める。

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>アナライザー パッケージを参照する

ASP.NET Core 3.0 以降、アナライザーは .NET Core SDK に含まれています。 プロジェクトでアナライザーを有効にするには、`IncludeOpenAPIAnalyzers` プロパティをプロジェクト ファイルに追加します。

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>パッケージ インストール

次のいずれかの方法を使用して、[Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet パッケージをインストールします。

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**[パッケージ マネージャー コンソール]** ウィンドウから:
  * **[表示]** >  **[その他のウィンドウ]** > **[パッケージ マネージャー コンソール]** に移動します。
  * *ApiConventions.csproj* ファイルが存在するディレクトリに移動します。
  * 次のコマンドを実行します。

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **[Solution Pad]** > **[パッケージを追加]** で *[パッケージ]* フォルダーを右クリックします。
* **[パッケージを追加]** ウィンドウの **[ソース]** ドロップダウンを "nuget.org" に設定します。
* 検索ボックスに「Microsoft.AspNetCore.Mvc.Api.Analyzers」と入力します。
* 結果ウィンドウから "Microsoft.AspNetCore.Mvc.Api.Analyzers" パッケージを選択して、 **[パッケージを追加]** をクリックします。

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**統合端末**からから次のコマンドを実行します。

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

次のコマンドを実行します。

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Web API 規則のアナライザー

OpenAPI ドキュメントには、アクションによって返される可能性のある状態コードと応答の種類が含まれます。 ASP.NET Core MVC では、<xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> や <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> などの属性がアクションの文書化に使用されます。 Web API の文書化の詳細については、<xref:tutorials/web-api-help-pages-using-swagger> を参照してください。

パッケージのいずれかのアナライザーが、<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> の注釈が付けられたコントローラーを検査し、応答全体を文書化していないアクションを特定します。 次に例を示します。

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

前のアクションでは、HTTP 200 の成功の戻り値の型は文書化していますが、HTTP 404 のエラー状態コードは文書化していません。 アナライザーは、HTTP 404 状態コードの文書化の不備を警告として報告します。 問題を解決するためのオプションが提供されます。

![警告を報告するアナライザー](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>その他の技術情報

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
