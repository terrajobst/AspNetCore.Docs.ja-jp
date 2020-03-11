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
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>2\.1 から2.2 または3.0 への移行

この記事では、2.1 から2.2 または3.0 の `Microsoft.Extensions.Logging` を使用する non-ASP.NET Core アプリケーションを移行するための一般的な手順について説明します。

## <a name="21-to-22"></a>2.1 から 2.2

手動で `ServiceCollection` を作成し、`AddLogging`を呼び出します。

2.1 の例:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2.2 の例:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2.1 ~ 3.0

3\.0 では、`LoggingFactory.Create`を使用します。

2.1 の例:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3.0 の例:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>その他のリソース

<xref:fundamentals/logging/index>