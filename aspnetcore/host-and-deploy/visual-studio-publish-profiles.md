---
title: ASP.NET Core アプリを配置するための Visual Studio 発行プロファイル
author: rick-anderson
description: Visual Studio で発行プロファイルを作成し、それらを使用してさまざまなターゲットへの ASP.NET Core アプリの配置を管理する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/18/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: ac243a3898553b2e14a6c15d311afaf62f112a24
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207821"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a><span data-ttu-id="cbd55-103">ASP.NET Core アプリを配置するための Visual Studio 発行プロファイル</span><span class="sxs-lookup"><span data-stu-id="cbd55-103">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>

<span data-ttu-id="cbd55-104">作成者: [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cbd55-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cbd55-105">このドキュメントでは、Visual Studio 2017 以降を使用して、発行プロファイルを作成および使用する方法に焦点を当てます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-105">This document focuses on using Visual Studio 2017 or later to create and use publish profiles.</span></span> <span data-ttu-id="cbd55-106">Visual Studio を使用して作成した発行プロファイルは、MSBuild および Visual Studio から実行することができます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-106">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio.</span></span> <span data-ttu-id="cbd55-107">Azure に発行する方法については、「[Visual Studio を使用して Azure App Service に ASP.NET Core アプリを発行する](xref:tutorials/publish-to-azure-webapp-using-vs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-107">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on publishing to Azure.</span></span>

<span data-ttu-id="cbd55-108">`dotnet new mvc` コマンドでは、次の最上位レベルの `<Project>` 要素を含むプロジェクト ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-108">The `dotnet new mvc` command produces a project file containing the following top-level `<Project>` element:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="cbd55-109">上の `<Project>` 要素の `Sdk` 属性では、MSBuild の[プロパティ](/visualstudio/msbuild/msbuild-properties)と[ターゲット](/visualstudio/msbuild/msbuild-targets)が、それぞれ *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* と *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* からインポートされます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="cbd55-110">(Visual Studio 2019 Enterprise の場合) `$(MSBuildSDKsPath)` の既定の場所は、 *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="cbd55-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="cbd55-111">`Microsoft.NET.Sdk.Web` (Web SDK) は、`Microsoft.NET.Sdk` (.NET Core SDK) や `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)) などの他の SDK に依存します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="cbd55-112">依存する各 SDK に関連付けられている MSBuild のプロパティとターゲットがインポートされます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="cbd55-113">発行ターゲットでは、使われる発行方法に基づいて、適切なターゲットのセットがインポートされます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="cbd55-114">MSBuild または Visual Studio がプロジェクトを読み込むと、次の高レベルのアクションが発生します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="cbd55-115">プロジェクトをビルドする</span><span class="sxs-lookup"><span data-stu-id="cbd55-115">Build project</span></span>
* <span data-ttu-id="cbd55-116">発行するファイルを計算する</span><span class="sxs-lookup"><span data-stu-id="cbd55-116">Compute files to publish</span></span>
* <span data-ttu-id="cbd55-117">ターゲットにファイルを発行する</span><span class="sxs-lookup"><span data-stu-id="cbd55-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="cbd55-118">プロジェクト項目を比較する</span><span class="sxs-lookup"><span data-stu-id="cbd55-118">Compute project items</span></span>

<span data-ttu-id="cbd55-119">プロジェクトが読み込まれると、[MSBuild プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items) (ファイル) が計算されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="cbd55-120">項目の種類によって、ファイルの処理方法が決まります。</span><span class="sxs-lookup"><span data-stu-id="cbd55-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="cbd55-121">既定で、 *.cs* ファイルは `Compile` 項目一覧に含まれています。</span><span class="sxs-lookup"><span data-stu-id="cbd55-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="cbd55-122">`Compile` 項目一覧のファイルがコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="cbd55-123">`Content` 項目一覧には、ビルドの出力に加え、発行されるファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cbd55-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="cbd55-124">既定では、`wwwroot\**`、`**\*.config`、`**\*.json` というパターンに一致するファイルが、`Content` の項目一覧に含まれます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="cbd55-125">たとえば、`wwwroot\**` [glob パターン](https://gruntjs.com/configuring-tasks#globbing-patterns)は、*wwwroot* フォルダー**および**サブフォルダー内のすべてのファイルと一致します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder **and** its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cbd55-126">Web SDK では、[Razor SDK](xref:razor-pages/sdk) がインポートされます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="cbd55-127">その結果、`**\*.cshtml` および `**\*.razor` というパターンに一致するファイルが、`Content` の項目一覧に含まれます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="cbd55-128">Web SDK では、[Razor SDK](xref:razor-pages/sdk) がインポートされます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="cbd55-129">その結果、`**\*.cshtml` というパターンに一致するファイルが、`Content` の項目一覧に含まれます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="cbd55-130">発行一覧に明示的にファイルを追加するには、「[ファイルを含める](#include-files)」セクションで説明されているように、 *.csproj* ファイルに直接ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="cbd55-131">Visual Studio で **[発行]** ボタンを選択するか、コマンド ラインから発行すると、以下が実行されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="cbd55-132">プロパティ/項目が計算されます (ビルドに必要なファイル)。</span><span class="sxs-lookup"><span data-stu-id="cbd55-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="cbd55-133">**Visual Studio のみ**: NuGet パッケージが復元されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="cbd55-134">(CLI では、ユーザーが明示的に復元する必要があります)。</span><span class="sxs-lookup"><span data-stu-id="cbd55-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="cbd55-135">プロジェクトがビルドされます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-135">The project builds.</span></span>
* <span data-ttu-id="cbd55-136">発行項目が計算されます (発行に必要なファイル)。</span><span class="sxs-lookup"><span data-stu-id="cbd55-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="cbd55-137">プロジェクトが発行されます (計算されたファイルが発行先にコピーされます)。</span><span class="sxs-lookup"><span data-stu-id="cbd55-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="cbd55-138">ASP.NET Core プロジェクトは、プロジェクト ファイルの `Microsoft.NET.Sdk.Web` を参照して、*app_offline.htm* ファイルを Web アプリのディレクトリのルートに配置します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="cbd55-139">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="cbd55-140">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="cbd55-141">基本的なコマンド ラインからの発行</span><span class="sxs-lookup"><span data-stu-id="cbd55-141">Basic command-line publishing</span></span>

<span data-ttu-id="cbd55-142">コマンド ラインからの発行は、.NET Core をサポートするすべてのプラットフォームで機能し、Visual Studio は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="cbd55-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="cbd55-143">次の例では、プロジェクト ディレクトリ ( *.csproj* ファイルが含まれているディレクトリ) から [dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-143">In the following samples, the [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="cbd55-144">現在のフォルダーがプロジェクト フォルダーではない場合は、プロジェクト ファイル パスにパスを明示的に渡します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-144">If not in the project folder, explicitly pass in the project file path.</span></span> <span data-ttu-id="cbd55-145">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-145">For example:</span></span>

```console
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="cbd55-146">Web アプリを作成して発行するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-146">Run the following commands to create and publish a web app:</span></span>

```console
dotnet new mvc
dotnet publish
```

<span data-ttu-id="cbd55-147">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドは、次のような出力を生成します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-147">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {version} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\publish\
```

<span data-ttu-id="cbd55-148">既定の発行フォルダーは `bin\$(Configuration)\netcoreapp<version>\publish` です。</span><span class="sxs-lookup"><span data-stu-id="cbd55-148">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="cbd55-149">`$(Configuration)` の既定値は *Debug* です。</span><span class="sxs-lookup"><span data-stu-id="cbd55-149">The default for `$(Configuration)` is *Debug*.</span></span> <span data-ttu-id="cbd55-150">上記のサンプルでは、`<TargetFramework>` は `netcoreapp{X.Y}`です。</span><span class="sxs-lookup"><span data-stu-id="cbd55-150">In the preceding sample, the `<TargetFramework>` is `netcoreapp{X.Y}`.</span></span>

<span data-ttu-id="cbd55-151">`dotnet publish -h` では、発行のヘルプ情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-151">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="cbd55-152">次のコマンドでは、`Release` ビルドと発行ディレクトリを指定します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-152">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="cbd55-153">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドは MSBuild を呼び出し、MSBuild は `Publish` ターゲットを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-153">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="cbd55-154">`dotnet publish` に渡されたすべてのパラメーターが MSBuild に渡されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-154">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="cbd55-155">`-c` パラメーターは、`Configuration` MSBuild プロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-155">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="cbd55-156">`-o` パラメーターは `OutputPath` にマップされます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-156">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="cbd55-157">MSBuild のプロパティは、次のいずれかの形式を使用して渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-157">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="cbd55-158">次のコマンドは、`Release` ビルドをネットワーク共有に発行します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-158">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="cbd55-159">ネットワーク共有は、スラッシュを使用して指定します ( *//r8/* )。 .NET Core がサポートされるすべてのプラットフォームで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-159">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="cbd55-160">配置用に発行したアプリが実行されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-160">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="cbd55-161">アプリが実行中は、*publish* フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-161">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="cbd55-162">ロックされているファイルはコピーできないため、配置は行われません。</span><span class="sxs-lookup"><span data-stu-id="cbd55-162">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="cbd55-163">プロファイルを発行する</span><span class="sxs-lookup"><span data-stu-id="cbd55-163">Publish profiles</span></span>

<span data-ttu-id="cbd55-164">このセクションでは、Visual Studio 2017 以降を使用して発行プロファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-164">This section uses Visual Studio 2017 or later to create a publishing profile.</span></span> <span data-ttu-id="cbd55-165">プロファイルが作成されると、Visual Studio またはコマンド ラインから発行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="cbd55-165">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span>

<span data-ttu-id="cbd55-166">発行プロファイルは発行プロセスを簡略化でき、任意の数のプロファイルが存在できます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-166">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span> <span data-ttu-id="cbd55-167">次のいずれかの方法で、Visual Studio で発行プロファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-167">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="cbd55-168">**ソリューション エクスプローラー**で、プロジェクトを右クリックして **[発行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="cbd55-168">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="cbd55-169">**[ビルド]** メニューの **[{PROJECT NAME} の発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-169">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="cbd55-170">アプリ容量ページの **[発行]** タブが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-170">The **Publish** tab of the app capacities page is displayed.</span></span> <span data-ttu-id="cbd55-171">プロジェクトに発行プロファイルが含まれていない場合は、次のページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-171">If the project lacks a publish profile, the following page is displayed:</span></span>

![アプリ容量ページの [発行] タブ](visual-studio-publish-profiles/_static/az.png)

<span data-ttu-id="cbd55-173">**[フォルダー]** を選択した場合は、発行された資産を保存するフォルダーのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-173">When **Folder** is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="cbd55-174">既定のフォルダーは *bin\Release\PublishOutput* です。</span><span class="sxs-lookup"><span data-stu-id="cbd55-174">The default folder is *bin\Release\PublishOutput*.</span></span> <span data-ttu-id="cbd55-175">**[プロファイルの作成]** ボタンをクリックして完了します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-175">Click the **Create Profile** button to finish.</span></span>

<span data-ttu-id="cbd55-176">発行プロファイルが作成されると、 **[発行]** タブが変化します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-176">Once a publish profile is created, the **Publish** tab changes.</span></span> <span data-ttu-id="cbd55-177">新しく作成したプロファイルがドロップダウン リストに表示されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-177">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="cbd55-178">別の新しいプロファイルを作成するには、 **[新しいプロファイルの作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="cbd55-178">Click **Create new profile** to create another new profile.</span></span>

![FolderProfile を示しているアプリ容量ページの [発行] タブ](visual-studio-publish-profiles/_static/create_new.png)

<span data-ttu-id="cbd55-180">発行ウィザードは、次の発行ターゲットをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="cbd55-180">The Publish wizard supports the following publish targets:</span></span>

* <span data-ttu-id="cbd55-181">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cbd55-181">Azure App Service</span></span>
* <span data-ttu-id="cbd55-182">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="cbd55-182">Azure Virtual Machines</span></span>
* <span data-ttu-id="cbd55-183">IIS、FTP など (すべての Web サーバー)</span><span class="sxs-lookup"><span data-stu-id="cbd55-183">IIS, FTP, etc. (for any web server)</span></span>
* <span data-ttu-id="cbd55-184">フォルダー</span><span class="sxs-lookup"><span data-stu-id="cbd55-184">Folder</span></span>
* <span data-ttu-id="cbd55-185">インポート プロファイル</span><span class="sxs-lookup"><span data-stu-id="cbd55-185">Import Profile</span></span>

<span data-ttu-id="cbd55-186">詳細については、「[状況に適した発行オプション](/visualstudio/ide/not-in-toc/web-publish-options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-186">For more information, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="cbd55-187">Visual Studio を使用して発行プロファイルを作成すると、*Properties/PublishProfiles/{PROFILE NAME}.pubxml* という MSBuild ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-187">When creating a publish profile with Visual Studio, a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="cbd55-188">*.pubxml* ファイルは MSBuild ファイルであり、発行構成設定が含まれています。</span><span class="sxs-lookup"><span data-stu-id="cbd55-188">The *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="cbd55-189">このファイルを変更して、ビルドと発行プロセスをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-189">This file can be changed to customize the build and publish process.</span></span> <span data-ttu-id="cbd55-190">このファイルは、発行プロファイルで読み取られます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-190">This file is read by the publishing process.</span></span> <span data-ttu-id="cbd55-191">`<LastUsedBuildConfiguration>` はグローバル プロパティであり、ビルドにインポートされるファイルには含めるべきではない特別なプロパティです。</span><span class="sxs-lookup"><span data-stu-id="cbd55-191">`<LastUsedBuildConfiguration>` is special because it's a global property and shouldn't be in any file that's imported in the build.</span></span> <span data-ttu-id="cbd55-192">詳細については、「[MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)」(MSBuild: 構成プロパティの設定方法) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-192">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more information.</span></span>

<span data-ttu-id="cbd55-193">Azure ターゲットに発行する場合、 *.pubxml* ファイルには、Azure サブスクリプション識別子が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-193">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="cbd55-194">そのターゲットの種類では、このファイルをソース管理に追加することはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="cbd55-194">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="cbd55-195">Azure 以外のターゲットに発行する場合は、 *.pubxml* ファイルをチェックインするほうが安全です。</span><span class="sxs-lookup"><span data-stu-id="cbd55-195">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="cbd55-196">機微な情報 (発行パスワードなど) は、個々のユーザー/コンピューター レベルで暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-196">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="cbd55-197">それは、*Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-197">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="cbd55-198">このファイルには機微な情報が格納される可能性があるため、ソース コード管理にチェックインしないでください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-198">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="cbd55-199">ASP.NET Core で Web アプリを発行する方法の概要については、「[ホストと展開](xref:host-and-deploy/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-199">For an overview of how to publish a web app on ASP.NET Core, see [Host and deploy](xref:host-and-deploy/index).</span></span> <span data-ttu-id="cbd55-200">ASP.NET Core アプリを発行するために必要な MSBuild タスクとターゲットは、[aspnet/websdk リポジトリ](https://github.com/aspnet/websdk)のオープン ソースです。</span><span class="sxs-lookup"><span data-stu-id="cbd55-200">The MSBuild tasks and targets necessary to publish an ASP.NET Core app are open-source at the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="cbd55-201">`dotnet publish` は、フォルダー、MSDeploy、および [Kudu](https://github.com/projectkudu/kudu/wiki) 発行プロファイルを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-201">`dotnet publish` can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles:</span></span>

<span data-ttu-id="cbd55-202">フォルダー (クロス プラットフォームで動作します):</span><span class="sxs-lookup"><span data-stu-id="cbd55-202">Folder (works cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="cbd55-203">MSDeploy (MSDeploy はクロスプラットフォームではないため、これは現在 Windows のみで機能します):</span><span class="sxs-lookup"><span data-stu-id="cbd55-203">MSDeploy (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="cbd55-204">MSDeploy パッケージ (MSDeploy はクロスプラットフォームではないため、これは現在 Windows のみで機能します):</span><span class="sxs-lookup"><span data-stu-id="cbd55-204">MSDeploy package (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="cbd55-205">上記の例で、`deployonbuild` を `dotnet publish` に渡さないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-205">In the preceding examples, don't pass `deployonbuild` to `dotnet publish`.</span></span>

<span data-ttu-id="cbd55-206">詳細については、「[Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-206">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="cbd55-207">`dotnet publish` は、任意のプラットフォームから Azure に発行する Kudu API をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="cbd55-207">`dotnet publish` supports Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="cbd55-208">Visual Studio の発行は、Kudu API をサポートしていますが、Azure へのクロスプラットフォームの発行は WebSDK がサポートしています。</span><span class="sxs-lookup"><span data-stu-id="cbd55-208">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>

<span data-ttu-id="cbd55-209">次の内容の発行プロファイルを *Properties/PublishProfiles* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-209">Add a publish profile to the *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="cbd55-210">次のコマンドを実行すると、発行するコンテンツが圧縮され、Kudu API を使用して Azure に発行されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-210">Run the following command to zip up the publish contents and publish it to Azure using the Kudu APIs:</span></span>

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

<span data-ttu-id="cbd55-211">発行プロファイルを使用する場合、次の MSBuild プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-211">Set the following MSBuild properties when using a publish profile:</span></span>

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

<span data-ttu-id="cbd55-212">*FolderProfile* という名前のプロファイルを使用して発行する場合は、以下のいずれかのコマンドを実行できます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-212">When publishing with a profile named *FolderProfile*, either of the commands below can be executed:</span></span>

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="cbd55-213">[dotnet build](/dotnet/core/tools/dotnet-build) を呼び出すと、`msbuild` が呼び出されて、ビルドと発行プロセスが実行されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-213">When invoking [dotnet build](/dotnet/core/tools/dotnet-build), it calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="cbd55-214">`dotnet build` または `msbuild` の呼び出しは、フォルダー プロファイルで渡す場合は同等です。</span><span class="sxs-lookup"><span data-stu-id="cbd55-214">Calling either `dotnet build` or `msbuild` is equivalent when passing in a folder profile.</span></span> <span data-ttu-id="cbd55-215">Windows で MSBuild を直接呼び出すと、MSBuild の .NET Framework バージョンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-215">When calling MSBuild directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="cbd55-216">MSDeploy の発行は、現在 Windows コンピューターに制限されています。</span><span class="sxs-lookup"><span data-stu-id="cbd55-216">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="cbd55-217">フォルダー以外のプロファイルで `dotnet build` を呼び出すと MSBuild が呼び出され、MSBuild はフォルダー以外のプロファイルで MSDeploy を使用します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-217">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="cbd55-218">フォルダー以外のプロファイルで `dotnet build` を呼び出すと、(MSDeploy を使用して) MSBuild が呼び出され、(Windows プラットフォームで実行している場合でも) エラーになります。</span><span class="sxs-lookup"><span data-stu-id="cbd55-218">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="cbd55-219">フォルダー以外のプロファイルで発行するには、MSBuild を直接呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-219">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="cbd55-220">次のフォルダー発行プロファイルは、Visual Studio で作成され、ネットワーク共有に発行されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="cbd55-221">上の例で、`<LastUsedBuildConfiguration>` は `Release` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-221">In the preceding example, `<LastUsedBuildConfiguration>` is set to `Release`.</span></span> <span data-ttu-id="cbd55-222">Visual Studio から発行すると、発行プロセスが開始されたときの値を使用して、`<LastUsedBuildConfiguration>` 構成プロパティ値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-222">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="cbd55-223">`<LastUsedBuildConfiguration>` 構成プロパティは特別なプロパティなので、インポートされる MSBuild ファイルで上書きされないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-223">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="cbd55-224">このプロパティは、コマンド ラインからオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-224">This property can be overridden from the command line.</span></span>

<span data-ttu-id="cbd55-225">.NET Core CLI の使用:</span><span class="sxs-lookup"><span data-stu-id="cbd55-225">Using the .NET Core CLI:</span></span>

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="cbd55-226">MSBuild の使用:</span><span class="sxs-lookup"><span data-stu-id="cbd55-226">Using MSBuild:</span></span>

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="cbd55-227">コマンド ラインから MSDeploy エンドポイントに発行する</span><span class="sxs-lookup"><span data-stu-id="cbd55-227">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="cbd55-228">次の例では、Visual Studio によって作成された *AzureWebApp* という名前の ASP.NET Core Web アプリが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-228">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="cbd55-229">Azure アプリ発行プロファイルは、Visual Studio を使用して追加されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-229">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="cbd55-230">プロファイルを作成する方法の詳細については、「[発行プロファイル](#publish-profiles)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-230">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="cbd55-231">発行プロファイルを使用してアプリを配置するには、Visual Studio の**開発者コマンド プロンプト**から `msbuild` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-231">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="cbd55-232">コマンド プロンプトは、Windows タスク バー上の **[スタート]** メニューの *Visual Studio* フォルダーで利用できます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-232">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="cbd55-233">簡単にアクセスできるようにするために、Visual Studio の **[ツール]** メニューにコマンド プロンプトを追加できます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-233">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="cbd55-234">詳細については、「[Visual Studio 用開発者コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-234">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="cbd55-235">MSBuild では次の構文が使用されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-235">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="cbd55-236">{PATH} &ndash; アプリのプロジェクト ファイルへのパス。</span><span class="sxs-lookup"><span data-stu-id="cbd55-236">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="cbd55-237">{PROFILE} &ndash; 発行プロファイルの名前。</span><span class="sxs-lookup"><span data-stu-id="cbd55-237">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="cbd55-238">{USERNAME} &ndash; MSDeploy ユーザー名。</span><span class="sxs-lookup"><span data-stu-id="cbd55-238">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="cbd55-239">{USERNAME} は発行プロファイルで確認できます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-239">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="cbd55-240">{PASSWORD} &ndash; MSDeploy パスワード。</span><span class="sxs-lookup"><span data-stu-id="cbd55-240">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="cbd55-241">*{PROFILE}.PublishSettings* ファイルから {PASSWORD} を取得します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-241">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="cbd55-242">次のいずれかの方法で、 *.PublishSettings* ファイルをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="cbd55-242">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="cbd55-243">ソリューション エクスプローラー: **[ビュー]**  >  **[Cloud Explorer]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-243">Solution Explorer: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="cbd55-244">ご自分の Azure サブスクリプションを使用して接続します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-244">Connect with your Azure subscription.</span></span> <span data-ttu-id="cbd55-245">**App Services** を開きます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-245">Open **App Services**.</span></span> <span data-ttu-id="cbd55-246">アプリを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="cbd55-246">Right-click the app.</span></span> <span data-ttu-id="cbd55-247">**[発行プロファイルのダウンロード]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-247">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="cbd55-248">Azure portal: Web アプリの **[概要]** ウィンドウで **[発行プロファイルの取得]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="cbd55-248">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="cbd55-249">次の例では、*AzureWebApp - Web Deploy* という名前の発行プロファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-249">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="cbd55-250">Windows コマンド プロンプトからの .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) コマンドで、発行プロファイルを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-250">A publish profile can also be used with the .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command prompt:</span></span>

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> <span data-ttu-id="cbd55-251">[dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) コマンドは、プラットフォームにまたがって使用できます。これにより、macOS および Linux 上で ASP.NET Core アプリをコンパイルすることができます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-251">The [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command is available cross-platform and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="cbd55-252">ただし、macOS および Linux 上の MSBuild では、Azure またはその他の MSDeploy エンドポイントにアプリを配置することはできません。</span><span class="sxs-lookup"><span data-stu-id="cbd55-252">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoint.</span></span> <span data-ttu-id="cbd55-253">MSDeploy は Windows 上でしか利用できません。</span><span class="sxs-lookup"><span data-stu-id="cbd55-253">MSDeploy is only available on Windows.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="cbd55-254">環境を設定する</span><span class="sxs-lookup"><span data-stu-id="cbd55-254">Set the environment</span></span>

<span data-ttu-id="cbd55-255">発行プロファイル ( *.pubxml*) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを追加し、アプリの[環境](xref:fundamentals/environments)を設定します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="cbd55-256">*web.config* の変換が必要な場合 (たとえば、構成、プロファイル、または環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="cbd55-257">ファイルを除外する</span><span class="sxs-lookup"><span data-stu-id="cbd55-257">Exclude files</span></span>

<span data-ttu-id="cbd55-258">ASP.NET Core Web アプリを発行するときは、次の資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="cbd55-259">ビルド成果物</span><span class="sxs-lookup"><span data-stu-id="cbd55-259">Build artifacts</span></span>
* <span data-ttu-id="cbd55-260">次の glob パターンと一致するフォルダーおよびファイル:</span><span class="sxs-lookup"><span data-stu-id="cbd55-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="cbd55-261">`**\*.config` (例: *web.config*)</span><span class="sxs-lookup"><span data-stu-id="cbd55-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="cbd55-262">`**\*.json` (例: *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="cbd55-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="cbd55-263">MSBuild では、[glob パターン](https://gruntjs.com/configuring-tasks#globbing-patterns)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cbd55-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="cbd55-264">たとえば、次の `<Content>` 要素では、*wwwroot/content* フォルダーとそのサブフォルダー内にあるテキスト ( *.txt*) ファイルのコピーが抑制されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="cbd55-265">上記のマークアップは、発行プロファイルまたは *.csproj* ファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="cbd55-266">*.csproj* ファイルに追加すると、プロジェクト内のすべての発行プロファイルにルールが追加されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="cbd55-267">次の `<MsDeploySkipRules>` 要素では、*wwwroot\content* フォルダーのすべてのファイルが除外されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="cbd55-268">`<MsDeploySkipRules>` は、"*スキップされる*" ターゲットを配置サイトから削除しません。</span><span class="sxs-lookup"><span data-stu-id="cbd55-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="cbd55-269">`<Content>` のターゲットであるファイルとフォルダーは、配置サイトから削除されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="cbd55-270">たとえば、配置される Web アプリに次のファイルが含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="cbd55-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="cbd55-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cbd55-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="cbd55-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cbd55-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="cbd55-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cbd55-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="cbd55-274">次の `<MsDeploySkipRules>` 要素を追加した場合、これらのファイルは配置サイトでは削除されません。</span><span class="sxs-lookup"><span data-stu-id="cbd55-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="cbd55-275">前述の `<MsDeploySkipRules>` 要素は、"*スキップされる*" ファイルが配置されないようにします。</span><span class="sxs-lookup"><span data-stu-id="cbd55-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="cbd55-276">それは、いったん配置されたファイルは削除しません。</span><span class="sxs-lookup"><span data-stu-id="cbd55-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="cbd55-277">次の `<Content>` 要素は、配置サイトのターゲット ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="cbd55-278">前述の`<Content>` 要素を使用してコマンド ライン配置を行うと、次の出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-278">Using command-line deployment with the preceding `<Content>` element yields the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="cbd55-279">インクルード ファイル</span><span class="sxs-lookup"><span data-stu-id="cbd55-279">Include files</span></span>

<span data-ttu-id="cbd55-280">次のマークアップでは:</span><span class="sxs-lookup"><span data-stu-id="cbd55-280">The following markup:</span></span>

* <span data-ttu-id="cbd55-281">プロジェクト ディレクトリの外部にある *images* フォルダーが、発行サイトの *wwwroot/images* フォルダーに含められます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-281">Includes an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site.</span></span>
* <span data-ttu-id="cbd55-282">*.csproj* ファイルまたは発行プロファイルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-282">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="cbd55-283">*.csproj* ファイルに追加した場合は、プロジェクトの各発行プロファイルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-283">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="cbd55-284">次の強調表示されているマークアップは、この方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="cbd55-284">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="cbd55-285">プロジェクトの外部にあるファイルを *wwwroot* フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="cbd55-285">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="cbd55-286">*wwwroot\Content* フォルダーを除外します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-286">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="cbd55-287">*Views\Home\About2.cshtml* を除外します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-287">Exclude *Views\Home\About2.cshtml*.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework />
    <ProjectGuid>afa9f185-7ce0-4935-9da1-ab676229d68a</ProjectGuid>
    <publishUrl>bin\Release\PublishOutput</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
  <ItemGroup>
    <ResolvedFileToPublish Include="..\ReadMe2.MD">
      <RelativePath>wwwroot\ReadMe2.MD</RelativePath>
    </ResolvedFileToPublish>

    <Content Update="wwwroot\Content\**\*" CopyToPublishDirectory="Never" />
    <Content Update="Views\Home\About2.cshtml" CopyToPublishDirectory="Never" />

  </ItemGroup>
</Project>
```

<span data-ttu-id="cbd55-288">他の配置例については、[Web SDK リポジトリの Readme](https://github.com/aspnet/websdk) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cbd55-288">See the [Web SDK repository Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="cbd55-289">発行の前または後にターゲットを実行する</span><span class="sxs-lookup"><span data-stu-id="cbd55-289">Run a target before or after publishing</span></span>

<span data-ttu-id="cbd55-290">組み込みの `BeforePublish` と `AfterPublish` ターゲットは、ターゲットの発行前または後にターゲットを実行できます。</span><span class="sxs-lookup"><span data-stu-id="cbd55-290">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="cbd55-291">発行の前と後の両方でコンソール メッセージをログに記録するには、発行プロファイルに次の要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-291">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="cbd55-292">信頼されていない証明書を使用してサーバーに発行する</span><span class="sxs-lookup"><span data-stu-id="cbd55-292">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="cbd55-293">値が `True` の `<AllowUntrustedCertificate>` プロパティを発行プロファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-293">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="cbd55-294">Kudu サービス</span><span class="sxs-lookup"><span data-stu-id="cbd55-294">The Kudu service</span></span>

<span data-ttu-id="cbd55-295">Azure App Service での Web アプリのデプロイに含まれるファイルを表示するには、[Kudu サービス](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) を使用します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-295">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="cbd55-296">`scm` トークンを Web アプリ名に追加します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-296">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="cbd55-297">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-297">For example:</span></span>

| <span data-ttu-id="cbd55-298">URL</span><span class="sxs-lookup"><span data-stu-id="cbd55-298">URL</span></span>                                    | <span data-ttu-id="cbd55-299">結果</span><span class="sxs-lookup"><span data-stu-id="cbd55-299">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="cbd55-300">Web アプリ</span><span class="sxs-lookup"><span data-stu-id="cbd55-300">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="cbd55-301">Kudu サービス</span><span class="sxs-lookup"><span data-stu-id="cbd55-301">Kudu service</span></span> |

<span data-ttu-id="cbd55-302">ファイルの表示、編集、削除、追加を行うには、[[デバッグ コンソール]](https://github.com/projectkudu/kudu/wiki/Kudu-console) メニュー項目を選択します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-302">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cbd55-303">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="cbd55-303">Additional resources</span></span>

* <span data-ttu-id="cbd55-304">[Web 配置](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) は、IIS サーバーへの Web アプリと Web サイトの配置を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="cbd55-304">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="cbd55-305">[Web SDK GitHub リポジトリ](https://github.com/aspnet/websdk/issues):配置でのファイルの問題と要求機能。</span><span class="sxs-lookup"><span data-stu-id="cbd55-305">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="cbd55-306">Visual Studio から Azure VM に ASP.NET Web アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="cbd55-306">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
