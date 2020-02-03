---
title: ASP.NET Core のディレクトリ構造
author: guardrex
description: 発行された ASP.NET Core アプリのディレクトリ構造について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2020
uid: host-and-deploy/directory-structure
ms.openlocfilehash: ba5cb96dfdcdca10034299e3bbe662ce056af791
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76870267"
---
# <a name="aspnet-core-directory-structure"></a><span data-ttu-id="bd288-103">ASP.NET Core のディレクトリ構造</span><span class="sxs-lookup"><span data-stu-id="bd288-103">ASP.NET Core directory structure</span></span>

<span data-ttu-id="bd288-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd288-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bd288-105">*publish* ディレクトリには、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドによって生成された、アプリの展開可能な資産が含まれています。</span><span class="sxs-lookup"><span data-stu-id="bd288-105">The *publish* directory contains the app's deployable assets produced by the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="bd288-106">ディレクトリには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="bd288-106">The directory contains:</span></span>

* <span data-ttu-id="bd288-107">アプリケーション ファイル</span><span class="sxs-lookup"><span data-stu-id="bd288-107">Application files</span></span>
* <span data-ttu-id="bd288-108">構成ファイル</span><span class="sxs-lookup"><span data-stu-id="bd288-108">Configuration files</span></span>
* <span data-ttu-id="bd288-109">静的な資産</span><span class="sxs-lookup"><span data-stu-id="bd288-109">Static assets</span></span>
* <span data-ttu-id="bd288-110">パッケージ</span><span class="sxs-lookup"><span data-stu-id="bd288-110">Packages</span></span>
* <span data-ttu-id="bd288-111">ランタイム ([自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)のみ)</span><span class="sxs-lookup"><span data-stu-id="bd288-111">A runtime ([self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) only)</span></span>

| <span data-ttu-id="bd288-112">アプリの種類</span><span class="sxs-lookup"><span data-stu-id="bd288-112">App Type</span></span> | <span data-ttu-id="bd288-113">ディレクトリの構造</span><span class="sxs-lookup"><span data-stu-id="bd288-113">Directory Structure</span></span> |
| -------- | ------------------- |
| [<span data-ttu-id="bd288-114">フレームワークに依存する実行可能ファイル (FDE)</span><span class="sxs-lookup"><span data-stu-id="bd288-114">Framework-dependent Executable (FDE)</span></span>](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li><span data-ttu-id="bd288-115">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="bd288-115">publish&dagger;</span></span><ul><li><span data-ttu-id="bd288-116">Views&dagger; MVC アプリ、ビューがプリコンパイルされていない場合</span><span class="sxs-lookup"><span data-stu-id="bd288-116">Views&dagger; MVC apps; if views aren't precompiled</span></span></li><li><span data-ttu-id="bd288-117">Pages&dagger; MVC または Razor Pages アプリ、ページがプリコンパイルされていない場合</span><span class="sxs-lookup"><span data-stu-id="bd288-117">Pages&dagger; MVC or Razor Pages apps, if pages aren't precompiled</span></span></li><li><span data-ttu-id="bd288-118">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="bd288-118">wwwroot&dagger;</span></span></li><li><span data-ttu-id="bd288-119">*.dll ファイル</li><li>{ASSEMBLY NAME}.deps.json</li><li>{ASSEMBLY NAME}.dll</li><li>Windows 上の {ASSEMBLY NAME}{.EXTENSION} *.exe* 拡張機能、macOS または Linux 上は拡張機能なし</li><li>{ASSEMBLY NAME}.pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{ASSEMBLY NAME}.Views.pdb</li><li>{ASSEMBLY NAME}.runtimeconfig.json</li><li>web.config (IIS 展開)</li><li>createdump ([Linux createdump ユーティリティ](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>* .so (Linux 共有オブジェクト ライブラリ)</span><span class="sxs-lookup"><span data-stu-id="bd288-119">*.dll files</li><li>{ASSEMBLY NAME}.deps.json</li><li>{ASSEMBLY NAME}.dll</li><li>{ASSEMBLY NAME}{.EXTENSION} *.exe* extension on Windows, no extension on macOS or Linux</li><li>{ASSEMBLY NAME}.pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{ASSEMBLY NAME}.Views.pdb</li><li>{ASSEMBLY NAME}.runtimeconfig.json</li><li>web.config (IIS deployments)</li><li>createdump ([Linux createdump utility](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>*.so (Linux shared object library)</span></span></li><li><span data-ttu-id="bd288-120">*.a (macOS アーカイブ)</li><li>* .dylib (macOS ダイナミック ライブラリ)</span><span class="sxs-lookup"><span data-stu-id="bd288-120">*.a (macOS archive)</li><li>*.dylib (macOS dynamic library)</span></span></li></ul></li></ul> |
| [<span data-ttu-id="bd288-121">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="bd288-121">Self-contained Deployment (SCD)</span></span>](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li><span data-ttu-id="bd288-122">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="bd288-122">publish&dagger;</span></span><ul><li><span data-ttu-id="bd288-123">Views&dagger; MVC アプリ、ビューがプリコンパイルされていない場合</span><span class="sxs-lookup"><span data-stu-id="bd288-123">Views&dagger; MVC apps, if views aren't precompiled</span></span></li><li><span data-ttu-id="bd288-124">Pages&dagger; MVC または Razor Pages アプリ、ページがプリコンパイルされていない場合</span><span class="sxs-lookup"><span data-stu-id="bd288-124">Pages&dagger; MVC or Razor Pages apps, if pages aren't precompiled</span></span></li><li><span data-ttu-id="bd288-125">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="bd288-125">wwwroot&dagger;</span></span></li><li><span data-ttu-id="bd288-126">\*.dll ファイル</span><span class="sxs-lookup"><span data-stu-id="bd288-126">\*.dll files</span></span></li><li><span data-ttu-id="bd288-127">{ASSEMBLY NAME}.deps.json</span><span class="sxs-lookup"><span data-stu-id="bd288-127">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="bd288-128">{ASSEMBLY NAME}.dll</span><span class="sxs-lookup"><span data-stu-id="bd288-128">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="bd288-129">{ASSEMBLY NAME}.exe</span><span class="sxs-lookup"><span data-stu-id="bd288-129">{ASSEMBLY NAME}.exe</span></span></li><li><span data-ttu-id="bd288-130">{ASSEMBLY NAME}.pdb</span><span class="sxs-lookup"><span data-stu-id="bd288-130">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="bd288-131">{ASSEMBLY NAME}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="bd288-131">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="bd288-132">{ASSEMBLY NAME}.Views.pdb</span><span class="sxs-lookup"><span data-stu-id="bd288-132">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="bd288-133">{ASSEMBLY NAME}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="bd288-133">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="bd288-134">web.config (IIS 展開)</span><span class="sxs-lookup"><span data-stu-id="bd288-134">web.config (IIS deployments)</span></span></li></ul></li></ul> |

<span data-ttu-id="bd288-135">&dagger; ディレクトリを示します</span><span class="sxs-lookup"><span data-stu-id="bd288-135">&dagger;Indicates a directory</span></span>

<span data-ttu-id="bd288-136">*publish* ディレクトリは、展開の "*コンテンツ ルート パス*" ("*アプリケーション ベース パス*" とも呼ばれます) を表します。</span><span class="sxs-lookup"><span data-stu-id="bd288-136">The *publish* directory represents the *content root path*, also called the *application base path*, of the deployment.</span></span> <span data-ttu-id="bd288-137">サーバー上で展開されたアプリの *publish* ディレクトリにどのような名前が指定されても、その場所がホストされたアプリへのサーバーの物理パスとして機能します。</span><span class="sxs-lookup"><span data-stu-id="bd288-137">Whatever name is given to the *publish* directory of the deployed app on the server, its location serves as the server's physical path to the hosted app.</span></span>

<span data-ttu-id="bd288-138">*Wwwroot* ディレクトリが存在する場合は、静的資産のみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="bd288-138">The *wwwroot* directory, if present, only contains static assets.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd288-139">[ASP.NET Core モジュールの強化されたデバッグ ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)では、*Logs* フォルダーを作成すると便利です。</span><span class="sxs-lookup"><span data-stu-id="bd288-139">Creating a *Logs* folder is useful for [ASP.NET Core Module enhanced debug logging](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="bd288-140">`<handlerSetting>` 値に提供されるパスのフォルダーがこのモジュールによって自動的に作成されることはありません。デバッグ ログの書き込みをモジュールに許可するには、フォルダーがデプロイに事前に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd288-140">Folders in the path provided to the `<handlerSetting>` value aren't created by the module automatically and should pre-exist in the deployment to allow the module to write the debug log.</span></span>

<span data-ttu-id="bd288-141">*Logs* ディレクトリは、次の 2 つの方法のいずれかを使って展開用に作成できます。</span><span class="sxs-lookup"><span data-stu-id="bd288-141">A *Logs* directory can be created for the deployment using one of the following two approaches:</span></span>

* <span data-ttu-id="bd288-142">プロジェクト ファイルに次の `<Target>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="bd288-142">Add the following `<Target>` element to the project file:</span></span>

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   <span data-ttu-id="bd288-143">`<MakeDir>` 要素は、公開される出力に空の *Logs* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="bd288-143">The `<MakeDir>` element creates an empty *Logs* folder in the published output.</span></span> <span data-ttu-id="bd288-144">この要素は、`PublishDir` プロパティを使って、フォルダーを作成するためのターゲットの場所を決定します。</span><span class="sxs-lookup"><span data-stu-id="bd288-144">The element uses the `PublishDir` property to determine the target location for creating the folder.</span></span> <span data-ttu-id="bd288-145">Web 配置などの複数の展開方法は、展開の間に空のフォルダーをスキップします。</span><span class="sxs-lookup"><span data-stu-id="bd288-145">Several deployment methods, such as Web Deploy, skip empty folders during deployment.</span></span> <span data-ttu-id="bd288-146">`<WriteLinesToFile>` 要素は *Logs* フォルダーにファイルを生成します。これは、サーバーへのフォルダーの展開を保証します。</span><span class="sxs-lookup"><span data-stu-id="bd288-146">The `<WriteLinesToFile>` element generates a file in the *Logs* folder, which guarantees deployment of the folder to the server.</span></span> <span data-ttu-id="bd288-147">ワーカー プロセスにターゲット フォルダーへの書き込みアクセス許可がない場合、このアプローチを使用したフォルダーの作成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="bd288-147">Folder creation using this approach fails if the worker process doesn't have write access to the target folder.</span></span>

* <span data-ttu-id="bd288-148">展開内のサーバー上に *Logs* ディレクトリを物理的に作成します。</span><span class="sxs-lookup"><span data-stu-id="bd288-148">Physically create the *Logs* directory on the server in the deployment.</span></span>

<span data-ttu-id="bd288-149">展開ディレクトリには、読み取り/実行アクセス許可が必要です。</span><span class="sxs-lookup"><span data-stu-id="bd288-149">The deployment directory requires Read/Execute permissions.</span></span> <span data-ttu-id="bd288-150">*Logs* ディレクトリには、読み取り/書き込みアクセス許可が必要です。</span><span class="sxs-lookup"><span data-stu-id="bd288-150">The *Logs* directory requires Read/Write permissions.</span></span> <span data-ttu-id="bd288-151">ファイルが書き込まれる追加のディレクトリには、読み取り/書き込みアクセス許可が必要です。</span><span class="sxs-lookup"><span data-stu-id="bd288-151">Additional directories where files are written require Read/Write permissions.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="bd288-152">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="bd288-152">Additional resources</span></span>

* [<span data-ttu-id="bd288-153">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="bd288-153">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* [<span data-ttu-id="bd288-154">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="bd288-154">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* [<span data-ttu-id="bd288-155">ターゲット フレームワーク</span><span class="sxs-lookup"><span data-stu-id="bd288-155">Target frameworks</span></span>](/dotnet/standard/frameworks)
* [<span data-ttu-id="bd288-156">.NET Core の RID カタログ</span><span class="sxs-lookup"><span data-stu-id="bd288-156">.NET Core RID Catalog</span></span>](/dotnet/core/rid-catalog)
