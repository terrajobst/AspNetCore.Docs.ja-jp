---
title: ASP.NET Core Blazor 依存関係の挿入
author: guardrex
description: Blazor アプリでコンポーネントにサービスを挿入する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78646694"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="ab0f3-103">ASP.NET Core Blazor 依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="ab0f3-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="ab0f3-104">作成者: [Rainer Stropek](https://www.timecockpit.com)、[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="ab0f3-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ab0f3-105">Blazor では、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ab0f3-106">アプリでは、組み込みサービスをコンポーネントに挿入することにより、サービスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="ab0f3-107">また、アプリでは、カスタム サービスを定義して登録し、DI によってアプリ全体でそれを使用できるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="ab0f3-108">DI は、中央の場所で構成されたサービスにアクセスするための手法です。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="ab0f3-109">Blazor アプリでは、これは次のような役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="ab0f3-110">サービス クラスの 1 つのインスタンスを、多数のコンポーネント間で共有します。これは、"*シングルトン*" サービスと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="ab0f3-111">参照の抽象化を使用することで、具象サービス クラスからコンポーネントを切り離します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="ab0f3-112">たとえば、アプリ内のデータにアクセスするためのインターフェイス `IDataAccess` について考えます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="ab0f3-113">このインターフェイスは、具象クラス `DataAccess` によって実装され、アプリのサービス コンテナーにサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="ab0f3-114">コンポーネントで DI を使用して `IDataAccess` の実装を受け取ると、コンポーネントは具象型に結合されません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="ab0f3-115">たとえば単体テストでのモック実装の場合など、実装をスワップすることができます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="ab0f3-116">既定のサービス</span><span class="sxs-lookup"><span data-stu-id="ab0f3-116">Default services</span></span>

<span data-ttu-id="ab0f3-117">既定のサービスは、アプリのサービス コレクションに自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="ab0f3-118">サービス</span><span class="sxs-lookup"><span data-stu-id="ab0f3-118">Service</span></span> | <span data-ttu-id="ab0f3-119">有効期間</span><span class="sxs-lookup"><span data-stu-id="ab0f3-119">Lifetime</span></span> | <span data-ttu-id="ab0f3-120">説明</span><span class="sxs-lookup"><span data-stu-id="ab0f3-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="ab0f3-121">シングルトン</span><span class="sxs-lookup"><span data-stu-id="ab0f3-121">Singleton</span></span> | <span data-ttu-id="ab0f3-122">URI によって識別されるリソースに HTTP 要求を送信し、そのリソースから HTTP 応答を受信するためのメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="ab0f3-123">Blazor WebAssembly アプリの `HttpClient` のインスタンスでは、バックグラウンドでの HTTP トラフィックの処理にブラウザーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="ab0f3-124">Blazor サーバー アプリには、既定でサービスとして構成される `HttpClient` は含まれません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="ab0f3-125">Blazor Server アプリに `HttpClient` を提供します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="ab0f3-126">詳細については、「<xref:blazor/call-web-api>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="ab0f3-127">シングルトン (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="ab0f3-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="ab0f3-128">スコープ (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="ab0f3-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="ab0f3-129">JavaScript の呼び出しがディスパッチされる JavaScript ランタイムのインスタンスを表します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="ab0f3-130">詳細については、「<xref:blazor/call-javascript-from-dotnet>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="ab0f3-131">シングルトン (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="ab0f3-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="ab0f3-132">スコープ (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="ab0f3-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="ab0f3-133">URI とナビゲーション状態を操作するためのヘルパーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="ab0f3-134">詳細については、「[URI およびナビゲーション状態ヘルパー](xref:blazor/routing#uri-and-navigation-state-helpers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="ab0f3-135">カスタム サービス プロバイダーでは、表に示されている既定のサービスは自動的に提供されません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="ab0f3-136">カスタム サービス プロバイダーを使用し、表に示されているいずれかのサービスが必要な場合は、必要なサービスを新しいサービス プロバイダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="ab0f3-137">サービスをアプリに追加する</span><span class="sxs-lookup"><span data-stu-id="ab0f3-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="ab0f3-138">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ab0f3-138">Blazor WebAssembly</span></span>

<span data-ttu-id="ab0f3-139">`Main`Program.cs*の* メソッドで、アプリのサービス コレクション用のサービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="ab0f3-140">次の例では、`MyDependency` の実装が `IMyDependency` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="ab0f3-141">ホストが構築されると、コンポーネントがレンダリングされる前に、ルート DI スコープからサービスにアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="ab0f3-142">これは、コンテンツをレンダリングする前に初期化ロジックを実行する場合に役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-142">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="ab0f3-143">また、ホストでは、アプリの中央構成インスタンスも提供されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="ab0f3-144">前の例を基にして、天気予報サービスの URL を、既定の構成ソース (*appsettings.json* など) から `InitializeWeatherAsync` に渡します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a><span data-ttu-id="ab0f3-145">Blazor サーバー</span><span class="sxs-lookup"><span data-stu-id="ab0f3-145">Blazor Server</span></span>

<span data-ttu-id="ab0f3-146">新しいアプリを作成した後、`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="ab0f3-147">`ConfigureServices` メソッドには、サービス記述子オブジェクト (<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>) のリストである <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> が渡されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="ab0f3-148">サービスは、サービス コレクションにサービス記述子を提供することによって追加されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="ab0f3-149">次の例では、`IDataAccess` インターフェイスとその具象実装 `DataAccess` での概念を示します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="ab0f3-150">サービスの有効期間</span><span class="sxs-lookup"><span data-stu-id="ab0f3-150">Service lifetime</span></span>

<span data-ttu-id="ab0f3-151">サービスは、次の表に示す有効期間で構成できます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="ab0f3-152">有効期間</span><span class="sxs-lookup"><span data-stu-id="ab0f3-152">Lifetime</span></span> | <span data-ttu-id="ab0f3-153">説明</span><span class="sxs-lookup"><span data-stu-id="ab0f3-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="ab0f3-154">現在、Blazor WebAssembly には DI スコープの概念はありません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="ab0f3-155">`Scoped` 登録済みサービスは `Singleton` サービスのように動作します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="ab0f3-156">ただし、Blazor サーバー ホスティング モデルでは、`Scoped` 有効期間がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="ab0f3-157">Blazor サーバー アプリでは、スコープ サービスの登録は "*接続*" にスコープされます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="ab0f3-158">このため、現在の目的がブラウザーでクライアント側を実行する場合でも、現在のユーザーにスコープする必要があるサービスの場合は、スコープ サービスを使用することが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="ab0f3-159">DI では、サービスの "*単一インスタンス*" が作成されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="ab0f3-160">`Singleton` サービスを必要とするすべてのコンポーネントは、同じサービスのインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="ab0f3-161">コンポーネントは、サービス コンテナーから `Transient` サービスのインスタンスを取得するたびに、サービスの "*新しいインスタンス*" を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="ab0f3-162">DI システムは、ASP.NET Core の DI システムが基になっています。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="ab0f3-163">詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="ab0f3-164">コンポーネント内のサービスを要求する</span><span class="sxs-lookup"><span data-stu-id="ab0f3-164">Request a service in a component</span></span>

<span data-ttu-id="ab0f3-165">サービスがサービス コレクションに追加された後、[\@inject](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、サービスをコンポーネントに挿入します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="ab0f3-166">`@inject` には、2 つのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="ab0f3-167">型 &ndash; 挿入するサービスの型。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="ab0f3-168">プロパティ &ndash; 挿入されたアプリ サービスを受け取るプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="ab0f3-169">プロパティを手動で作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="ab0f3-170">プロパティはコンパイラによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-170">The compiler creates the property.</span></span>

<span data-ttu-id="ab0f3-171">詳細については、「<xref:mvc/views/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="ab0f3-172">異なるサービスを挿入するには、複数の `@inject` ステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="ab0f3-173">次の例は、`@inject` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="ab0f3-174">`Services.IDataAccess` を実装するサービスを、コンポーネントのプロパティ `DataRepository` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="ab0f3-175">コードによって `IDataAccess` 抽象化だけが使用されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="ab0f3-176">内部的には、生成されたプロパティ (`DataRepository`) によって、`InjectAttribute` 属性が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="ab0f3-177">通常、この属性を直接使用することはありません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="ab0f3-178">コンポーネントで基底クラスが必要であり、基底クラスで挿入されたプロパティも必要な場合は、`InjectAttribute` を手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="ab0f3-179">基底クラスから派生されたコンポーネントでは、`@inject` ディレクティブは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="ab0f3-180">基底クラスの `InjectAttribute` で十分です。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="ab0f3-181">サービスで DI を使用する</span><span class="sxs-lookup"><span data-stu-id="ab0f3-181">Use DI in services</span></span>

<span data-ttu-id="ab0f3-182">複雑なサービスでは、追加のサービスが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-182">Complex services might require additional services.</span></span> <span data-ttu-id="ab0f3-183">前の例では、`DataAccess` で `HttpClient` の既定のサービスが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="ab0f3-184">`@inject` (または `InjectAttribute`) は、サービスでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="ab0f3-185">代わりに、"*コンストラクター挿入*" を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="ab0f3-186">サービスのコンストラクターにパラメーターを追加することによって、必要なサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="ab0f3-187">DI では、サービスを作成するときに、コンストラクターで必要なサービスが認識され、それに応じてサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="ab0f3-188">コンストラクター挿入の前提条件:</span><span class="sxs-lookup"><span data-stu-id="ab0f3-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="ab0f3-189">DI によってすべての引数を満たすことができるコンストラクターが 1 つ存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="ab0f3-190">DI で満たすことができない追加のパラメーターは、既定値が指定されている場合に許可されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="ab0f3-191">該当するコンストラクターは、*public* である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="ab0f3-192">該当するコンストラクターが 1 つ存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-192">One applicable constructor must exist.</span></span> <span data-ttu-id="ab0f3-193">あいまいさがある場合は、DI で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="ab0f3-194">DI スコープを管理するためのユーティリティの基本コンポーネント クラス</span><span class="sxs-lookup"><span data-stu-id="ab0f3-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="ab0f3-195">ASP.NET Core アプリでは、スコープ サービスは通常、現在の要求にスコープされます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="ab0f3-196">要求が完了すると、スコープ サービスまたは一時サービスは DI システムによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="ab0f3-197">Blazor サーバー アプリでは、要求スコープはクライアント接続の期間を通して保持されるため、一時サービスとスコープ サービスが予想よりはるかに長く存続する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="ab0f3-198">Blazor WebAssembly アプリでは、スコープ付きの有効期間で登録されたサービスはシングルトンとして扱われるため、通常の ASP.NET Core アプリのスコープ サービスより長く存続します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="ab0f3-199">Blazor アプリでサービスの有効期間を制限するには、`OwningComponentBase` 型を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="ab0f3-200">`OwningComponentBase` は `ComponentBase` から派生された抽象型であり、コンポーネントの有効期間に対応する DI スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="ab0f3-201">このスコープを使用すると、スコープ付きの有効期間で DI サービスを使用し、コンポーネントと同じ期間だけ持続させることができます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="ab0f3-202">コンポーネントが破棄されると、コンポーネントのスコープ サービス プロバイダーからのサービスも破棄されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="ab0f3-203">これは、次のようなサービスに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-203">This can be useful for services that:</span></span>

* <span data-ttu-id="ab0f3-204">一時的な有効期間が不適切であるため、コンポーネント内で再利用する必要がある。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="ab0f3-205">シングルトンの有効期間が不適切であるため、コンポーネント間で共有してはならない。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="ab0f3-206">`OwningComponentBase` 型には、使用できるバージョンが 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="ab0f3-207">`OwningComponentBase` は、`ComponentBase` 型の抽象的で破棄可能な子であり、`ScopedServices`型の保護された `IServiceProvider` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="ab0f3-208">このプロバイダーを使用すると、コンポーネントの有効期間にスコープが設定されているサービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="ab0f3-209">`@inject` または `InjectAttribute` (`[Inject]`) を使用してコンポーネントに挿入された DI サービスは、コンポーネントのスコープでは作成されません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="ab0f3-210">コンポーネントのスコープを使用するには、`ScopedServices.GetRequiredService` または `ScopedServices.GetService` を使用してサービスを解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="ab0f3-211">`ScopedServices` プロバイダーを使用して解決されたすべてのサービスには、同じスコープから提供される依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="ab0f3-212">`OwningComponentBase<T>` から派生する `OwningComponentBase` では、スコープ DI プロバイダーから `Service` のインスタンスを返すプロパティ `T` が追加されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="ab0f3-213">この型は、アプリで 1 つのプライマリ サービスをコンポーネントのスコープを使用して DI コンテナーに要求するときに、`IServiceProvider` のインスタンスを使用せずにスコープ サービスにアクセスするための便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="ab0f3-214">`ScopedServices` プロパティを使用できるので、必要に応じて、アプリで他の型のサービスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="ab0f3-215">DI からの Entity Framework の DbContext の使用</span><span class="sxs-lookup"><span data-stu-id="ab0f3-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="ab0f3-216">Web アプリで DI から取得する一般的なサービスの型の 1 つは、Entity Framework (EF) の `DbContext` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="ab0f3-217">`IServiceCollection.AddDbContext` を使用して EF サービスを登録すると、既定ではスコープ サービスとして `DbContext` が追加されます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="ab0f3-218">スコープ サービスとして登録すると、Blazor のインスタンスの有効期間が長くなり、アプリ全体で共有されるため、`DbContext` アプリで問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="ab0f3-219">`DbContext` はスレッドセーフではなく、同時に使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="ab0f3-220">アプリによっては、`OwningComponentBase` を使用して `DbContext` のスコープを 1 つのコンポーネントに限定することで、問題が解決する "*場合があります*"。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="ab0f3-221">コンポーネントで `DbContext` が並列に使用されていない場合は、`OwningComponentBase` からコンポーネントを派生させ、`DbContext` から `ScopedServices` を取得すれば、次のことが保証されるため、他には何も必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="ab0f3-222">個別のコンポーネントで `DbContext` が共有されません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="ab0f3-223">`DbContext` は、それに依存するコンポーネントと同じ期間だけ存在します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="ab0f3-224">1 つのコンポーネントで `DbContext` が同時に使用される可能性がある場合は (たとえば、ユーザーがボタンを選択するたび)、`OwningComponentBase` を使用しても、EF の同時操作に関する問題を回避することはできません。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="ab0f3-225">その場合は、論理 EF 操作ごとに別の `DbContext` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="ab0f3-226">次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="ab0f3-227">引数として `DbContext` を使用して、`DbContextOptions<TContext>` を直接作成します。これは DI から取得でき、スレッドセーフです。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="ab0f3-228">一時的な有効期間を使用して、サービス コンテナーに `DbContext` を登録します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="ab0f3-229">コンテキストを登録するときに、`ServiceLifetime.Transient` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="ab0f3-230">`AddDbContext` 拡張メソッドは、`ServiceLifetime` 型の 2 つの省略可能なパラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="ab0f3-231">この方法を使用するには、`contextLifetime` パラメーターだけを `ServiceLifetime.Transient` にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="ab0f3-232">`optionsLifetime` は、既定値である `ServiceLifetime.Scoped` のままにできます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="ab0f3-233">一時的な `DbContext` は、複数の EF 操作を並列に実行しないコンポーネントに、普通に (`@inject` を使用して) 挿入できます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="ab0f3-234">複数の EF 操作を同時に実行する可能性がある場合は、`DbContext` を使用して、並列操作ごとに個別の `IServiceProvider.GetRequiredService` オブジェクトを要求できます。</span><span class="sxs-lookup"><span data-stu-id="ab0f3-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="ab0f3-235">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ab0f3-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
