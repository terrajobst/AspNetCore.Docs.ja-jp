---
title: ASP.NET Core での Razor ファイルのコンパイル
author: rick-anderson
description: ASP.NET Core アプリで Razor ファイルのコンパイルがどのように行われるかについて説明します。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: cd096bba5eb580c0a606699a2bf7c36442fb56f7
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809069"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core での Razor ファイルのコンパイル

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

関連する MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。 ビルド時の Razor ファイルの公開はサポートされていません。 Razor ファイルはオプションで発行時にコンパイルして、アプリ &mdash; でプリコンパイル ツールを使用して展開できます。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

関連する Razor ページまたは MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。 ビルド時の Razor ファイルの公開はサポートされていません。 Razor ファイルはオプションで発行時にコンパイルして、アプリ &mdash; でプリコンパイル ツールを使用して展開できます。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

関連する Razor ページまたは MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。 Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

*.cshtml* 拡張子の Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。 実行時コンパイルは、アプリケーションを構成することで必要に応じて有効にできることがあります。

::: moniker-end

## <a name="razor-compilation"></a>Razor コンパイル

::: moniker range=">= aspnetcore-3.0"

Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。 有効になっていると、実行時コンパイルはビルド時のコンパイルを補完し、編集された Razor ファイルを更新できます。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。 更新後の Razor ファイルの編集は、ビルド時にサポートされています。 既定では、コンパイルされた *Views.dll* のみがアプリと共に展開されます。Razor ファイルのコンパイルに必要な *.cshtml* ファイルまたは参照アセンブリはアプリと共に展開されません。

> [!IMPORTANT]
> プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。 [Razor Sdk](xref:razor-pages/sdk) に移行することをお勧めします。
>
> Razor SDK は、プロジェクト ファイルにプリコンパイル固有のプロパティが設定されていない場合のみ有効です。 たとえば、 *.csproj* ファイルの `MvcRazorCompileOnPublish` プロパティを `true` に設定して、Razor SDK を無効にします。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

プロジェクトのターゲットが .NET Framework である場合は、次のように [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet パッケージをインストールします。

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

プロジェクトのターゲットが .NET Core である場合、変更する必要はありません。

ASP.NET Core 2.x プロジェクト テンプレートは既定で、暗黙的に `MvcRazorCompileOnPublish` プロパティを `true` に設定します。 そのため、この要素は *.csproj* ファイルから安全に削除できます。

> [!IMPORTANT]
> プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。 [Razor Sdk](xref:razor-pages/sdk) に移行することをお勧めします。
>
> ASP.NET Core 2.0 で[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) を実行する場合、Razor ファイルのプリコンパイルを使用できません。

::: moniker-end

::: moniker range="= aspnetcore-1.1"

`MvcRazorCompileOnPublish` プロパティを `true` に設定して、[Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet パッケージをインストールします。 次の *.csproj* サンプルでは、これらの設定が強調表示されています。

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[.NET Core CLI publish コマンド](/dotnet/core/tools/dotnet-publish)を使用して、[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)用にアプリを準備します。 たとえば、プロジェクト ルートで、次のコマンドを実行します。

```dotnetcli
dotnet publish -c Release
```

コンパイル済みの Razor ファイルを含む、 *\<project_name>.PrecompiledViews.dll* ファイルは、プリコンパイルが成功したときに生成されます。 たとえば、次のスクリーンショットは、*WebApplication1.PrecompiledViews.dll* 内の *Index.cshtml* のコンテンツを示しています。

![DLL 内の Razor ビュー](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a>実行時のコンパイル

::: moniker range="= aspnetcore-2.1"

ビルド時のコンパイルは Razor ファイルの実行時コンパイルによって補完されます。 ASP.NET Core MVC は、 *.cshtml* ファイルの内容が変更されたとき、Razor ファイルを再コンパイルします。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

ビルド時のコンパイルは Razor ファイルの実行時コンパイルによって補完されます。 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> では、ディスク上で Razor ファイル (Razor ビューと Razor Pages) が変更された場合に、ファイルが再コンパイルおよび更新されるかどうかを判断する値が取得または設定されます。

次の場合、規定値は `true` です。

* アプリの互換性バージョンが <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> 以前に設定されている場合
* アプリの互換性バージョンが <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> 以降に設定され、アプリが開発環境 <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*> にある場合。 言い換えると、<xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> が明示的に設定されていない限り、Razor ファイルは非開発環境では再コンパイルされません。

アプリの互換バージョンの設定に関するガイダンスと例については、<xref:mvc/compatibility-version> をご覧ください。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

すべての環境と構成モードで実行時コンパイルを有効にするには、次のようにします。

1. [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet パッケージをインストールします。

1. プロジェクトの `Startup.ConfigureServices` メソッドを更新して、`AddRazorRuntimeCompilation` の呼び出しを含めます。 次に例を示します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>実行時コンパイルを条件付きで有効にする

実行時コンパイルをローカル開発でのみ有効にできます。 このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。

* コンパイル済みのビューを使用する。
* サイズが小さい。
* 運用環境のファイル監視を有効にしない。

環境や構成モードに基づく実行時コンパイルを有効にするには、次のようにします。

1. アクティブな `Configuration` 値に基づいて、[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) パッケージを条件付きで参照します。

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. プロジェクトの `Startup.ConfigureServices` メソッドを更新して、`AddRazorRuntimeCompilation` の呼び出しを含めます。 `ASPNETCORE_ENVIRONMENT` 変数が `Development` に設定されている場合にのみ Debug モードで実行されるように、条件付きで `AddRazorRuntimeCompilation` を実行します。

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

::: moniker-end

## <a name="additional-resources"></a>その他の技術情報

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
