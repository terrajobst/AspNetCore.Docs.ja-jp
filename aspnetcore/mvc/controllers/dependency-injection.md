---
title: ASP.NET Core でのコントローラーへの依存関係の挿入
author: ardalis
description: ASP.NET Core の MVC コントローラーが、ASP.NET Core でそれらのコンストラクターと依存関係の挿入を使用して、明示的にそれらの依存関係を要求する方法について説明します。
ms.author: riande
ms.date: 02/24/2019
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 202b62d4b30c5c61c407abdc8509a2a75e181cb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652154"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="e44dd-103">ASP.NET Core でのコントローラーへの依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="e44dd-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="e44dd-104">作成者: [Shadi Namrouti](https://github.com/shadinamrouti)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="e44dd-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="e44dd-105">ASP.NET Core の MVC コントローラーは、コンストラクターを使用して明示的に依存関係を要求します。</span><span class="sxs-lookup"><span data-stu-id="e44dd-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="e44dd-106">ASP.NET Core には、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) の組み込みのサポートがあります。</span><span class="sxs-lookup"><span data-stu-id="e44dd-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e44dd-107">DI を利用すれば、アプリのテストと保守管理が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="e44dd-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="e44dd-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e44dd-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="e44dd-109">コンストラクターの挿入</span><span class="sxs-lookup"><span data-stu-id="e44dd-109">Constructor Injection</span></span>

<span data-ttu-id="e44dd-110">サービスはコンストラクター パラメーターとして追加されます。ランタイムによって、サービス コンテナーからサービスが解決されます。</span><span class="sxs-lookup"><span data-stu-id="e44dd-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="e44dd-111">サービスは通常、インターフェイスを利用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="e44dd-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="e44dd-112">たとえば、現在の時刻を必要とするアプリについて考えてください。</span><span class="sxs-lookup"><span data-stu-id="e44dd-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="e44dd-113">次のインターフェイスは `IDateTime` サービスを公開します。</span><span class="sxs-lookup"><span data-stu-id="e44dd-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="e44dd-114">次のコードは `IDateTime` インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="e44dd-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="e44dd-115">サービスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="e44dd-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="e44dd-116"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> の詳細については、[DI のサービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e44dd-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="e44dd-117">次のコードでは、一日の時刻に基づいてユーザーにあいさつが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e44dd-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="e44dd-118">アプリを実行すると、時刻に基づいてメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e44dd-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="e44dd-119">FromServices によるアクションの挿入</span><span class="sxs-lookup"><span data-stu-id="e44dd-119">Action injection with FromServices</span></span>

<span data-ttu-id="e44dd-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> を利用すると、コンストラクター挿入を利用しなくても、アクション メソッドがサービスに直接挿入されます。</span><span class="sxs-lookup"><span data-stu-id="e44dd-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="e44dd-121">コントローラーから設定にアクセスする</span><span class="sxs-lookup"><span data-stu-id="e44dd-121">Access settings from a controller</span></span>

<span data-ttu-id="e44dd-122">コントローラー内からアプリまたは構成の設定にアクセスするのが一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="e44dd-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="e44dd-123">*で説明されている*オプション パターン<xref:fundamentals/configuration/options>が設定の管理手法として推奨されています。</span><span class="sxs-lookup"><span data-stu-id="e44dd-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="e44dd-124">通常はコントローラーに <xref:Microsoft.Extensions.Configuration.IConfiguration> を直接挿入しないでください。</span><span class="sxs-lookup"><span data-stu-id="e44dd-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="e44dd-125">オプションを表すクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e44dd-125">Create a class that represents the options.</span></span> <span data-ttu-id="e44dd-126">例 :</span><span class="sxs-lookup"><span data-stu-id="e44dd-126">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="e44dd-127">サービス コレクションに構成クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="e44dd-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="e44dd-128">JSON 形式ファイルから設定を読み込むようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="e44dd-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="e44dd-129">次のコードはサービス コンテナーから `IOptions<SampleWebSettings>` 設定を要求し、それを `Index` メソッドで使用します。</span><span class="sxs-lookup"><span data-stu-id="e44dd-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="e44dd-130">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e44dd-130">Additional resources</span></span>

* <span data-ttu-id="e44dd-131">コントローラーで依存関係を明示的に要求することによってコードをテストしやすくする方法については、「<xref:mvc/controllers/testing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e44dd-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="e44dd-132">[既定の依存関係挿入コンテナーをサードパーティの実装に置換します](xref:fundamentals/dependency-injection#default-service-container-replacement)。</span><span class="sxs-lookup"><span data-stu-id="e44dd-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>
