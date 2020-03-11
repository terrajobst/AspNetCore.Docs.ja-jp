---
title: ASP.NET Core の要件ハンドラーでの依存関係の挿入
author: rick-anderson
description: 依存関係の挿入を使用して ASP.NET Core アプリに承認要件ハンドラーを挿入する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 71d563e11d308a95c08e6d012d3a071f4697d2de
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654362"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>ASP.NET Core の要件ハンドラーでの依存関係の挿入

<a name="security-authorization-di"></a>

[承認ハンドラーは](xref:security/authorization/policies#handler-registration)、構成中に ([依存関係の挿入](xref:fundamentals/dependency-injection)を使用して) サービスコレクションに登録する必要があります。

認可ハンドラー内に評価するルールのリポジトリがあり、そのリポジトリがサービスコレクションに登録されているとします。 承認はそれを解決してコンストラクターに挿入します。

たとえば、ASP を使用する場合を考えてみます。`ILoggerFactory` をハンドラーに挿入する NET のログインフラストラクチャ。 ハンドラーは次のようになります。

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

`services.AddSingleton()`にハンドラーを登録します。

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

アプリケーションの起動時にハンドラーのインスタンスが作成され、DI によって、登録された `ILoggerFactory` がコンストラクターに挿入されます。

> [!NOTE]
> Entity Framework を使用したハンドラーは、シングルトンとして登録することはできません。
