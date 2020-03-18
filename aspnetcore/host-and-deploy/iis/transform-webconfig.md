---
title: web.config を変換する
author: rick-anderson
description: ASP.NET Core アプリを発行するときに web.config ファイルを変換する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: 069b9bb516644a1a722235b33d4916460488ebf2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646658"
---
# <a name="transform-webconfig"></a>web.config を変換する

作成者: [Vijay Ramakrishnan](https://github.com/vijayrkn)

次のものに基づいてアプリを発行する場合、*web.config* ファイルに対する変換を自動的に適用することができます。

* [ビルド構成](#build-configuration)
* [Profile](#profile)
* [環境](#environment)
* [カスタム](#custom)

これらの変換は、次のいずれかの *web.config* の生成シナリオで発生します。

* `Microsoft.NET.Sdk.Web` SDK によって自動的に生成された。
* 開発者によってアプリの[コンテンツ ルート](xref:fundamentals/index#content-root)に提供された。

## <a name="build-configuration"></a>[ビルド構成]

ビルド構成の変換は、最初に実行されます。

*web.config* の変換を必要とする[ビルド構成 (デバッグ|リリース)](/dotnet/core/tools/dotnet-publish#options) ごとに、*web.{CONFIGURATION}.config* ファイルを含めます。

次の例では、構成固有の環境変数が *web.Release.config* 内で設定されています。

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

構成が *Release* に設定された場合、変換が適用されます。

```dotnetcli
dotnet publish --configuration Release
```

構成の MSBuild プロパティは `$(Configuration)` です。

## <a name="profile"></a>Profile

プロファイルの変換は、[ビルド構成](#build-configuration)の変換の後、2 番目に実行されます。

*web.config* の変換を必要とするプロファイル構成ごとに、*web.{PROFILE}.config* ファイルを含めます。

次の例では、プロファイル固有の環境変数が *web.FolderProfile.config* 内でフォルダーの発行プロファイルに対して設定されています。

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

プロファイルが *FolderProfile* であった場合、変換が適用されます。

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

プロファイル名の MSBuild プロパティは `$(PublishProfile)` です。

プロファイルが渡されなかった場合、既定のプロファイル名は **FileSystem** です。また、アプリのコンテンツのルートにそのファイルが存在していた場合、*web.FileSystem.config* が適用されます。

## <a name="environment"></a>環境

環境の変換は、[ビルド構成](#build-configuration)および[プロファイル](#profile)の変換の後、3 番目に実行されます。

*web.config* の変換を必要とする[環境](xref:fundamentals/environments)ごとに、*web.{ENVIRONMENT}.config* ファイルを含めます。

次の例では、環境固有の環境変数が *web.Production.config* 内で Production 環境に対して設定されています。

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

環境が *Production* だった場合、変換が適用されます。

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

環境の MSBuild プロパティは `$(EnvironmentName)` です。

Visual Studio から発行プロファイルを使って発行する場合は、<xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment> をご覧ください。

環境名を指定すると、環境変数 `ASPNETCORE_ENVIRONMENT` が自動的に *web.config* ファイルに追加されます。

## <a name="custom"></a>カスタム

カスタム変換は、[ビルド構成](#build-configuration)、[プロファイル](#profile)、および[環境](#environment)の変換の後、最後に実行されます。

*web.config* の変換を必要とするカスタム構成ごとに、 *{CUSTOM_NAME}.transform* ファイルを含めます。

次の例では、カスタム変換の環境変数が *custom.transform* 内で設定されています。

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

`CustomTransformFileName` プロパティが [dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドに渡された場合、変換が適用されます。

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

プロファイル名の MSBuild プロパティは `$(CustomTransformFileName)` です。

## <a name="prevent-webconfig-transformation"></a>web.config の変換を回避する

*web.config* ファイルの変換を回避するには、MSBuild プロパティ `$(IsWebConfigTransformDisabled)` を設定します。

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>その他の技術情報

* [Web アプリケーション プロジェクト配置の Web.config 変換構文](/previous-versions/dd465326(v=vs.100))
* [Visual Studio を使用する Web プロジェクト配置の Web.config 変換構文](/previous-versions/aspnet/dd465326(v=vs.110))
