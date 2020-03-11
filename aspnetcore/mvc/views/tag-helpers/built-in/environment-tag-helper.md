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
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="38327-103">ASP.NET Core の環境タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="38327-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="38327-104">著者: [Peter Kellner](https://peterkellner.net)、[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="38327-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="38327-105">環境タグ ヘルパーは、現在の[ホスティング環境](xref:fundamentals/environments)に基づき、囲まれたコンテンツを条件付きで表示します。</span><span class="sxs-lookup"><span data-stu-id="38327-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="38327-106">環境タグ ヘルパーの 1 つの属性 `names` は、環境名のコンマ区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="38327-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="38327-107">指定された環境名のいずれかが現在の環境と一致する場合、囲まれたコンテンツが表示されます。</span><span class="sxs-lookup"><span data-stu-id="38327-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="38327-108">タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="38327-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="38327-109">環境タグ ヘルパーの属性</span><span class="sxs-lookup"><span data-stu-id="38327-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="38327-110">names</span><span class="sxs-lookup"><span data-stu-id="38327-110">names</span></span>

<span data-ttu-id="38327-111">`names` は、囲まれたコンテンツの表示をトリガーする単一のホスティング環境名またはホスティング環境名のコンマ区切りリストを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="38327-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="38327-112">環境値は、[IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*) によって返される現在の値と比較されます。</span><span class="sxs-lookup"><span data-stu-id="38327-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="38327-113">比較では大文字と小文字の区別は無視されます。</span><span class="sxs-lookup"><span data-stu-id="38327-113">The comparison ignores case.</span></span>

<span data-ttu-id="38327-114">環境タグ ヘルパーを使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="38327-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="38327-115">ホスティング環境が Staging または Production の場合、コンテンツが表示されます。</span><span class="sxs-lookup"><span data-stu-id="38327-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="38327-116">include および exclude 属性</span><span class="sxs-lookup"><span data-stu-id="38327-116">include and exclude attributes</span></span>

<span data-ttu-id="38327-117">`include` & `exclude` 属性は、含まれている、または除外されているホスト環境名に基づいて、囲まれたコンテンツを表示します。</span><span class="sxs-lookup"><span data-stu-id="38327-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="38327-118">include</span><span class="sxs-lookup"><span data-stu-id="38327-118">include</span></span>

<span data-ttu-id="38327-119">`include` プロパティが示す動作は、`names` 属性と似ています。</span><span class="sxs-lookup"><span data-stu-id="38327-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="38327-120">`include` タグの内容が表示されるためには、[ 属性の値で列記されている環境が、アプリのホスティング環境 (](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)IHostingEnvironment.EnvironmentName`<environment>`) と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38327-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="38327-121">exclude</span><span class="sxs-lookup"><span data-stu-id="38327-121">exclude</span></span>

<span data-ttu-id="38327-122">`include` 属性とは対照的に、`<environment>` タグの内容は、ホスティング環境が `exclude` 属性の値で列記されている環境と一致しない場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="38327-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="38327-123">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="38327-123">Additional resources</span></span>

* <xref:fundamentals/environments>
