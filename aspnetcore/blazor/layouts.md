---
title: Blazor レイアウトの ASP.NET Core
author: guardrex
description: Blazor アプリ用の再利用可能なレイアウトコンポーネントを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/layouts
ms.openlocfilehash: 2d652e149381f0a93e3135da978ab5737d47c6f1
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "68948222"
---
# <a name="aspnet-core-blazor-layouts"></a>Blazor レイアウトの ASP.NET Core

[Rainer Stropek](https://www.timecockpit.com)

メニュー、著作権メッセージ、会社のロゴなどの一部のアプリ要素は、通常、アプリの全体的なレイアウトの一部であり、アプリのすべてのコンポーネントで使用されます。 これらの要素のコードをアプリのすべてのコンポーネントにコピーすることは、要素&mdash;の1つが更新を必要とするたびに効率的な方法ではありません。すべてのコンポーネントを更新する必要があります。 このような重複を維持することは困難であり、時間の経過と共にコンテンツの一貫性が失われる可能性があります。 *レイアウト*はこの問題を解決します。

技術的には、レイアウトは別のコンポーネントにすぎません。 レイアウトは Razor テンプレートまたはC#コードで定義され、[データバインディング](xref:blazor/components#data-binding)、[依存関係の挿入](xref:blazor/dependency-injection)、およびその他のコンポーネントのシナリオを使用できます。

*コンポーネント*を*レイアウト*に変換するには、次のコンポーネントを実行します。

* を`LayoutComponentBase`継承します。これ`Body`は、レイアウト内に表示されるコンテンツのプロパティを定義します。
* Razor 構文`@Body`を使用して、コンテンツがレンダリングされるレイアウトマークアップ内の場所を指定します。

次のコードサンプルは、レイアウトコンポーネントの Razor テンプレートである*Mainlayout*を示しています。 レイアウトは、 `LayoutComponentBase`ナビゲーションバーと`@Body`フッターの間のを継承して設定します。

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="specify-a-layout-in-a-component"></a>コンポーネントでのレイアウトの指定

コンポーネントにレイアウトを`@layout`適用するには、Razor ディレクティブを使用します。 コンパイラは、 `@layout`を`LayoutAttribute`に変換します。これは、コンポーネントクラスに適用されます。

次のコンポーネントのコンテンツ ( *masterlist*) が、 `MainLayout`の`@Body`位置に挿入されます。

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a>一元的なレイアウト選択

アプリのすべてのフォルダーには、必要に応じて、*インポート*という名前のテンプレートファイルを含めることができます。 コンパイラには、インポートファイルで指定されたディレクティブが同じフォルダー内のすべての Razor テンプレートに含まれ、そのすべてのサブフォルダーに再帰的に含まれています。 したがって、を含む`@layout MainLayout`インポートの razor ファイルを使用すると、フォルダー内のすべてのコンポーネントでが使用`MainLayout`されるようになります。 フォルダーおよびサブフォルダー内のすべて`@layout MainLayout`の*razor*ファイルに繰り返し追加する必要はありません。 `@using`ディレクティブは、同じ方法でコンポーネントにも適用されます。

次のインポートを実行し*ます。 razor ファイルのインポート (_c)* :

* `MainLayout`。
* 同じフォルダーおよびサブフォルダー内のすべての Razor コンポーネント。
* `BlazorApp1.Data` 名前空間。
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

Razorファイルは、razor の[ビューとページの _ViewImports ファイル](xref:mvc/views/layout#importing-shared-directives)に似ていますが、razor コンポーネントファイルに特に適用されます。

Blazor テンプレートでは、レイアウトを選択するために、 *razor*ファイルが使用されます。 Blazor テンプレートから作成されたアプリには、プロジェクトのルートと*Pages*フォルダーにある*インポートの razor*ファイルが含まれています。

## <a name="nested-layouts"></a>入れ子になったレイアウト

アプリは、入れ子になったレイアウトで構成できます。 コンポーネントは、別のレイアウトを参照するレイアウトを参照できます。 たとえば、入れ子になったレイアウトは、複数レベルのメニュー構造を作成するために使用されます。

次の例は、入れ子になったレイアウトの使用方法を示しています。 *EpisodesComponent*ファイルは、表示するコンポーネントです。 コンポーネントはを参照`MasterListLayout`します。

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

*Masterlistlayout. razor*ファイルには、 `MasterListLayout`が用意されています。 レイアウトは、表示され`MasterLayout`ている別のレイアウト () を参照します。 `EpisodesComponent``@Body`が表示されます。

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

最後に`MasterLayout` 、 *masterlayout*には、ヘッダー、メインメニュー、フッターなどの最上位レベルのレイアウト要素が含まれています。 `MasterListLayout`が`EpisodesComponent`表示される`@Body`と、が表示されます。

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>その他の資料

* <xref:mvc/views/layout>
