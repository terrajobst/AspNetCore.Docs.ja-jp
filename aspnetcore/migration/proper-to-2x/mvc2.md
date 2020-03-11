---
title: ASP.NET から ASP.NET Core 2.0 への移行
author: isaac2004
description: 既存の ASP.NET MVC または Web API アプリケーションを ASP.NET Core 2.0 に移行するためのガイダンスを受信します。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
uid: migration/mvc2
ms.openlocfilehash: 11bd3b948afaedc675ac4249099969382683f653
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653972"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="7bdca-103">ASP.NET から ASP.NET Core 2.0 への移行</span><span class="sxs-lookup"><span data-stu-id="7bdca-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="7bdca-104">著者: [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="7bdca-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="7bdca-105">この記事は、ASP.NET アプリケーションを ASP.NET Core 2.0 に移行するための参考ガイドです。</span><span class="sxs-lookup"><span data-stu-id="7bdca-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7bdca-106">前提条件</span><span class="sxs-lookup"><span data-stu-id="7bdca-106">Prerequisites</span></span>

<span data-ttu-id="7bdca-107">.Net のダウンロードから、次の**いずれか**をインストールし[ます。 Windows](https://www.microsoft.com/net/download/windows):</span><span class="sxs-lookup"><span data-stu-id="7bdca-107">Install **one** of the following from [.NET Downloads: Windows](https://www.microsoft.com/net/download/windows):</span></span>

* <span data-ttu-id="7bdca-108">.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="7bdca-108">.NET Core SDK</span></span>
* <span data-ttu-id="7bdca-109">Windows 用 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7bdca-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="7bdca-110">**ASP.NET および Web の開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="7bdca-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="7bdca-111">**.NET Core クロスプラットフォームの開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="7bdca-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="7bdca-112">ターゲット フレームワーク</span><span class="sxs-lookup"><span data-stu-id="7bdca-112">Target frameworks</span></span>

<span data-ttu-id="7bdca-113">ASP.NET Core 2.0 プロジェクトを使うと、開発者は、.NET Core と .NET Framework のどちらか一方または両方を対象にして柔軟に開発できます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="7bdca-114">最も適切なターゲット フレームワークの決定については、「[サーバー アプリ用 .NET Core と .NET Framework の選択](/dotnet/standard/choosing-core-framework-server)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7bdca-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="7bdca-115">.NET Framework を対象にする場合は、プロジェクトで個々の NuGet パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7bdca-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="7bdca-116">.NET Core を対象にすると、ASP.NET Core 2.0 [メタパッケージ](xref:fundamentals/metapackage)のおかげで、さまざまな明示的パッケージ参照をしなくて済みます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="7bdca-117">`Microsoft.AspNetCore.All` メタパッケージをプロジェクトにインストールします。</span><span class="sxs-lookup"><span data-stu-id="7bdca-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="7bdca-118">メタパッケージを使うと、メタパッケージ内で参照されているパッケージはアプリでは展開されません。</span><span class="sxs-lookup"><span data-stu-id="7bdca-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="7bdca-119">.NET Core ランタイム ストアにはこれらのアセットが含まれており、パフォーマンス向上のためにプリコンパイルされています。</span><span class="sxs-lookup"><span data-stu-id="7bdca-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="7bdca-120">詳細については、「<xref:fundamentals/metapackage>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7bdca-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="7bdca-121">プロジェクトの構造の違い</span><span class="sxs-lookup"><span data-stu-id="7bdca-121">Project structure differences</span></span>

<span data-ttu-id="7bdca-122">*.csproj* ファイルの形式は、ASP.NET Core では簡素化されています。</span><span class="sxs-lookup"><span data-stu-id="7bdca-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="7bdca-123">いくつかの重要な変更は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7bdca-123">Some notable changes include:</span></span>

* <span data-ttu-id="7bdca-124">ファイルがプロジェクトの一部と見なされるためにファイルを明示的に含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7bdca-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="7bdca-125">これにより、大規模なチームで作業する場合に XML のマージが競合するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="7bdca-126">他のプロジェクトを GUID で参照することはなくなり、ファイルの読みやすさが向上します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="7bdca-127">Visual Studio でアンロードせずにファイルを編集することができます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Visual Studio 2017 の CSPROJ の編集コンテキスト メニュー オプション](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="7bdca-129">Global.asax ファイルの置換</span><span class="sxs-lookup"><span data-stu-id="7bdca-129">Global.asax file replacement</span></span>

<span data-ttu-id="7bdca-130">ASP.NET Core では、アプリをブートストラップする新しいメカニズムが導入されました。</span><span class="sxs-lookup"><span data-stu-id="7bdca-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="7bdca-131">ASP.NET アプリケーションのエントリ ポイントは、*Global.asax* ファイルです。</span><span class="sxs-lookup"><span data-stu-id="7bdca-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="7bdca-132">ルート構成、フィルター、領域の登録などのタスクは、*Global.asax* ファイルで処理されます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="7bdca-133">このアプローチでは、アプリケーションとその展開先のサーバーが、実装を妨げるような方法で結合されます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="7bdca-134">結合を切り離すため、複数のフレームワークを一緒に使うさらにクリーンな方法を提供する [OWIN](https://owin.org/) が導入されました。</span><span class="sxs-lookup"><span data-stu-id="7bdca-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="7bdca-135">OWIN は、必要なモジュールのみを追加するためのパイプラインを提供します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="7bdca-136">ホスティング環境は、[Startup](xref:fundamentals/startup) 関数を取得して、サービスとアプリの要求パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="7bdca-137">`Startup` は、ミドルウェアのセットをアプリケーションに登録します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="7bdca-138">アプリケーションは、要求ごとに、既存のハンドラーのセットに対するリンク リストのヘッド ポインターを指定して、各ミドルウェア コンポーネントを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="7bdca-139">各ミドルウェア コンポーネントは、要求処理パイプラインに 1 つ以上のハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="7bdca-140">これは、新しいリストのヘッドであるハンドラーへの参照を返すことによって行われます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="7bdca-141">各ハンドラーは、リスト内の次のハンドラーを記憶して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="7bdca-142">ASP.NET Core では、アプリケーションへのエントリ ポイントは `Startup` であり、*Global.asax* に依存する必要はなくなりました。</span><span class="sxs-lookup"><span data-stu-id="7bdca-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="7bdca-143">.NET Framework で OWIN を使うときは、パイプラインとして次のようなものを使います。</span><span class="sxs-lookup"><span data-stu-id="7bdca-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="7bdca-144">これにより既定のルートが構成され、既定では Json 経由の XmlSerialization です。</span><span class="sxs-lookup"><span data-stu-id="7bdca-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="7bdca-145">必要に応じて、このパイプラインに他のミドルウェアを追加します (サービスの読み込み、構成設定、静的ファイルなど)。</span><span class="sxs-lookup"><span data-stu-id="7bdca-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="7bdca-146">ASP.NET Core は同様のアプローチを使いますが、エントリを処理するために OWIN には依存しません。</span><span class="sxs-lookup"><span data-stu-id="7bdca-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="7bdca-147">代わりに、(コンソール アプリケーションと同じように) *Program.cs* の `Main` メソッドを通して行われ、そこから `Startup` が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="7bdca-148">`Startup` は、`Configure` メソッドを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="7bdca-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="7bdca-149">`Configure` では、必要なミドルウェアをパイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="7bdca-150">(既定の Web サイト テンプレートからの) 次の例では、複数の拡張メソッドを使って、以下をサポートするパイプラインが構成されています。</span><span class="sxs-lookup"><span data-stu-id="7bdca-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="7bdca-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="7bdca-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="7bdca-152">エラー ページ</span><span class="sxs-lookup"><span data-stu-id="7bdca-152">Error pages</span></span>
* <span data-ttu-id="7bdca-153">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="7bdca-153">Static files</span></span>
* <span data-ttu-id="7bdca-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="7bdca-154">ASP.NET Core MVC</span></span>
* <span data-ttu-id="7bdca-155">ID</span><span class="sxs-lookup"><span data-stu-id="7bdca-155">Identity</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="7bdca-156">ホストとアプリケーションは切り離されており、将来別のプラットフォームに柔軟に移動できます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-156">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="7bdca-157">ASP.NET Core のスタートアップとミドルウェアの詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7bdca-157">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="7bdca-158">保存の構成</span><span class="sxs-lookup"><span data-stu-id="7bdca-158">Storing configurations</span></span>

<span data-ttu-id="7bdca-159">ASP.NET では保存の設定がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7bdca-159">ASP.NET supports storing settings.</span></span> <span data-ttu-id="7bdca-160">これらの設定は、たとえば、アプリケーションが展開された環境のサポートに使われます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-160">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="7bdca-161">一般的な方法は、すべてのカスタム キー/値ペアを、`<appSettings>`Web.config*ファイルの* セクションに保存するというものでした。</span><span class="sxs-lookup"><span data-stu-id="7bdca-161">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="7bdca-162">アプリケーションでは、`ConfigurationManager.AppSettings` 名前空間内の `System.Configuration` コレクションを使ってこれらの設定を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="7bdca-162">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="7bdca-163">ASP.NET Core では、アプリケーションの構成データを任意のファイルに保存し、ミドルウェアのブートストラップの一部として読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-163">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="7bdca-164">プロジェクト テンプレートで使われる既定のファイルは、*appsettings.json* です。</span><span class="sxs-lookup"><span data-stu-id="7bdca-164">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="7bdca-165">アプリケーション内部の `IConfiguration` のインスタンスにこのファイルを読み込むには、*Startup.cs* が使われます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-165">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="7bdca-166">アプリでは、`Configuration` から読み取って設定を取得します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-166">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="7bdca-167">このアプローチにはプロセスをより堅牢にする拡張機能があります。たとえば、[依存性の注入](xref:fundamentals/dependency-injection) (DI) を使ってサービスとこれらの値を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-167">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="7bdca-168">DI アプローチは、厳密に型指定された構成オブジェクトのセットを提供します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-168">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="7bdca-169">**注:** ASP.NET Core 構成の詳細なリファレンスについては、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7bdca-169">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="7bdca-170">ネイティブな依存性の注入</span><span class="sxs-lookup"><span data-stu-id="7bdca-170">Native dependency injection</span></span>

<span data-ttu-id="7bdca-171">大規模で拡張性の高いアプリケーションを構築するときの重要な目標は、コンポーネントとサービスの疎な結合です。</span><span class="sxs-lookup"><span data-stu-id="7bdca-171">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="7bdca-172">[依存関係の挿入](xref:fundamentals/dependency-injection)は、これを実現するための一般的な手法であり、ASP.NET Core のネイティブコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="7bdca-172">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="7bdca-173">ASP.NET アプリケーションでは、開発者はサードパーティのライブラリを使用して依存関係の挿入を実装します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-173">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="7bdca-174">[Unity](https://github.com/unitycontainer/unity) はそのようなライブラリの 1 つであり、Microsoft Patterns & Practices によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-174">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="7bdca-175">Unity で依存関係の挿入を設定する例として、`UnityContainer`をラップする `IDependencyResolver` が実装されています。</span><span class="sxs-lookup"><span data-stu-id="7bdca-175">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="7bdca-176">`UnityContainer` のインスタンスを作成し、サービスを登録して、`HttpConfiguration` の依存関係リゾルバーをコンテナー用の `UnityResolver` の新しいインスタンスに設定します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-176">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="7bdca-177">必要な場所に `IProductRepository` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="7bdca-177">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="7bdca-178">依存関係の挿入は ASP.NET Core の一部であるため、`Startup.ConfigureServices`にサービスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-178">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="7bdca-179">Unity でそうであったように、リポジトリは任意の場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-179">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="7bdca-180">ASP.NET Core での依存関係の挿入の詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7bdca-180">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="7bdca-181">静的ファイルの提供</span><span class="sxs-lookup"><span data-stu-id="7bdca-181">Serving static files</span></span>

<span data-ttu-id="7bdca-182">Web 開発の重要な部分は、静的なクライアント側アセットを提供する機能です。</span><span class="sxs-lookup"><span data-stu-id="7bdca-182">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="7bdca-183">静的なファイルの最も一般的な例は、HTML、CSS、Javascript、およびイメージです。</span><span class="sxs-lookup"><span data-stu-id="7bdca-183">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="7bdca-184">これらのファイルは、アプリ (または CDN) の公開された場所に保存され、要求によって読み込めるように参照される必要があります。</span><span class="sxs-lookup"><span data-stu-id="7bdca-184">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="7bdca-185">このプロセスは、ASP.NET Core で変更されました。</span><span class="sxs-lookup"><span data-stu-id="7bdca-185">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="7bdca-186">ASP.NET では、静的ファイルはさまざまなディレクトリに保存され、ビューで参照されます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-186">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="7bdca-187">ASP.NET Core では、構成が変更されていない限り、静的ファイルは "Web ルート" ( *&lt;コンテンツ ルート&gt;/wwwroot*) に保存されます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-187">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="7bdca-188">ファイルは、`UseStaticFiles` から `Startup.Configure` 拡張メソッドを呼び出すことによって、要求パイプラインに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-188">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="7bdca-189">**注:** .NET Framework を対象にする場合は、NuGet パッケージ `Microsoft.AspNetCore.StaticFiles` をインストールします。</span><span class="sxs-lookup"><span data-stu-id="7bdca-189">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="7bdca-190">たとえば、*wwwroot/images* フォルダー内のイメージ アセットには、ブラウザーから `http://<app>/images/<imageFileName>` などの場所でアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7bdca-190">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="7bdca-191">**注:** ASP.NET Core での静的ファイルの提供に関する詳細なリファレンスについては、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7bdca-191">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7bdca-192">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="7bdca-192">Additional resources</span></span>

* [<span data-ttu-id="7bdca-193">.NET Core にライブラリを移植する</span><span class="sxs-lookup"><span data-stu-id="7bdca-193">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
