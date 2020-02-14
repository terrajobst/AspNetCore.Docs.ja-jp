---
title: Blazor レイアウトの ASP.NET Core
author: guardrex
description: Blazor アプリ用の再利用可能なレイアウトコンポーネントを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 8e7294f6b66d34781473522a71f929ed5f9c33f2
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213377"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>Blazor レイアウトの ASP.NET Core

By [Rainer Stropek](https://www.timecockpit.com)と[Luke latham](https://github.com/guardrex)

メニュー、著作権メッセージ、会社のロゴなどの一部のアプリ要素は、通常、アプリの全体的なレイアウトの一部であり、アプリのすべてのコンポーネントで使用されます。 これらの要素のコードをアプリのすべてのコンポーネントにコピーするのは、いずれかの要素に更新が必要な場合に、すべてのコンポーネントを更新する必要があるので&mdash;効率的な方法ではありません。 このような重複を維持することは困難であり、時間の経過と共にコンテンツの一貫性が失われる可能性があります。 *レイアウト*はこの問題を解決します。

技術的には、レイアウトは別のコンポーネントにすぎません。 レイアウトは Razor テンプレートまたはC#コードで定義され、[データバインディング](xref:blazor/components#data-binding)、[依存関係の挿入](xref:blazor/dependency-injection)、およびその他のコンポーネントのシナリオを使用できます。

*コンポーネント*を*レイアウト*に変換するには、次のコンポーネントを実行します。

* レイアウト内に表示されるコンテンツの `Body` プロパティを定義する `LayoutComponentBase`から継承します。
* Razor 構文 `@Body` を使用して、コンテンツがレンダリングされるレイアウトマークアップ内の場所を指定します。

次のコードサンプルは、レイアウトコンポーネントの Razor テンプレートである*Mainlayout*を示しています。 レイアウトは `LayoutComponentBase` を継承し、ナビゲーションバーとフッターの間に `@Body` を設定します。

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Blazor アプリテンプレートのいずれかに基づくアプリでは、`MainLayout` コンポーネント (*Mainlayout*) はアプリの*共有*フォルダーにあります。

## <a name="default-layout"></a>既定のレイアウト

アプリの*アプリケーションの razor*ファイルの `Router` コンポーネントで、既定のアプリレイアウトを指定します。 既定の Blazor テンプレートによって提供される次の `Router` コンポーネントは、既定のレイアウトを `MainLayout` コンポーネントに設定します。

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

`NotFound` コンテンツの既定のレイアウトを指定するには `NotFound` コンテンツの `LayoutView` を指定します。

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

`Router` コンポーネントの詳細については、「<xref:blazor/routing>」を参照してください。

ルーターでレイアウトを既定のレイアウトとして指定することは、コンポーネントごとまたはフォルダーごとにオーバーライドできるため、便利な方法です。 最も一般的な方法であるため、ルーターを使用してアプリの既定のレイアウトを設定することをお勧めします。

## <a name="specify-a-layout-in-a-component"></a>コンポーネントでのレイアウトの指定

コンポーネントにレイアウトを適用するには、Razor ディレクティブ `@layout` を使用します。 コンパイラは `@layout` を `LayoutAttribute`に変換します。これはコンポーネントクラスに適用されます。

次の `MasterList` コンポーネントの内容は、`@Body`の位置にある `MasterLayout` に挿入されます。

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

コンポーネントにレイアウトを直接指定すると、ルーターの*既定のレイアウト*セットまたは *_Imports*からインポートされた `@layout` ディレクティブが上書きされます。

## <a name="centralized-layout-selection"></a>一元的なレイアウト選択

アプリのすべてのフォルダーには、必要に応じて *_Imports*という名前のテンプレートファイルを含めることができます。 コンパイラには、インポートファイルで指定されたディレクティブが同じフォルダー内のすべての Razor テンプレートに含まれ、そのすべてのサブフォルダーに再帰的に含まれています。 したがって、`@layout MyCoolLayout` を含む *_Imports razor*ファイルは、フォルダー内のすべてのコンポーネントが `MyCoolLayout`を使用することを保証します。 フォルダーおよびサブフォルダー内のすべての*razor*ファイルに `@layout MyCoolLayout` を繰り返し追加する必要はありません。 `@using` ディレクティブも、同じ方法でコンポーネントに適用されます。

次の *_Imports の razor*ファイルのインポート:

* [https://login.microsoftonline.com/consumers/](`MyCoolLayout`)
* 同じフォルダーおよびサブフォルダー内のすべての Razor コンポーネント。
* `BlazorApp1.Data` 名前空間。
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

*_Imports の razor*ファイルは、razor[のビューおよびページの _ViewImports ファイル](xref:mvc/views/layout#importing-shared-directives)に似ていますが、razor コンポーネントファイルに特に適用されます。

*_Imports*でレイアウトを指定すると、ルーターの*既定のレイアウト*として指定されたレイアウトが上書きされます。

## <a name="nested-layouts"></a>入れ子になったレイアウト

アプリは、入れ子になったレイアウトで構成できます。 コンポーネントは、別のレイアウトを参照するレイアウトを参照できます。 たとえば、入れ子になったレイアウトは、複数レベルのメニュー構造を作成するために使用されます。

次の例は、入れ子になったレイアウトの使用方法を示しています。 *EpisodesComponent*ファイルは、表示するコンポーネントです。 コンポーネントは `MasterListLayout`を参照します。

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

*Masterlistlayout. razor*ファイルには、`MasterListLayout`が用意されています。 レイアウトは、表示されている別のレイアウト (`MasterLayout`) を参照します。 `@Body` 表示される `EpisodesComponent` がレンダリングされます。

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

最後に、Masterlayout に `MasterLayout`、ヘッダー、メインメニュー、フッターなどの最上位レベルのレイアウト要素が含まれてい*ます。* `EpisodesComponent` が表示される `MasterListLayout`、`@Body` が表示されます。

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Razor Pages レイアウトを統合コンポーネントと共有する

ルーティング可能なコンポーネントが Razor Pages アプリに統合されている場合、アプリの共有レイアウトをコンポーネントで使用できます。 詳細については、<xref:blazor/hosting-model-configuration#integrate-razor-components-into-razor-pages-and-mvc-apps> を参照してください。

## <a name="additional-resources"></a>その他のリソース

* <xref:mvc/views/layout>
