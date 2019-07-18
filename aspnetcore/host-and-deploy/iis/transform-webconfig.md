---
title: web.config を変換する
author: guardrex
description: ASP.NET Core アプリを発行するときに web.config ファイルを変換する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2019
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 58dee024f5b032d1ef13df02648727b6a07eac1f
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813361"
---
# <a name="transform-webconfig"></a><span data-ttu-id="a8f48-103">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="a8f48-103">Transform web.config</span></span>

<span data-ttu-id="a8f48-104">作成者: [Vijay Ramakrishnan](https://github.com/vijayrkn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a8f48-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a8f48-105">次のものに基づいてアプリを発行する場合、*web.config* ファイルに対する変換を自動的に適用することができます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="a8f48-106">ビルド構成</span><span class="sxs-lookup"><span data-stu-id="a8f48-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="a8f48-107">Profile</span><span class="sxs-lookup"><span data-stu-id="a8f48-107">Profile</span></span>](#profile)
* [<span data-ttu-id="a8f48-108">環境</span><span class="sxs-lookup"><span data-stu-id="a8f48-108">Environment</span></span>](#environment)
* [<span data-ttu-id="a8f48-109">カスタム</span><span class="sxs-lookup"><span data-stu-id="a8f48-109">Custom</span></span>](#custom)

<span data-ttu-id="a8f48-110">これらの変換は、次のいずれかの *web.config* の生成シナリオで発生します。</span><span class="sxs-lookup"><span data-stu-id="a8f48-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="a8f48-111">`Microsoft.NET.Sdk.Web` SDK によって自動的に生成された。</span><span class="sxs-lookup"><span data-stu-id="a8f48-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="a8f48-112">開発者によってアプリのコンテンツのルートに提供された。</span><span class="sxs-lookup"><span data-stu-id="a8f48-112">Provided by the developer in the content root of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="a8f48-113">ビルド構成</span><span class="sxs-lookup"><span data-stu-id="a8f48-113">Build configuration</span></span>

<span data-ttu-id="a8f48-114">ビルド構成の変換は、最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="a8f48-115">*web.config* の変換を必要とする[ビルド構成 (デバッグ|リリース)](/dotnet/core/tools/dotnet-publish#options) ごとに、*web.{CONFIGURATION}.config* ファイルを含めます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="a8f48-116">次の例では、構成固有の環境変数が *web.Release.config* 内で設定されています。</span><span class="sxs-lookup"><span data-stu-id="a8f48-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="a8f48-117">構成が *Release* に設定された場合、変換が適用されます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-117">The transform is applied when the configuration is set to *Release*:</span></span>

```console
dotnet publish --configuration Release
```

<span data-ttu-id="a8f48-118">構成の MSBuild プロパティは `$(Configuration)` です。</span><span class="sxs-lookup"><span data-stu-id="a8f48-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="a8f48-119">Profile</span><span class="sxs-lookup"><span data-stu-id="a8f48-119">Profile</span></span>

<span data-ttu-id="a8f48-120">プロファイルの変換は、[ビルド構成](#build-configuration)の変換の後、2 番目に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="a8f48-121">*web.config* の変換を必要とするプロファイル構成ごとに、*web.{PROFILE}.config* ファイルを含めます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="a8f48-122">次の例では、プロファイル固有の環境変数が *web.FolderProfile.config* 内でフォルダーの発行プロファイルに対して設定されています。</span><span class="sxs-lookup"><span data-stu-id="a8f48-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="a8f48-123">プロファイルが *FolderProfile* であった場合、変換が適用されます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```console
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="a8f48-124">プロファイル名の MSBuild プロパティは `$(PublishProfile)` です。</span><span class="sxs-lookup"><span data-stu-id="a8f48-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="a8f48-125">プロファイルが渡されなかった場合、既定のプロファイル名は **FileSystem** です。また、アプリのコンテンツのルートにそのファイルが存在していた場合、*web.FileSystem.config* が適用されます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="a8f48-126">環境</span><span class="sxs-lookup"><span data-stu-id="a8f48-126">Environment</span></span>

<span data-ttu-id="a8f48-127">環境の変換は、[ビルド構成](#build-configuration)および[プロファイル](#profile)の変換の後、3 番目に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="a8f48-128">*web.config* の変換を必要とする[環境](xref:fundamentals/environments)ごとに、*web.{ENVIRONMENT}.config* ファイルを含めます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="a8f48-129">次の例では、環境固有の環境変数が *web.Production.config* 内で Production 環境に対して設定されています。</span><span class="sxs-lookup"><span data-stu-id="a8f48-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="a8f48-130">環境が *Production* だった場合、変換が適用されます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-130">The transform is applied when the environment is *Production*:</span></span>

```console
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="a8f48-131">環境の MSBuild プロパティは `$(EnvironmentName)` です。</span><span class="sxs-lookup"><span data-stu-id="a8f48-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="a8f48-132">Visual Studio から発行プロファイルを使って発行する場合は、<xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a8f48-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="a8f48-133">環境名を指定すると、環境変数 `ASPNETCORE_ENVIRONMENT` が自動的に *web.config* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="a8f48-134">カスタム</span><span class="sxs-lookup"><span data-stu-id="a8f48-134">Custom</span></span>

<span data-ttu-id="a8f48-135">カスタム変換は、[ビルド構成](#build-configuration)、[プロファイル](#profile)、および[環境](#environment)の変換の後、最後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="a8f48-136">*web.config* の変換を必要とするカスタム構成ごとに、 *{CUSTOM_NAME}.transform* ファイルを含めます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="a8f48-137">次の例では、カスタム変換の環境変数が *custom.transform* 内で設定されています。</span><span class="sxs-lookup"><span data-stu-id="a8f48-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="a8f48-138">`CustomTransformFileName` プロパティが [dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドに渡された場合、変換が適用されます。</span><span class="sxs-lookup"><span data-stu-id="a8f48-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```console
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="a8f48-139">プロファイル名の MSBuild プロパティは `$(CustomTransformFileName)` です。</span><span class="sxs-lookup"><span data-stu-id="a8f48-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="a8f48-140">web.config の変換を回避する</span><span class="sxs-lookup"><span data-stu-id="a8f48-140">Prevent web.config transformation</span></span>

<span data-ttu-id="a8f48-141">*web.config* ファイルの変換を回避するには、MSBuild プロパティ `$(IsWebConfigTransformDisabled)` を設定します。</span><span class="sxs-lookup"><span data-stu-id="a8f48-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```console
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="a8f48-142">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a8f48-142">Additional resources</span></span>

* [<span data-ttu-id="a8f48-143">Web アプリケーション プロジェクト配置の Web.config 変換構文</span><span class="sxs-lookup"><span data-stu-id="a8f48-143">Web.config Transformation Syntax for Web Application Project Deployment</span></span>](https://go.microsoft.com/fwlink/?LinkId=301874)
* <span data-ttu-id="a8f48-144">[Visual Studio を使用する Web プロジェクト配置の Web.config 変換構文](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="a8f48-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
