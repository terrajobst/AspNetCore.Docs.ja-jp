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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646694"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core Blazor 依存関係の挿入

作成者: [Rainer Stropek](https://www.timecockpit.com)、[Mike Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor では、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) がサポートされています。 アプリでは、組み込みサービスをコンポーネントに挿入することにより、サービスを使用できます。 また、アプリでは、カスタム サービスを定義して登録し、DI によってアプリ全体でそれを使用できるようにすることもできます。

DI は、中央の場所で構成されたサービスにアクセスするための手法です。 Blazor アプリでは、これは次のような役に立ちます。

* サービス クラスの 1 つのインスタンスを、多数のコンポーネント間で共有します。これは、"*シングルトン*" サービスと呼ばれます。
* 参照の抽象化を使用することで、具象サービス クラスからコンポーネントを切り離します。 たとえば、アプリ内のデータにアクセスするためのインターフェイス `IDataAccess` について考えます。 このインターフェイスは、具象クラス `DataAccess` によって実装され、アプリのサービス コンテナーにサービスとして登録されます。 コンポーネントで DI を使用して `IDataAccess` の実装を受け取ると、コンポーネントは具象型に結合されません。 たとえば単体テストでのモック実装の場合など、実装をスワップすることができます。

## <a name="default-services"></a>既定のサービス

既定のサービスは、アプリのサービス コレクションに自動的に追加されます。

| サービス | 有効期間 | 説明 |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | シングルトン | URI によって識別されるリソースに HTTP 要求を送信し、そのリソースから HTTP 応答を受信するためのメソッドが提供されます。<br><br>Blazor WebAssembly アプリの `HttpClient` のインスタンスでは、バックグラウンドでの HTTP トラフィックの処理にブラウザーが使用されます。<br><br>Blazor サーバー アプリには、既定でサービスとして構成される `HttpClient` は含まれません。 Blazor Server アプリに `HttpClient` を提供します。<br><br>詳細については、「<xref:blazor/call-web-api>」を参照してください。 |
| `IJSRuntime` | シングルトン (Blazor WebAssembly)<br>スコープ (Blazor Server) | JavaScript の呼び出しがディスパッチされる JavaScript ランタイムのインスタンスを表します。 詳細については、「<xref:blazor/call-javascript-from-dotnet>」を参照してください。 |
| `NavigationManager` | シングルトン (Blazor WebAssembly)<br>スコープ (Blazor Server) | URI とナビゲーション状態を操作するためのヘルパーが含まれます。 詳細については、「[URI およびナビゲーション状態ヘルパー](xref:blazor/routing#uri-and-navigation-state-helpers)」を参照してください。 |

カスタム サービス プロバイダーでは、表に示されている既定のサービスは自動的に提供されません。 カスタム サービス プロバイダーを使用し、表に示されているいずれかのサービスが必要な場合は、必要なサービスを新しいサービス プロバイダーに追加します。

## <a name="add-services-to-an-app"></a>サービスをアプリに追加する

### <a name="blazor-webassembly"></a>Blazor WebAssembly

*Program.cs* の `Main` メソッドで、アプリのサービス コレクション用のサービスを構成します。 次の例では、`MyDependency` の実装が `IMyDependency` に登録されます。

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

ホストが構築されると、コンポーネントがレンダリングされる前に、ルート DI スコープからサービスにアクセスできるようになります。 これは、コンテンツをレンダリングする前に初期化ロジックを実行する場合に役に立ちます。

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

また、ホストでは、アプリの中央構成インスタンスも提供されます。 前の例を基にして、天気予報サービスの URL を、既定の構成ソース (*appsettings.json* など) から `InitializeWeatherAsync` に渡します。

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

### <a name="blazor-server"></a>Blazor サーバー

新しいアプリを作成した後、`Startup.ConfigureServices` メソッドを調べます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

`ConfigureServices` メソッドには、サービス記述子オブジェクト (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) のリストである <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> が渡されます。 サービスは、サービス コレクションにサービス記述子を提供することによって追加されます。 次の例では、`IDataAccess` インターフェイスとその具象実装 `DataAccess` での概念を示します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>サービスの有効期間

サービスは、次の表に示す有効期間で構成できます。

| 有効期間 | 説明 |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | 現在、Blazor WebAssembly には DI スコープの概念はありません。 `Scoped` 登録済みサービスは `Singleton` サービスのように動作します。 ただし、Blazor サーバー ホスティング モデルでは、`Scoped` 有効期間がサポートされています。 Blazor サーバー アプリでは、スコープ サービスの登録は "*接続*" にスコープされます。 このため、現在の目的がブラウザーでクライアント側を実行する場合でも、現在のユーザーにスコープする必要があるサービスの場合は、スコープ サービスを使用することが推奨されます。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI では、サービスの "*単一インスタンス*" が作成されます。 `Singleton` サービスを必要とするすべてのコンポーネントは、同じサービスのインスタンスを受け取ります。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | コンポーネントは、サービス コンテナーから `Transient` サービスのインスタンスを取得するたびに、サービスの "*新しいインスタンス*" を受け取ります。 |

DI システムは、ASP.NET Core の DI システムが基になっています。 詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。

## <a name="request-a-service-in-a-component"></a>コンポーネント内のサービスを要求する

サービスがサービス コレクションに追加された後、[\@inject](xref:mvc/views/razor#inject) Razor ディレクティブを使用して、サービスをコンポーネントに挿入します。 `@inject` には、2 つのパラメーターがあります。

* 型 &ndash; 挿入するサービスの型。
* プロパティ &ndash; 挿入されたアプリ サービスを受け取るプロパティの名前。 プロパティを手動で作成する必要はありません。 プロパティはコンパイラによって作成されます。

詳細については、「<xref:mvc/views/dependency-injection>」を参照してください。

異なるサービスを挿入するには、複数の `@inject` ステートメントを使用します。

次の例は、`@inject` を使用する方法を示しています。 `Services.IDataAccess` を実装するサービスを、コンポーネントのプロパティ `DataRepository` に挿入します。 コードによって `IDataAccess` 抽象化だけが使用されていることに注意してください。

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

内部的には、生成されたプロパティ (`DataRepository`) によって、`InjectAttribute` 属性が使用されます。 通常、この属性を直接使用することはありません。 コンポーネントで基底クラスが必要であり、基底クラスで挿入されたプロパティも必要な場合は、`InjectAttribute` を手動で追加します。

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

基底クラスから派生されたコンポーネントでは、`@inject` ディレクティブは必要ありません。 基底クラスの `InjectAttribute` で十分です。

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>サービスで DI を使用する

複雑なサービスでは、追加のサービスが必要になる場合があります。 前の例では、`DataAccess` で `HttpClient` の既定のサービスが必要になる場合があります。 `@inject` (または `InjectAttribute`) は、サービスでは使用できません。 代わりに、"*コンストラクター挿入*" を使用する必要があります。 サービスのコンストラクターにパラメーターを追加することによって、必要なサービスが追加されます。 DI では、サービスを作成するときに、コンストラクターで必要なサービスが認識され、それに応じてサービスが提供されます。

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

コンストラクター挿入の前提条件:

* DI によってすべての引数を満たすことができるコンストラクターが 1 つ存在する必要があります。 DI で満たすことができない追加のパラメーターは、既定値が指定されている場合に許可されます。
* 該当するコンストラクターは、*public* である必要があります。
* 該当するコンストラクターが 1 つ存在する必要があります。 あいまいさがある場合は、DI で例外がスローされます。

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>DI スコープを管理するためのユーティリティの基本コンポーネント クラス

ASP.NET Core アプリでは、スコープ サービスは通常、現在の要求にスコープされます。 要求が完了すると、スコープ サービスまたは一時サービスは DI システムによって破棄されます。 Blazor サーバー アプリでは、要求スコープはクライアント接続の期間を通して保持されるため、一時サービスとスコープ サービスが予想よりはるかに長く存続する可能性があります。 Blazor WebAssembly アプリでは、スコープ付きの有効期間で登録されたサービスはシングルトンとして扱われるため、通常の ASP.NET Core アプリのスコープ サービスより長く存続します。

Blazor アプリでサービスの有効期間を制限するには、`OwningComponentBase` 型を使用します。 `OwningComponentBase` は `ComponentBase` から派生された抽象型であり、コンポーネントの有効期間に対応する DI スコープを作成します。 このスコープを使用すると、スコープ付きの有効期間で DI サービスを使用し、コンポーネントと同じ期間だけ持続させることができます。 コンポーネントが破棄されると、コンポーネントのスコープ サービス プロバイダーからのサービスも破棄されます。 これは、次のようなサービスに役立ちます。

* 一時的な有効期間が不適切であるため、コンポーネント内で再利用する必要がある。
* シングルトンの有効期間が不適切であるため、コンポーネント間で共有してはならない。

`OwningComponentBase` 型には、使用できるバージョンが 2 つあります。

* `OwningComponentBase` は、`ComponentBase` 型の抽象的で破棄可能な子であり、`IServiceProvider`型の保護された `ScopedServices` プロパティがあります。 このプロバイダーを使用すると、コンポーネントの有効期間にスコープが設定されているサービスを解決できます。

  `@inject` または `InjectAttribute` (`[Inject]`) を使用してコンポーネントに挿入された DI サービスは、コンポーネントのスコープでは作成されません。 コンポーネントのスコープを使用するには、`ScopedServices.GetRequiredService` または `ScopedServices.GetService` を使用してサービスを解決する必要があります。 `ScopedServices` プロバイダーを使用して解決されたすべてのサービスには、同じスコープから提供される依存関係があります。

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

* `OwningComponentBase` から派生する `OwningComponentBase<T>` では、スコープ DI プロバイダーから `T` のインスタンスを返すプロパティ `Service` が追加されます。 この型は、アプリで 1 つのプライマリ サービスをコンポーネントのスコープを使用して DI コンテナーに要求するときに、`IServiceProvider` のインスタンスを使用せずにスコープ サービスにアクセスするための便利な方法です。 `ScopedServices` プロパティを使用できるので、必要に応じて、アプリで他の型のサービスを取得できます。

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>DI からの Entity Framework の DbContext の使用

Web アプリで DI から取得する一般的なサービスの型の 1 つは、Entity Framework (EF) の `DbContext` オブジェクトです。 `IServiceCollection.AddDbContext` を使用して EF サービスを登録すると、既定ではスコープ サービスとして `DbContext` が追加されます。 スコープ サービスとして登録すると、`DbContext` のインスタンスの有効期間が長くなり、アプリ全体で共有されるため、Blazor アプリで問題が発生する可能性があります。 `DbContext` はスレッドセーフではなく、同時に使用することはできません。

アプリによっては、`OwningComponentBase` を使用して `DbContext` のスコープを 1 つのコンポーネントに限定することで、問題が解決する "*場合があります*"。 コンポーネントで `DbContext` が並列に使用されていない場合は、`OwningComponentBase` からコンポーネントを派生させ、`ScopedServices` から `DbContext` を取得すれば、次のことが保証されるため、他には何も必要ありません。

* 個別のコンポーネントで `DbContext` が共有されません。
* `DbContext` は、それに依存するコンポーネントと同じ期間だけ存在します。

1 つのコンポーネントで `DbContext` が同時に使用される可能性がある場合は (たとえば、ユーザーがボタンを選択するたび)、`OwningComponentBase` を使用しても、EF の同時操作に関する問題を回避することはできません。 その場合は、論理 EF 操作ごとに別の `DbContext` を使用します。 次のいずれかの方法を使用します。

* 引数として `DbContextOptions<TContext>` を使用して、`DbContext` を直接作成します。これは DI から取得でき、スレッドセーフです。

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

* 一時的な有効期間を使用して、サービス コンテナーに `DbContext` を登録します。
  * コンテキストを登録するときに、`ServiceLifetime.Transient` を使用します。 `AddDbContext` 拡張メソッドは、`ServiceLifetime` 型の 2 つの省略可能なパラメーターを受け取ります。 この方法を使用するには、`contextLifetime` パラメーターだけを `ServiceLifetime.Transient` にする必要があります。 `optionsLifetime` は、既定値である `ServiceLifetime.Scoped` のままにできます。

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * 一時的な `DbContext` は、複数の EF 操作を並列に実行しないコンポーネントに、普通に (`@inject` を使用して) 挿入できます。 複数の EF 操作を同時に実行する可能性がある場合は、`IServiceProvider.GetRequiredService` を使用して、並列操作ごとに個別の `DbContext` オブジェクトを要求できます。

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

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
