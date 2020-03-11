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
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="10072-103">ASP.NET Core の要件ハンドラーでの依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="10072-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="10072-104">[承認ハンドラーは](xref:security/authorization/policies#handler-registration)、構成中に ([依存関係の挿入](xref:fundamentals/dependency-injection)を使用して) サービスコレクションに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="10072-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="10072-105">認可ハンドラー内に評価するルールのリポジトリがあり、そのリポジトリがサービスコレクションに登録されているとします。</span><span class="sxs-lookup"><span data-stu-id="10072-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="10072-106">承認はそれを解決してコンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="10072-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="10072-107">たとえば、ASP を使用する場合を考えてみます。`ILoggerFactory` をハンドラーに挿入する NET のログインフラストラクチャ。</span><span class="sxs-lookup"><span data-stu-id="10072-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="10072-108">ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="10072-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="10072-109">`services.AddSingleton()`にハンドラーを登録します。</span><span class="sxs-lookup"><span data-stu-id="10072-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="10072-110">アプリケーションの起動時にハンドラーのインスタンスが作成され、DI によって、登録された `ILoggerFactory` がコンストラクターに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="10072-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="10072-111">Entity Framework を使用したハンドラーは、シングルトンとして登録することはできません。</span><span class="sxs-lookup"><span data-stu-id="10072-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
