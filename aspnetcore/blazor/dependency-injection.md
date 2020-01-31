---
title: ASP.NET Core Blazor 依存関係の挿入
author: guardrex
description: Blazor アプリがコンポーネントにサービスを挿入する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: fa6762522c831c7fbe2742dbfe4e25a377988e1e
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869565"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="edd1d-103">ASP.NET Core Blazor の依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="edd1d-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="edd1d-104">[Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="edd1d-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="edd1d-105">Blazor では、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="edd1d-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="edd1d-106">アプリは、組み込みのサービスをコンポーネントに挿入することによって使用できます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="edd1d-107">アプリでは、カスタムサービスを定義して登録し、DI を使用してアプリ全体で使用できるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="edd1d-108">DI は、中央の場所で構成されたサービスにアクセスするための手法です。</span><span class="sxs-lookup"><span data-stu-id="edd1d-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="edd1d-109">これは、Blazor アプリで次のような場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="edd1d-110">1つのサービスクラスのインスタンスを、*シングルトン*サービスと呼ばれる多数のコンポーネントで共有します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="edd1d-111">参照の抽象化を使用して、具象サービスクラスからコンポーネントを分離します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="edd1d-112">たとえば、アプリケーションのデータにアクセスするためのインターフェイス `IDataAccess` を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="edd1d-113">インターフェイスは具象 `DataAccess` クラスによって実装され、アプリのサービスコンテナーにサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="edd1d-114">コンポーネントが DI を使用して `IDataAccess` の実装を受け取ると、コンポーネントは具象型に結合されません。</span><span class="sxs-lookup"><span data-stu-id="edd1d-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="edd1d-115">の実装はスワップできます。たとえば、単体テストのモック実装では、</span><span class="sxs-lookup"><span data-stu-id="edd1d-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="edd1d-116">既定のサービス</span><span class="sxs-lookup"><span data-stu-id="edd1d-116">Default services</span></span>

<span data-ttu-id="edd1d-117">既定のサービスは、アプリのサービスコレクションに自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="edd1d-118">サービス</span><span class="sxs-lookup"><span data-stu-id="edd1d-118">Service</span></span> | <span data-ttu-id="edd1d-119">有効期間</span><span class="sxs-lookup"><span data-stu-id="edd1d-119">Lifetime</span></span> | <span data-ttu-id="edd1d-120">説明</span><span class="sxs-lookup"><span data-stu-id="edd1d-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="edd1d-121">シングルトン</span><span class="sxs-lookup"><span data-stu-id="edd1d-121">Singleton</span></span> | <span data-ttu-id="edd1d-122">URI によって識別されるリソースから HTTP 要求を送信し、HTTP 応答を受信するためのメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="edd1d-123">Blazor WebAssembly アプリの `HttpClient` インスタンスは、ブラウザーを使用してバックグラウンドで HTTP トラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="edd1d-124">Blazor サーバーアプリには、既定でサービスとして構成されている `HttpClient` は含まれません。</span><span class="sxs-lookup"><span data-stu-id="edd1d-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="edd1d-125">Blazor Server アプリに `HttpClient` を提供します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="edd1d-126">詳細については、「 <xref:blazor/call-web-api>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="edd1d-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="edd1d-127">シングルトン (Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="edd1d-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="edd1d-128">スコープ (Blazor サーバー)</span><span class="sxs-lookup"><span data-stu-id="edd1d-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="edd1d-129">JavaScript 呼び出しがディスパッチされる JavaScript ランタイムのインスタンスを表します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="edd1d-130">詳細については、「 <xref:blazor/javascript-interop>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="edd1d-130">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="edd1d-131">シングルトン (Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="edd1d-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="edd1d-132">スコープ (Blazor サーバー)</span><span class="sxs-lookup"><span data-stu-id="edd1d-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="edd1d-133">Uri とナビゲーション状態を操作するためのヘルパーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="edd1d-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="edd1d-134">詳細については、「 [URI およびナビゲーション状態ヘルパー](xref:blazor/routing#uri-and-navigation-state-helpers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="edd1d-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="edd1d-135">カスタムサービスプロバイダーは、表に示されている既定のサービスを自動的に提供しません。</span><span class="sxs-lookup"><span data-stu-id="edd1d-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="edd1d-136">カスタムサービスプロバイダーを使用し、表に示されているいずれかのサービスが必要な場合は、必要なサービスを新しいサービスプロバイダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="edd1d-137">アプリにサービスを追加する</span><span class="sxs-lookup"><span data-stu-id="edd1d-137">Add services to an app</span></span>

<span data-ttu-id="edd1d-138">新しいアプリを作成した後、`Startup.ConfigureServices` 方法を調べます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-138">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="edd1d-139">`ConfigureServices` メソッドには、サービス記述子オブジェクト (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) の一覧である <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>が渡されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-139">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="edd1d-140">サービスは、サービスコレクションにサービス記述子を提供することによって追加されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-140">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="edd1d-141">次の例は、`IDataAccess` インターフェイスと具象実装 `DataAccess`の概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="edd1d-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="edd1d-142">サービスは、次の表に示す有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-142">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="edd1d-143">有効期間</span><span class="sxs-lookup"><span data-stu-id="edd1d-143">Lifetime</span></span> | <span data-ttu-id="edd1d-144">説明</span><span class="sxs-lookup"><span data-stu-id="edd1d-144">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="edd1d-145"> WebAssembly には、現在、DI スコープという概念はありません。</span><span class="sxs-lookup"><span data-stu-id="edd1d-145"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="edd1d-146">`Scoped`登録されたサービスは `Singleton` サービスと同様に動作します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-146">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="edd1d-147">ただし、Blazor サーバーホスティングモデルでは、`Scoped` の有効期間がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="edd1d-147">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="edd1d-148">Blazor サーバーアプリでは、スコープが指定されたサービス登録のスコープは*接続*になります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-148">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="edd1d-149">このため、現在の目的がブラウザーでクライアント側を実行する場合でも、スコープ付きサービスを使用することは、現在のユーザーにスコープを設定する必要があるサービスに対して推奨されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-149">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="edd1d-150">DI は、サービスの*1 つのインスタンス*を作成します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-150">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="edd1d-151">`Singleton` サービスを必要とするすべてのコンポーネントは、同じサービスのインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-151">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="edd1d-152">コンポーネントは、サービスコンテナーから `Transient` サービスのインスタンスを取得するたびに、サービスの*新しいインスタンス*を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-152">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="edd1d-153">DI システムは ASP.NET Core の DI システムに基づいています。</span><span class="sxs-lookup"><span data-stu-id="edd1d-153">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="edd1d-154">詳細については、「 <xref:fundamentals/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="edd1d-154">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="edd1d-155">コンポーネントでサービスを要求する</span><span class="sxs-lookup"><span data-stu-id="edd1d-155">Request a service in a component</span></span>

<span data-ttu-id="edd1d-156">サービスがサービスコレクションに追加された後、 [\@を挿入](xref:mvc/views/razor#inject)する Razor ディレクティブを使用して、サービスをコンポーネントに挿入します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-156">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="edd1d-157">`@inject` には、次の2つのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-157">`@inject` has two parameters:</span></span>

* <span data-ttu-id="edd1d-158">挿入するサービスの型 &ndash; 入力します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-158">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="edd1d-159">プロパティ &ndash;、挿入された app service を受け取るプロパティの名前です。</span><span class="sxs-lookup"><span data-stu-id="edd1d-159">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="edd1d-160">プロパティは手動で作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="edd1d-160">The property doesn't require manual creation.</span></span> <span data-ttu-id="edd1d-161">コンパイラによってプロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-161">The compiler creates the property.</span></span>

<span data-ttu-id="edd1d-162">詳細については、「 <xref:mvc/views/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="edd1d-162">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="edd1d-163">複数の `@inject` ステートメントを使用して、さまざまなサービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-163">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="edd1d-164">次の例は、`@inject` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="edd1d-164">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="edd1d-165">`Services.IDataAccess` を実装するサービスは、コンポーネントのプロパティ `DataRepository`に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-165">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="edd1d-166">コードが `IDataAccess` 抽象化を使用するかどうかに注意してください。</span><span class="sxs-lookup"><span data-stu-id="edd1d-166">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="edd1d-167">内部的には、生成されたプロパティ (`DataRepository`) は、`InjectAttribute` 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-167">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="edd1d-168">通常、この属性は直接使用されません。</span><span class="sxs-lookup"><span data-stu-id="edd1d-168">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="edd1d-169">コンポーネントに基底クラスが必要であり、基底クラスにも挿入されたプロパティが必要な場合は、`InjectAttribute`を手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-169">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="edd1d-170">基底クラスから派生したコンポーネントでは、`@inject` ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="edd1d-170">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="edd1d-171">基底クラスの `InjectAttribute` で十分です。</span><span class="sxs-lookup"><span data-stu-id="edd1d-171">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="edd1d-172">サービスで DI を使用する</span><span class="sxs-lookup"><span data-stu-id="edd1d-172">Use DI in services</span></span>

<span data-ttu-id="edd1d-173">複雑なサービスでは、追加のサービスが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-173">Complex services might require additional services.</span></span> <span data-ttu-id="edd1d-174">前の例では、`DataAccess` に `HttpClient` 既定のサービスが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-174">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="edd1d-175">`@inject` (または `InjectAttribute`) は、サービスで使用できません。</span><span class="sxs-lookup"><span data-stu-id="edd1d-175">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="edd1d-176">代わりに*コンストラクターの挿入*を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-176">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="edd1d-177">必要なサービスは、サービスのコンストラクターにパラメーターを追加することによって追加されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-177">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="edd1d-178">DI は、サービスを作成するときに、必要なサービスをコンストラクターで認識し、それに応じてそれを提供します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-178">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="edd1d-179">コンストラクターインジェクションの前提条件:</span><span class="sxs-lookup"><span data-stu-id="edd1d-179">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="edd1d-180">すべての引数が DI によって満たされることができるコンストラクターが1つ存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-180">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="edd1d-181">DI でカバーされない追加のパラメーターは、既定値を指定した場合に許可されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-181">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="edd1d-182">該当するコンストラクターは*パブリック*である必要があります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-182">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="edd1d-183">1つの適用可能なコンストラクターが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-183">One applicable constructor must exist.</span></span> <span data-ttu-id="edd1d-184">あいまいさが発生した場合、DI は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="edd1d-184">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="edd1d-185">DI スコープを管理するためのユーティリティの基本コンポーネントクラス</span><span class="sxs-lookup"><span data-stu-id="edd1d-185">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="edd1d-186">ASP.NET Core アプリでは、スコープ付きサービスは通常、現在の要求にスコープが設定されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-186">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="edd1d-187">要求が完了すると、スコープまたは一時的なサービスが DI システムによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-187">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="edd1d-188">Blazor サーバーアプリでは、要求スコープはクライアント接続の間継続されるため、一時的でスコープのあるサービスが予想よりもはるかに長くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="edd1d-188">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="edd1d-189">サービスのスコープをコンポーネントの有効期間に限定するには、`OwningComponentBase` と `OwningComponentBase<TService>` 基底クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-189">To scope services to the lifetime of a component, you can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="edd1d-190">これらの基本クラスは、コンポーネントの有効期間にスコープが設定されているサービスを解決する `IServiceProvider` 型の `ScopedServices` プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-190">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="edd1d-191">Razor の基底クラスから継承するコンポーネントを作成するには、`@inherits` ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="edd1d-191">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

```razor
@page "/users"
@attribute [Authorize]
@inherits OwningComponentBase<Data.ApplicationDbContext>

<h1>Users (@Service.Users.Count())</h1>
<ul>
    @foreach (var user in Service.Users)
    {
        <li>@user.UserName</li>
    }
</ul>
```

> [!NOTE]
> <span data-ttu-id="edd1d-192">`@inject` または `InjectAttribute` を使用してコンポーネントに挿入されたサービスは、コンポーネントのスコープ内に作成されず、要求スコープに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="edd1d-192">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="edd1d-193">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="edd1d-193">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
