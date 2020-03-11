---
title: ASP.NET Core のイメージ タグ ヘルパー
author: pkellner
description: イメージ タグ ヘルパーを使用する方法を示します。
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 964072ad276f7e3e411ee41cb03a2efb9d05c585
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653774"
---
# <a name="image-tag-helper-in-aspnet-core"></a>ASP.NET Core のイメージ タグ ヘルパー

著者: [Peter Kellner](https://peterkellner.net)

イメージ タグ ヘルパーは `<img>` タグを強化し、静的イメージ ファイルにキャッシュ バスティング動作を提供します。

キャッシュ バスティング文字列は、アセットの URL に追加される、静的なイメージ ファイルのハッシュを表す一意の値です。 一意の文字列は、クライアントのキャッシュからではなく、ホスト Web サーバーからイメージの再読み込みを行うよう、クライアント (および一部のプロキシ) に要求します。

イメージ ソース (`src`) がホスト Web サーバー上の静的ファイルの場合:

* 一意のキャッシュ バスティング文字列は、クエリ パラメーターとしてイメージ ソースに追加されます。
* ホスト Web サーバーのファイルが変更された場合、更新された要求パラメーターを含む一意の要求 URL が確実に生成されます。

タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。

## <a name="image-tag-helper-attributes"></a>イメージ タグ ヘルパーの属性

### <a name="src"></a>src

イメージ タグ ヘルパーをアクティブにするには、`src` 要素に `<img>` 属性が必要です。

イメージ ソース (`src`) は、サーバー上の物理静的ファイルをポイントしている必要があります。 `src` がリモート URI の場合は、キャッシュ バスティング クエリ文字列パラメーターは生成されません。

### <a name="asp-append-version"></a>asp-append-version

`asp-append-version` 属性の値が `true` で、`src` 属性も指定されている場合、イメージ タグ ヘルパーが呼び出されます。

イメージ タグ ヘルパーを使用する例を次に示します。

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

静的ファイルがディレクトリ */wwwroot/images/* に存在する場合、生成された HTML は次のようになります (ハッシュは異なります)。

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

パラメーター `v` に割り当てられた値は、ディスク上の *asplogo.png* ファイルのハッシュ値です。 Web サーバーが静的ファイルに対する読み取りアクセスを取得できない場合、`v` パラメーターは表示されるマークアップの `src` 属性に追加されません。

## <a name="hash-caching-behavior"></a>ハッシュ キャッシュの動作

イメージ タグ ヘルパーはローカル Web サーバーでキャッシュ プロバイダーを使用して、特定のファイルの計算された `Sha512` ハッシュを格納します。 ファイルが複数回要求された場合、ハッシュは再計算されません。 キャッシュは、ファイルの `Sha512` ハッシュが計算されたときにファイルにアタッチされるファイル ウォッチャーによって無効になります。 ディスク上のファイルが変更されると、新しいハッシュが計算されてキャッシュされます。

## <a name="additional-resources"></a>その他のリソース

* <xref:performance/caching/memory>
