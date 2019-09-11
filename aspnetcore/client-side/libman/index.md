---
title: LibMan を使用した ASP.NET Core でのクライアント側ライブラリの取得
author: scottaddie
description: ライブラリ マネージャー (LibMan) を使用して、ASP.NET Core プロジェクトにクライアント側ライブラリの資産をインストールする方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878331"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>LibMan を使用した ASP.NET Core でのクライアント側ライブラリの取得

作成者: [Scott Addie](https://twitter.com/Scott_Addie)

ライブラリ マネージャー (LibMan) は、軽量なクライアント側ライブラリ取得ツールです。 LibMan は、人気のあるライブラリとフレームワークをファイル システムまたは[コンテンツ配信ネットワーク (CDN)](https://wikipedia.org/wiki/Content_delivery_network) からダウンロードします。 サポートされる CDN には、[CDNJS](https://cdnjs.com/)、[jsDelivr](https://www.jsdelivr.com/)、および [unpkg](https://unpkg.com/#/) が含まれます。 選択したライブラリ ファイルが取り込まれ、ASP.NET Core プロジェクト内の適切な場所に配置されます。

## <a name="libman-use-cases"></a>LibMan のユース ケース

LibMan には次のような利点があります。

* 必要なライブラリ ファイルのみがダウンロードされます。
* [Node.js](https://nodejs.org)、[npm](https://www.npmjs.com)、[WebPack](https://webpack.js.org) などの追加ツールは、ライブラリ内のファイルのサブセットを取得するためには必要ありません。
* ビルド タスクや手動でのファイル コピーを実行しなくても、ファイルを特定の場所に置くことができます。

LibMan の利点について詳しくは、「[Modern front-end web development in Visual Studio 2017: LibMan segment (Visual Studio 2017 での最新のフロントエンド Web 開発: LibMan セグメント)」](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s)をご覧ください。

LibMan はパッケージ管理システムではありません。 npm や [yarn](https://yarnpkg.com) などのパッケージ マネージャーを既に使用している場合は、引き続き使用してください。 LibMan は、このようなツールの置き換えとして開発されたものではありません。

## <a name="additional-resources"></a>その他の技術情報

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [LibMan の GitHub リポジトリ](https://github.com/aspnet/LibraryManager)
