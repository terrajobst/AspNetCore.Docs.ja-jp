---
title: ASP.NET Core 2.0 の新機能
author: rick-anderson
description: ASP.NET Core 2.0 の新機能について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: aspnetcore-2.0
ms.openlocfilehash: 5ca43bab1496aa9fda65282cbb0b1177ad8689eb
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78650858"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="8765d-103">ASP.NET Core 2.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="8765d-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="8765d-104">この記事では、ASP.NET Core 2.0 の最も大きな変更点について説明します。また、その変更点のドキュメントへのリンクも示します。</span><span class="sxs-lookup"><span data-stu-id="8765d-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="8765d-105">Razor ページ</span><span class="sxs-lookup"><span data-stu-id="8765d-105">Razor Pages</span></span>

<span data-ttu-id="8765d-106">Razor ページは、ページ コーディングに重点を置いたシナリオをより簡略化し、生産性を高める ASP.NET Core MVC の新機能です。</span><span class="sxs-lookup"><span data-stu-id="8765d-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="8765d-107">詳細については、概要とチュートリアルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="8765d-108">Razor ページを始める</span><span class="sxs-lookup"><span data-stu-id="8765d-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="8765d-109">Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="8765d-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="8765d-110">ASP.NET Core メタパッケージ</span><span class="sxs-lookup"><span data-stu-id="8765d-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="8765d-111">新しい ASP.NET Core メタパッケージには、ASP.NET Core および Entity Framework Core チームでサポートされているすべてのパッケージが、内部およびサードパーティの依存関係と共に含まれています。</span><span class="sxs-lookup"><span data-stu-id="8765d-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="8765d-112">これにより、パッケージの ASP.NET Core の個別機能を選択する必要がなくなりました。</span><span class="sxs-lookup"><span data-stu-id="8765d-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="8765d-113">すべての機能は、[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) パッケージに含まれています。</span><span class="sxs-lookup"><span data-stu-id="8765d-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="8765d-114">このパッケージは、既定のテンプレートで使用されています。</span><span class="sxs-lookup"><span data-stu-id="8765d-114">The default templates use this package.</span></span>

<span data-ttu-id="8765d-115">詳細については、「[Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage)」 (ASP.NET Core 2.0 用の Microsoft.AspNetCore.All メタパッケージ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="8765d-116">ランタイム ストア</span><span class="sxs-lookup"><span data-stu-id="8765d-116">Runtime Store</span></span>

<span data-ttu-id="8765d-117">`Microsoft.AspNetCore.All` メタパッケージを使用するアプリケーションでは、新しい .NET Core ランタイム ストアが自動的に利用されます。</span><span class="sxs-lookup"><span data-stu-id="8765d-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="8765d-118">このストアには、ASP.NET Core 2.0 アプリケーションの実行に必要なすべてのランタイム アセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8765d-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="8765d-119">`Microsoft.AspNetCore.All` メタパッケージを使用する場合、参照される ASP.NET Core NuGet パッケージの資産は、既にターゲット システム上にあるため、アプリケーションと共に配置されません。</span><span class="sxs-lookup"><span data-stu-id="8765d-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="8765d-120">ランタイム ストア内の資産は、アプリケーションの起動時間を向上させるためにプリコンパイルもされています。</span><span class="sxs-lookup"><span data-stu-id="8765d-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="8765d-121">詳細については、「[Runtime Store](/dotnet/core/deploying/runtime-store)」 (ランタイム ストア) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="8765d-122">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="8765d-122">.NET Standard 2.0</span></span>

<span data-ttu-id="8765d-123">ASP.NET Core 2.0 パッケージは、.NET Standard 2.0 を対象としています。</span><span class="sxs-lookup"><span data-stu-id="8765d-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="8765d-124">このパッケージは、その他の .NET Standard 2.0 ライブラリから参照でき、.NET Core 2.0 と.NET Framework 4.6.1 を含む、.NET Standard 2.0 に準拠した実装で実行できます。</span><span class="sxs-lookup"><span data-stu-id="8765d-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="8765d-125">`Microsoft.AspNetCore.All` メタパッケージは、.NET Core 2.0 ランタイム ストアで使用することを意図しており、.NET Core 2.0 のみを対象としています。</span><span class="sxs-lookup"><span data-stu-id="8765d-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="8765d-126">構成の更新</span><span class="sxs-lookup"><span data-stu-id="8765d-126">Configuration update</span></span>

<span data-ttu-id="8765d-127">ASP.NET Core 2.0 では、既定で `IConfiguration` インスタンスがサービス コンテナーに追加されています。</span><span class="sxs-lookup"><span data-stu-id="8765d-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="8765d-128">サービス コンテナーの `IConfiguration` では、アプリケーションがコンテナーから構成値を取得するのを容易にします。</span><span class="sxs-lookup"><span data-stu-id="8765d-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="8765d-129">計画されているドキュメントの状態については、「[GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387)」 (GitHub の問題) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="8765d-130">ログ記録の更新</span><span class="sxs-lookup"><span data-stu-id="8765d-130">Logging update</span></span>

<span data-ttu-id="8765d-131">ASP.NET Core 2.0 には、既定で依存性の注入 (DI) システムにログ記録が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="8765d-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="8765d-132">*Startup.cs* ファイルではなく *Program.cs* ファイルに、プロバイダーを追加しフィルター処理を構成できます。</span><span class="sxs-lookup"><span data-stu-id="8765d-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="8765d-133">また、既定の `ILoggerFactory` では、プロバイダーをまたがるフィルター処理と特定のプロバイダーのフィルター処理の両方で、ある柔軟なアプローチを使用するフィルター処理をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8765d-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="8765d-134">詳細については、「[Introduction to Logging](xref:fundamentals/logging/index)」 (ログ記録の概要) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="8765d-135">認証の更新</span><span class="sxs-lookup"><span data-stu-id="8765d-135">Authentication update</span></span>

<span data-ttu-id="8765d-136">新しい認証モデルにより、DI を使用するアプリケーションでの認証の構成が容易になりました。</span><span class="sxs-lookup"><span data-stu-id="8765d-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="8765d-137">新しいテンプレートを使うと、[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) を使って Web アプリと Web API に対する認証を構成できます。</span><span class="sxs-lookup"><span data-stu-id="8765d-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="8765d-138">計画されているドキュメントの状態については、「[GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054)」 (GitHub の問題) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="8765d-139">ID の更新</span><span class="sxs-lookup"><span data-stu-id="8765d-139">Identity update</span></span>

<span data-ttu-id="8765d-140">ASP.NET Core 2.0 で ID を使用し、簡単に安全な Web API を作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8765d-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="8765d-141">[Microsoft 認証ライブラリ (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client) を使用して Web API にアクセスするアクセス トークンを取得することができます。</span><span class="sxs-lookup"><span data-stu-id="8765d-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="8765d-142">2\.0 での認証の変更の詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="8765d-143">ASP.NET Core でのアカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="8765d-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="8765d-144">ASP.NET Core の認証アプリでの QR コードの生成の有効化</span><span class="sxs-lookup"><span data-stu-id="8765d-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="8765d-145">ASP.NET Core 2.0 への認証と ID の移行</span><span class="sxs-lookup"><span data-stu-id="8765d-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="8765d-146">SPA テンプレート</span><span class="sxs-lookup"><span data-stu-id="8765d-146">SPA templates</span></span>

<span data-ttu-id="8765d-147">Angular、Aurelia、Knockout.js、React.js、Redux 用 React.js で、シングル ページ アプリケーション (SPA) プロジェクト テンプレートが利用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8765d-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="8765d-148">Angular テンプレートは、Angular 4 に更新されました。</span><span class="sxs-lookup"><span data-stu-id="8765d-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="8765d-149">Angular と React テンプレートは既定で使用可能です。その他のテンプレートの入手方法については、[新しい SPA プロジェクトを作成する](xref:client-side/spa-services#create-a-new-project)方法に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="8765d-150">ASP.NET Core で SPA を構築する方法については、<xref:client-side/spa-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="8765d-151">Kestrel の機能強化</span><span class="sxs-lookup"><span data-stu-id="8765d-151">Kestrel improvements</span></span>

<span data-ttu-id="8765d-152">Kestrel Web サーバーを、インターネット接続用サーバーとして使用するのにより適切にする新機能が追加されました。</span><span class="sxs-lookup"><span data-stu-id="8765d-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="8765d-153">`KestrelServerOptions` クラスの新しい `Limits` プロパティに、多数のサーバー制約構成が追加されました。</span><span class="sxs-lookup"><span data-stu-id="8765d-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="8765d-154">次に制限を追加できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8765d-154">Add limits for the following:</span></span>

* <span data-ttu-id="8765d-155">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="8765d-155">Maximum client connections</span></span>
* <span data-ttu-id="8765d-156">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="8765d-156">Maximum request body size</span></span>
* <span data-ttu-id="8765d-157">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="8765d-157">Minimum request body data rate</span></span>

<span data-ttu-id="8765d-158">詳細については、「[Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel)」 (ASP.NET Core への Kestrel Web サーバーの実装) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="8765d-159">WebListener から HTTP.sys への名称変更</span><span class="sxs-lookup"><span data-stu-id="8765d-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="8765d-160">パッケージ、`Microsoft.AspNetCore.Server.WebListener` と `Microsoft.Net.Http.Server` が新しい `Microsoft.AspNetCore.Server.HttpSys` パッケージにマージされました。</span><span class="sxs-lookup"><span data-stu-id="8765d-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="8765d-161">一致するように名前空間が更新されました。</span><span class="sxs-lookup"><span data-stu-id="8765d-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="8765d-162">詳細については、「[HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys)」 (ASP.NET Core への HTTP.sys Web サーバーの実装) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="8765d-163">HTTP ヘッダーのサポートの強化</span><span class="sxs-lookup"><span data-stu-id="8765d-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="8765d-164">MVC を使用して `FileStreamResult` または `FileContentResult` を送信する場合、送信するコンテンツに `ETag` または `LastModified` 日付を設定するオプションが追加されました。</span><span class="sxs-lookup"><span data-stu-id="8765d-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="8765d-165">次のようなコードで、返されるコンテンツにこれらの値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="8765d-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="8765d-166">ユーザーに返されるファイルには、`ETag` と `LastModified` 値に適切な HTTP ヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8765d-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="8765d-167">アプリケーションのユーザーが範囲要求ヘッダーを使用してコンテンツを要求する場合、ASP.NET Core は要求を認識して、そのヘッダーを処理します。</span><span class="sxs-lookup"><span data-stu-id="8765d-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="8765d-168">要求されたコンテンツを部分的に配信できる場合、ASP.NET Core は要求されたバイトのセットだけを適切に省略して返します。</span><span class="sxs-lookup"><span data-stu-id="8765d-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="8765d-169">この機能を適用または処理するために、メソッドに特別なハンドラーを記述する必要はありません。自動的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="8765d-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="8765d-170">スタートアップのホストと Application Insights</span><span class="sxs-lookup"><span data-stu-id="8765d-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="8765d-171">ホスティング環境で、アプリケーションが明示的に依存関係を取得したり、任意のメソッドを呼び出したりせずに、余分なパッケージの依存関係を挿入して、アプリケーションの起動時にコードを実行することができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8765d-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="8765d-172">この機能は、特定の環境でその環境に固有の機能を、アプリケーションが事前に認識する必要なく、明らかにできるようにするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="8765d-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="8765d-173">ASP.NET Core 2.0 では、Visual Studio でのデバッグ時および Azure App Services での実行時 (有効化後に) に、Application Insights での診断を自動的に有効にするために、この機能が使用されています。</span><span class="sxs-lookup"><span data-stu-id="8765d-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="8765d-174">その結果、プロジェクト テンプレートでは Application Insights のパッケージとコードが既定で追加されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="8765d-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="8765d-175">計画されているドキュメントの状態については、「[GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389)」 (GitHub の問題) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="8765d-176">偽造防止トークンの自動使用</span><span class="sxs-lookup"><span data-stu-id="8765d-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="8765d-177">ASP.NET Core では、常にコンテンツの HTML エンコードを既定で支援してきましたが、新しいバージョンでは、クロスサイト リクエスト フォージェリ (XSRF) 攻撃を防ぐための手段もさらに講じられています。</span><span class="sxs-lookup"><span data-stu-id="8765d-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="8765d-178">ASP.NET Core では、既定で偽造防止トークンが生成されるようになり、追加の構成をしなくても、フォーム POST アクションとページでそれらが検証されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8765d-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="8765d-179">詳細については、「[Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery)」(クロスサイト リクエスト フォージェリ (XSRF/CSRF) 攻撃の防止) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-179">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="8765d-180">自動でのプリコンパイル</span><span class="sxs-lookup"><span data-stu-id="8765d-180">Automatic precompilation</span></span>

<span data-ttu-id="8765d-181">Razor ビューのプリコンパイルは発行時に既定で有効になっています。これにより、発行の出力サイズが縮小され、アプリケーションの起動時間が削減されます。</span><span class="sxs-lookup"><span data-stu-id="8765d-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="8765d-182">詳細については、[ASP.NET Core での Razor ビューのコンパイルとプリコンパイル](xref:mvc/views/view-compilation)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="8765d-183">C# 7.1 での Razor サポート</span><span class="sxs-lookup"><span data-stu-id="8765d-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="8765d-184">Razor ビュー エンジンが更新され、新しい Roslyn コンパイラで使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8765d-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="8765d-185">これには、既定の式、推定タプル名、およびジェネリック パターン マッチングのような C# 7.1 の機能のサポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8765d-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="8765d-186">プロジェクトで C# 7.1 を使用する場合、プロジェクト ファイルに次のプロパティを追加し、ソリューションを再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="8765d-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="8765d-187">C# 7.1 機能の状態については、「[Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md)」 (Roslyn GitHub リポジトリ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="8765d-188">2\.0 のその他のドキュメントの更新</span><span class="sxs-lookup"><span data-stu-id="8765d-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="8765d-189">ASP.NET Core アプリ開発のための Visual Studio によるプロファイルの発行</span><span class="sxs-lookup"><span data-stu-id="8765d-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="8765d-190">キーの管理</span><span class="sxs-lookup"><span data-stu-id="8765d-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="8765d-191">Facebook 認証の構成</span><span class="sxs-lookup"><span data-stu-id="8765d-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="8765d-192">Twitter 認証を構成する</span><span class="sxs-lookup"><span data-stu-id="8765d-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="8765d-193">Googler 認証の構成</span><span class="sxs-lookup"><span data-stu-id="8765d-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="8765d-194">Microsoft アカウント認証の構成</span><span class="sxs-lookup"><span data-stu-id="8765d-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="8765d-195">移行ガイダンス</span><span class="sxs-lookup"><span data-stu-id="8765d-195">Migration guidance</span></span>

<span data-ttu-id="8765d-196">ASP.NET Core 1.x アプリケーションを ASP.NET Core 2.0 に移行する方法の手順については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="8765d-197">ASP.NET Core 1.x から ASP.NET Core 2.0 への移行</span><span class="sxs-lookup"><span data-stu-id="8765d-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="8765d-198">ASP.NET Core 2.0 への認証と ID の移行</span><span class="sxs-lookup"><span data-stu-id="8765d-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="8765d-199">追加情報</span><span class="sxs-lookup"><span data-stu-id="8765d-199">Additional Information</span></span>

<span data-ttu-id="8765d-200">変更の全一覧については、「[ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0)」 (ASP.NET Core 2.0 のリリース ノート) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8765d-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="8765d-201">ASP.NET Core 開発チームの進捗状況や計画とつながるには、週次の [ASP.NET Community Standup](https://live.asp.net/) にアクセスしてください。</span><span class="sxs-lookup"><span data-stu-id="8765d-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
