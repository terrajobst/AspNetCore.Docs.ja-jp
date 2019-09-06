---
title: ASP.NET Core Blazor の依存関係の挿入
author: guardrex
description: Blazor アプリがコンポーネントにサービスを挿入する方法を確認します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/dependency-injection
ms.openlocfilehash: a2bfa0cbe951e817ed6264f1a151d5a716cd795c
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310352"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core Blazor の依存関係の挿入

[Rainer Stropek](https://www.timecockpit.com)

Blazor では、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)がサポートされています。 アプリは、組み込みのサービスをコンポーネントに挿入することによって使用できます。 アプリでは、カスタムサービスを定義して登録し、DI を使用してアプリ全体で使用できるようにすることもできます。

DI は、中央の場所で構成されたサービスにアクセスするための手法です。 これは、Blazor アプリで次のような場合に役立ちます。

* 1つのサービスクラスのインスタンスを、*シングルトン*サービスと呼ばれる多数のコンポーネントで共有します。
* 参照の抽象化を使用して、具象サービスクラスからコンポーネントを分離します。 たとえば、アプリケーションのデータに`IDataAccess`アクセスするためのインターフェイスについて考えてみます。 インターフェイスは具象`DataAccess`クラスによって実装され、アプリのサービスコンテナーにサービスとして登録されます。 コンポーネントが DI を使用して`IDataAccess`実装を受け取ると、コンポーネントは具象型に結合されません。 の実装はスワップできます。たとえば、単体テストのモック実装では、

## <a name="default-services"></a>既定のサービス

既定のサービスは、アプリのサービスコレクションに自動的に追加されます。

| サービス | 有効期間 | 説明 |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | シングルトン | URI によって識別されるリソースから HTTP 要求を送信し、HTTP 応答を受信するためのメソッドを提供します。 の`HttpClient`このインスタンスは、ブラウザーを使用して、バックグラウンドで HTTP トラフィックを処理することに注意してください。 [BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress)は、アプリのベース URI プレフィックスに自動的に設定されます。 詳細については、「 <xref:blazor/call-web-api> 」を参照してください。 |
| `IJSRuntime` | シングルトン | JavaScript 呼び出しがディスパッチされる JavaScript ランタイムのインスタンスを表します。 詳細については、「 <xref:blazor/javascript-interop> 」を参照してください。 |
| `NavigationManager` | シングルトン | Uri とナビゲーション状態を操作するためのヘルパーが含まれています。 詳細については、「 [URI およびナビゲーション状態ヘルパー](xref:blazor/routing#uri-and-navigation-state-helpers)」を参照してください。 |

カスタムサービスプロバイダーは、表に示されている既定のサービスを自動的に提供しません。 カスタムサービスプロバイダーを使用し、表に示されているいずれかのサービスが必要な場合は、必要なサービスを新しいサービスプロバイダーに追加します。

## <a name="add-services-to-an-app"></a>アプリにサービスを追加する

新しいアプリを作成した後、 `Startup.ConfigureServices`メソッドを調べます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

メソッド`ConfigureServices`には<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>、サービス記述子オブジェクト (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) のリストであるが渡されます。 サービスは、サービスコレクションにサービス記述子を提供することによって追加されます。 次の例では、 `IDataAccess`インターフェイスとその具象実装`DataAccess`の概念を示します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

サービスは、次の表に示す有効期間で構成できます。

| 有効期間 | 説明 |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor クライアント側には、現在、DI スコープという概念はありません。 `Scoped`-登録済みサービスは`Singleton`サービスのように動作します。 ただし、サーバー側ホスティングモデルでは、有効`Scoped`期間がサポートされています。 Razor コンポーネントでは、スコープ付きサービス登録のスコープは接続に設定されます。 このため、現在の目的がブラウザーでクライアント側を実行する場合でも、スコープ付きサービスを使用することは、現在のユーザーにスコープを設定する必要があるサービスに対して推奨されます。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI は、サービスの*1 つのインスタンス*を作成します。 サービスを必要と`Singleton`するすべてのコンポーネントは、同じサービスのインスタンスを受け取ります。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | コンポーネントは、サービスコンテナーから`Transient`サービスのインスタンスを取得するたびに、サービスの*新しいインスタンス*を受け取ります。 |

DI システムは ASP.NET Core の DI システムに基づいています。 詳細については、「 <xref:fundamentals/dependency-injection> 」を参照してください。

## <a name="request-a-service-in-a-component"></a>コンポーネントでサービスを要求する

サービスがサービスコレクションに追加された後、 [ \@挿入](xref:mvc/views/razor#inject)Razor ディレクティブを使用して、サービスをコンポーネントに挿入します。 `@inject`には次の2つのパラメーターがあります。

* 挿入&ndash;するサービスの種類を入力します。
* プロパティ&ndash;には、挿入された app service を受け取るプロパティの名前を指定します。 プロパティは手動で作成する必要はありません。 コンパイラによってプロパティが作成されます。

詳細については、「 <xref:mvc/views/dependency-injection> 」を参照してください。

複数`@inject`のステートメントを使用して、さまざまなサービスを挿入します。

次の例は、`@inject` を使用する方法を示しています。 を実装`Services.IDataAccess`するサービスは、コンポーネントのプロパティ`DataRepository`に挿入されます。 コードが`IDataAccess`抽象化を使用するかどうかに注意してください。

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

内部的には、生成`DataRepository`されたプロパティ ( `InjectAttribute` ) は属性で修飾されます。 通常、この属性は直接使用されません。 コンポーネントに基本クラスが必要であり、基底クラスにも挿入されたプロパティが必要な場合`InjectAttribute`は、を手動で追加します。

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

基底クラスから派生したコンポーネントでは`@inject` 、ディレクティブは必要ありません。 基底`InjectAttribute`クラスのは十分です。

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>サービスで DI を使用する

複雑なサービスでは、追加のサービスが必要になる場合があります。 前の例では`DataAccess` 、は既定`HttpClient`のサービスを必要とする場合があります。 `@inject`サービスでは`InjectAttribute`、(または) を使用できません。 代わりに*コンストラクターの挿入*を使用する必要があります。 必要なサービスは、サービスのコンストラクターにパラメーターを追加することによって追加されます。 DI は、サービスを作成するときに、必要なサービスをコンストラクターで認識し、それに応じてそれを提供します。

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

コンストラクターインジェクションの前提条件:

* すべての引数が DI によって満たされることができるコンストラクターが1つ存在する必要があります。 DI でカバーされない追加のパラメーターは、既定値を指定した場合に許可されます。
* 該当するコンストラクターは*パブリック*である必要があります。
* 1つの適用可能なコンストラクターが存在する必要があります。 あいまいさが発生した場合、DI は例外をスローします。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
