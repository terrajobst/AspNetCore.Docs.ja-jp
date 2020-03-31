---
title: ASP.NET Core Razor コンポーネント クラス ライブラリ
author: guardrex
description: 外部コンポーネント ライブラリから、コンポーネントを Blazor アプリに含める方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218767"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core Razor コンポーネント クラス ライブラリ

作成者: [Simon Timms](https://github.com/stimms)

コンポーネントは、[Razor クラス ライブラリ (RCL)](xref:razor-pages/ui-class) 内でプロジェクト間で共有できます。 *Razor コンポーネント クラス ライブラリ*は、次から含めることができます。

* ソリューションの別のプロジェクト。
* NuGet パッケージ。
* 参照されている .NET ライブラリ。

コンポーネントが通常の .NET 型であるのと同様に、RCL によって提供されるコンポーネントは通常の .NET アセンブリです。

## <a name="create-an-rcl"></a>RCL を作成する

<xref:blazor/get-started> の記事のガイダンスに従って、Blazor 用の環境を構成します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 新しいプロジェクトを作成します。
1. **[Razor クラス ライブラリ]** を選択します。 **[次へ]** を選択します。
1. **[Create a new Razor class library]** (新しい Razor クラスライブラリの作成) ダイアログで、 **[作成]** を選択します。
1. **[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。 このトピックの例では、プロジェクト名 `MyComponentLib1` を使用します。 **[作成]** を選択します。
1. RCL をソリューションに追加します。
   1. ソリューションを右クリックします。 **[追加]**  >  **[既存のプロジェクト]** を選択します。
   1. RCL のプロジェクト ファイルに移動します。
   1. RCL のプロジェクト ファイル ( *.csproj*) を選択します。
1. アプリから RCL の参照を追加します。
   1. アプリ プロジェクトを右クリックします。 **[追加]**  >  **[参照]** の順に選択します。
   1. RCL プロジェクトを選択します。 **[OK]** を選択します。

> [!NOTE]
> テンプレートから RCL を生成するときに **[ページとビューのサポート]** チェック ボックスがオンになっている場合は、次のコンテンツで生成されたプロジェクトのルートに、 *_Imports.razor* ファイルも追加して、Razor コンポーネントの作成を有効にします。
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> 生成されたプロジェクトのルートにファイルを手動で追加します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. コマンド シェルで [dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用して、**Razor クラス ライブラリ** テンプレート (`razorclasslib`) を使用します。 次の例では、`MyComponentLib1` という名前の RCL が作成されます。 コマンドの実行時に、`MyComponentLib1` を保持するフォルダーが自動的に作成されます。

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > テンプレートから RCL を生成するときに、`-s|--support-pages-and-views` スイッチが使用されている場合、次のコンテンツで生成されたプロジェクトのルートに、 *_Imports.razor* ファイルも追加して、Razor コンポーネントの作成を有効にします。
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > 生成されたプロジェクトのルートにファイルを手動で追加します。

1. 既存のプロジェクトにライブラリを追加するには、コマンド シェルで [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) コマンドを使用します。 次の例では、RCL がアプリに追加されています。 ライブラリへのパスを使用して、アプリのプロジェクト フォルダーから次のコマンドを実行します。

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>ライブラリ コンポーネントの使用

別のプロジェクトのライブラリに定義されているコンポーネントを使用するには、次のいずれかの方法を使用します。

* 名前空間と完全な型名を使用します。
* Razor の [\@using](xref:mvc/views/razor#using) ディレクティブを使用します。 個々のコンポーネントを名前で追加することができます。

次の例で、`MyComponentLib1` は `SalesReport` コンポーネントを含むコンポーネント ライブラリです。

名前空間と完全な型名を使用して、`SalesReport` コンポーネントを参照できます。

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

また、ライブラリが `@using` ディレクティブを使用して、スコープ内に取り込まれている場合も、このコンポーネントを参照できます。

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

プロジェクト全体でライブラリのコンポーネントを使用できるようにするには、最上位の *_Import.razor* ファイルに `@using MyComponentLib1` ディレクティブを含めます。 ディレクティブを任意のレベルの *_Import.razor* ファイルに追加して、名前空間をフォルダー内の 1 つまたは複数のページに適用します。

## <a name="create-a-razor-components-class-library-with-static-assets"></a>静的アセットを含む Razor コンポーネント クラスライブラリを作成する

RCL には、静的アセットを含めることができます。 静的アセットは、ライブラリを使用するすべてのアプリで使用できます。 詳細については、「<xref:razor-pages/ui-class#create-an-rcl-with-static-assets>」を参照してください。

## <a name="build-pack-and-ship-to-nuget"></a>ビルド、パック、NuGet への配布

コンポーネント ライブラリは標準 .NET ライブラリであるため、それらをパッケージ化して NuGet に配布することは、ライブラリをパッケージ化して NuGet に配布する場合と変わりはありません。 パッケージ化は、コマンド シェルで [dotnet pack](/dotnet/core/tools/dotnet-pack) コマンドを使用して実行します。

```dotnetcli
dotnet pack
```

コマンド シェルで [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) コマンドを使用して、パッケージを NuGet にアップロードします。

## <a name="additional-resources"></a>その他の技術情報

* <xref:razor-pages/ui-class>
* [XML リンカー構成ファイルをライブラリに追加する](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
