---
title: ファイル ウォッチャーを使用した ASP.NET Core アプリの開発
author: rick-anderson
description: このチュートリアルでは、.NET Core CLI のファイル ウォッチャー (dotnet watch) ツールをインストールし、ASP.NET Core アプリで使用する方法について説明します。
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: bedb3e6a65839db915ca7bc821a267a14d34bf30
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650960"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>ファイル ウォッチャーを使用した ASP.NET Core アプリの開発

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) と [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

[dotnet watch](https://www.nuget.org/packages/dotnet-watch) は、ソース ファイルの変更時に [.NET Core CLI](/dotnet/core/tools) コマンドを実行するツールです。 たとえば、あるファイルを変更すると、コンパイル、テストの実行、展開が開始されます。

このチュートリアルでは、エンドポイントが 2 つの既存の Web API を利用します。合計を返すエンドポイントと積を返すエンドポイントです。 積のメソッドにはバグがあり、このチュートリアルで修正します。

[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)をダウンロードしてください。 これには次の 2 つのプロジェクトが含まれています。*WebApp* (ASP.NET Core Web API) および *WebAppTests* (Web API の単体テスト)。

コマンド シェルで、*WebApp* フォルダーに移動します。 次のコマンドを実行します。

```dotnetcli
dotnet run
```

> [!NOTE]
> `dotnet run --project <PROJECT>` を使用して、実行するプロジェクトを指定することができます。 たとえば、サンプル アプリのルートから `dotnet run --project WebApp` を実行すると、*WebApp* プロジェクトも実行されます。

コンソール出力に、次のようなメッセージが表示されます。アプリが実行中であり、要求を待っていることを示しています。

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Web ブラウザーで、`http://localhost:<port number>/api/math/sum?a=4&b=5` に移動します。 結果として `9` が表示されます。

製品 API に移動します (`http://localhost:<port number>/api/math/product?a=4&b=5`)。 予想していた `20` ではなく、`9` が返されます。 この問題は、チュートリアルで後ほど修正します。

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>`dotnet watch` をプロジェクトに追加する

`dotnet watch` ファイル ウォッチャー ツールは、.NET Core SDK のバージョン 2.1.300 に付属しています。 これより前のバージョンの .NET Core SDK を使用する場合は、次の手順が必要です。

1. `Microsoft.DotNet.Watcher.Tools` パッケージ参照を *.csproj* ファイルに追加します。

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. 次のコマンドを実行して `Microsoft.DotNet.Watcher.Tools` パッケージをインストールします。

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>`dotnet watch` を使用した .NET Core CLI コマンドの実行

[.NET Core CLI コマンド](/dotnet/core/tools#cli-commands) はいずれも、`dotnet watch` との組み合わせで実行することができます。 次に例を示します。

| コマンド | コマンドと watch |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f netcoreapp2.0 | dotnet watch run -f netcoreapp2.0 |
| dotnet run -f netcoreapp2.0 -- --arg1 | dotnet watch run -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet watch test |

*WebApp* フォルダーの `dotnet watch run` を実行します。 コンソール出力に、`watch` が起動したことが示されます。

> [!NOTE]
> `dotnet watch --project <PROJECT>` を使用して、ウォッチするプロジェクトを指定することができます。 たとえば、サンプル アプリのルートから `dotnet watch --project WebApp run` を実行すると、*WebApp* プロジェクトも実行されてウォッチされます。

## <a name="make-changes-with-dotnet-watch"></a>`dotnet watch` で変更を行う

`dotnet watch` が実行されていることを確認します。

*MathController.cs* の `Product` メソッドのバグを修正して、合計ではなく積を返すようにします。

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

ファイルを保存します。 コンソール出力により、`dotnet watch` がファイル変更を検出し、アプリを再起動したことが表示されます。

`http://localhost:<port number>/api/math/product?a=4&b=5` が正しい結果を返すことを確認します。

## <a name="run-tests-using-dotnet-watch"></a>`dotnet watch` を使用してテストを実行する

1. *MathController.cs* の `Product` メソッドを元に戻して合計を返すようにします。 ファイルを保存します。
1. コマンド シェルで、*WebAppTests* フォルダーに移動します。
1. [dotnet restore](/dotnet/core/tools/dotnet-restore) を実行します。
1. `dotnet watch test` を実行します。 テストに失敗し、ウォッチャーがファイル変更を待っていることが出力に示されます。

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. 積を返すように `Product` メソッドのコードを修正します。 ファイルを保存します。

`dotnet watch` はファイル変更を検出し、テストを再実行します。 コンソール出力にテストの合格が示されます。

## <a name="customize-files-list-to-watch"></a>監視するファイル リストのカスタマイズ

既定では、`dotnet-watch` は次の glob パターンに一致するすべてのファイルを追跡します。

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

ウォッチ リストに他の項目を追加するには、 *.csproj* ファイルを編集します。 項目は個別に指定することも、glob パターンを使用して指定することもできます。

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>ウォッチするファイルのオプトアウト

既定の設定を無視するように `dotnet-watch` を構成することができます。 特定のファイルを無視するには、 *.csproj* ファイルで項目の定義に `Watch="false"` 属性を追加します。

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a>カスタム ウォッチ プロジェクト

`dotnet-watch` は C# プロジェクトだけに限定されていません。 カスタム ウォッチ プロジェクトは、さまざまなシナリオを処理するために作成できます。 次のプロジェクト レイアウトを考えてみましょう。

* **test/**
  * *UnitTests/UnitTests.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

両方のプロジェクトを監視するのが目的である場合、両方のプロジェクトを監視するように構成されたカスタム プロジェクト ファイルを作成します。

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

両方のプロジェクトでファイルの監視を開始するには、*test* フォルダーに変更します。 次のコマンドを実行します。

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest は、いずれかのテスト プロジェクトでファイルが変更されたときに実行されます。

## <a name="dotnet-watch-in-github"></a>GitHub での `dotnet-watch`

`dotnet-watch` は GitHub の [aspnet/AspNetCore リポジトリ](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)に含まれています。
