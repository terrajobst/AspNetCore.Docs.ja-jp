---
title: 2\.1 から2.2 または3.0 への移行
author: pakrym
description: 2\.1 から2.2 または3.0 を使用する non-ASP.NET Core アプリケーションを移行する方法について説明します。
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651878"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="c9534-103">2\.1 から2.2 または3.0 への移行</span><span class="sxs-lookup"><span data-stu-id="c9534-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="c9534-104">この記事では、2.1 から2.2 または3.0 の `Microsoft.Extensions.Logging` を使用する non-ASP.NET Core アプリケーションを移行するための一般的な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="c9534-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="c9534-105">2.1 から 2.2</span><span class="sxs-lookup"><span data-stu-id="c9534-105">2.1 to 2.2</span></span>

<span data-ttu-id="c9534-106">手動で `ServiceCollection` を作成し、`AddLogging`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c9534-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="c9534-107">2.1 の例:</span><span class="sxs-lookup"><span data-stu-id="c9534-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="c9534-108">2.2 の例:</span><span class="sxs-lookup"><span data-stu-id="c9534-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="c9534-109">2.1 ~ 3.0</span><span class="sxs-lookup"><span data-stu-id="c9534-109">2.1 to 3.0</span></span>

<span data-ttu-id="c9534-110">3\.0 では、`LoggingFactory.Create`を使用します。</span><span class="sxs-lookup"><span data-stu-id="c9534-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="c9534-111">2.1 の例:</span><span class="sxs-lookup"><span data-stu-id="c9534-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="c9534-112">3.0 の例:</span><span class="sxs-lookup"><span data-stu-id="c9534-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="c9534-113">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c9534-113">Additional resources</span></span>

<xref:fundamentals/logging/index>