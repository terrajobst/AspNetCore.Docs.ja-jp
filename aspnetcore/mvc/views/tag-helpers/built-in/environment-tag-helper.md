---
title: ASP.NET Core の環境タグ ヘルパー
author: pkellner
description: すべてのプロパティを含む、定義済みの ASP.NET Core 環境タグ ヘルパー
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 308e7db47104ebd4d6bb8d08c64f14bbd118898b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653768"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>ASP.NET Core の環境タグ ヘルパー

著者: [Peter Kellner](https://peterkellner.net)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)

環境タグ ヘルパーは、現在の[ホスティング環境](xref:fundamentals/environments)に基づき、囲まれたコンテンツを条件付きで表示します。 環境タグ ヘルパーの 1 つの属性 `names` は、環境名のコンマ区切りリストです。 指定された環境名のいずれかが現在の環境と一致する場合、囲まれたコンテンツが表示されます。

タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。

## <a name="environment-tag-helper-attributes"></a>環境タグ ヘルパーの属性

### <a name="names"></a>names

`names` は、囲まれたコンテンツの表示をトリガーする単一のホスティング環境名またはホスティング環境名のコンマ区切りリストを受け入れます。

環境値は、[IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*) によって返される現在の値と比較されます。 比較では大文字と小文字の区別は無視されます。

環境タグ ヘルパーを使用する例を次に示します。 ホスティング環境が Staging または Production の場合、コンテンツが表示されます。

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>include および exclude 属性

`include` & `exclude` 属性は、含まれている、または除外されているホスト環境名に基づいて、囲まれたコンテンツを表示します。

### <a name="include"></a>include

`include` プロパティが示す動作は、`names` 属性と似ています。 `include` タグの内容が表示されるためには、[ 属性の値で列記されている環境が、アプリのホスティング環境 (](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)IHostingEnvironment.EnvironmentName`<environment>`) と一致する必要があります。

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclude

`include` 属性とは対照的に、`<environment>` タグの内容は、ホスティング環境が `exclude` 属性の値で列記されている環境と一致しない場合に表示されます。

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/environments>
