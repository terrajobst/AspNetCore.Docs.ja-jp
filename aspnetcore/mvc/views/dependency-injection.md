---
title: ASP.NET Core でのビューへの依存関係の挿入
author: ardalis
description: ASP.NET Core で MVC ビューへの依存関係の挿入をサポートする方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: mvc/views/dependency-injection
ms.openlocfilehash: 6241bb8e262f64e2e30721bc5fe6f8f1be84b60d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651476"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="8cc14-103">ASP.NET Core でのビューへの依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="8cc14-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="8cc14-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8cc14-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8cc14-105">ASP.NET Core では、ビューへの[依存関係の挿入](xref:fundamentals/dependency-injection)をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8cc14-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="8cc14-106">この機能は、ビュー要素を作成するためだけに必要なローカライズやデータなど、ビュー固有のサービスに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="8cc14-107">コントローラーとビューの間で[関心の分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)を保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8cc14-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="8cc14-108">ビューに表示されるデータのほとんどは、コントローラーから渡されます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="8cc14-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8cc14-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="8cc14-110">構成の挿入</span><span class="sxs-lookup"><span data-stu-id="8cc14-110">Configuration injection</span></span>

<span data-ttu-id="8cc14-111">*appsettings.json* 値は、ビューに直接挿入できます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-111">*appsettings.json* values can be injected directly into a view.</span></span>

<span data-ttu-id="8cc14-112">*appsettings.json* ファイルの例:</span><span class="sxs-lookup"><span data-stu-id="8cc14-112">Example of an *appsettings.json* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="8cc14-113">`@inject`: `@inject <type> <name>` の構文</span><span class="sxs-lookup"><span data-stu-id="8cc14-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="8cc14-114">`@inject` を使用した例:</span><span class="sxs-lookup"><span data-stu-id="8cc14-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="8cc14-115">サービスの挿入</span><span class="sxs-lookup"><span data-stu-id="8cc14-115">Service injection</span></span>

<span data-ttu-id="8cc14-116">`@inject` ディレクティブを使用して、サービスをビューに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="8cc14-117">`@inject` は、ビューにプロパティを追加したり、DI を使用してプロパティを設定したりするものと考えることができます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="8cc14-118">このビューでは、全体的な統計を示す概要と共に、`ToDoItem` インスタンスのリストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="8cc14-119">概要は、挿入された `StatisticsService` から作成されます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="8cc14-120">このサービスは、`ConfigureServices`Startup.cs*内の* の依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="8cc14-121">`StatisticsService` では、`ToDoItem` インスタンスのセットでいくつかの計算を実行します。これには、次のリポジトリを使用してアクセスします。</span><span class="sxs-lookup"><span data-stu-id="8cc14-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="8cc14-122">サンプルのリポジトリでは、メモリ内のコレクションを使用します。</span><span class="sxs-lookup"><span data-stu-id="8cc14-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="8cc14-123">上記の (メモリ内のすべてのデータを操作する) 実装は、大規模なリモートでアクセスされるデータ セットにはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="8cc14-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="8cc14-124">サンプルには、ビューとビューに挿入されたサービスにバインドされたモデルからのデータが表示されています。</span><span class="sxs-lookup"><span data-stu-id="8cc14-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![合計項目数、完了した項目数、優先度の平均、および優先度と完了を示すブール値を含むタスクのリストを一覧する To Do ビュー。](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="8cc14-126">ルックアップ データの作成</span><span class="sxs-lookup"><span data-stu-id="8cc14-126">Populating Lookup Data</span></span>

<span data-ttu-id="8cc14-127">ビューの挿入は、ドロップダウン リストなど、UI 要素のオプションの作成に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="8cc14-128">性別、状態、およびその他の基本設定を指定するオプションを含む、ユーザー プロファイル フォームを検討します。</span><span class="sxs-lookup"><span data-stu-id="8cc14-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="8cc14-129">標準の MVC アプローチを使用するフォームのようなレンダリングには、これらのオプションのセットごとにデータ アクセス サービスを要求し、バインドするオプションの各セットを含むモデルまたは `ViewBag` を作成するために、コントローラーが必要です。</span><span class="sxs-lookup"><span data-stu-id="8cc14-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="8cc14-130">別のアプローチでは、オプションを取得するために、ビューに直接サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="8cc14-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="8cc14-131">これにより、このビュー要素の構築ロジックをビュー自体に移動して、コントローラーから要求されるコードの量を最小限にします。</span><span class="sxs-lookup"><span data-stu-id="8cc14-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="8cc14-132">プロファイルの編集フォームを表示するコントローラー アクションは、フォームにプロファイル インスタンスを渡すためにのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="8cc14-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="8cc14-133">これらの基本設定を更新するために使用される HTML フォームには、3 つのプロパティのドロップダウン リストが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![名前、性別、状態、好きな色の入力を許可するフォームを含む、[Update Profile] (プロファイルの更新) ビュー。](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="8cc14-135">これらのリストは、ビューに挿入されたサービスによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="8cc14-136">`ProfileOptionsService` は、このフォームに必要なデータだけを提供する UI レベルのサービスです。</span><span class="sxs-lookup"><span data-stu-id="8cc14-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="8cc14-137">`Startup.ConfigureServices` での依存関係の挿入を介して要求する型を登録するのを忘れないでください。</span><span class="sxs-lookup"><span data-stu-id="8cc14-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8cc14-138">サービス プロバイダーは [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice) を介して内部的にクエリが実行されるため、未登録の型は実行時に例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="8cc14-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="8cc14-139">サービスのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="8cc14-139">Overriding Services</span></span>

<span data-ttu-id="8cc14-140">新しいサービスを挿入するだけでなく、この技術はページ上の以前に挿入されたサービスをオーバーライドするためにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="8cc14-141">以下の図は、最初の例で使用されたページで利用できるすべてのフィールドを示しています。</span><span class="sxs-lookup"><span data-stu-id="8cc14-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![@ 記号を入力して、Html、Component、StatsService、Url フィールドが一覧された IntelliSense コンテキスト メニュー](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="8cc14-143">ご覧のように、既定のフィールドには、`Html`、`Component`、`Url` (さらに、挿入した `StatsService`) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="8cc14-144">たとえば、既定の HTML ヘルパーを自分のヘルパーに置き換える場合は、`@inject` を使用して簡単に置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="8cc14-145">既存のサービスを拡張する場合、独自に既存の実装から継承したり、ラップしたりするときに、この技術を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8cc14-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="8cc14-146">参照</span><span class="sxs-lookup"><span data-stu-id="8cc14-146">See Also</span></span>

* <span data-ttu-id="8cc14-147">Simon Timms のブログ: [ルックアップ データをビューに取り込む](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="8cc14-147">Simon Timms Blog: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
