---
title: ASP.NET Core の分散キャッシュ タグ ヘルパー
author: pkellner
description: 分散キャッシュ タグ ヘルパーを使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: f5957adf3cef8966812a1bf0cbc6b2627d19d026
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653792"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>ASP.NET Core の分散キャッシュ タグ ヘルパー

著者: [Peter Kellner](https://peterkellner.net)

分散キャッシュ タグ ヘルパーは、ASP.NET Core アプリの内容を分散キャッシュ ソースにキャッシュすることによって、アプリのパフォーマンスを大幅に改善する機能を提供します。

タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。

分散キャッシュ タグ ヘルパーは、キャッシュ タグ ヘルパーと同じ基本クラスから継承されます。 すべての[キャッシュ タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)属性は分散タグ ヘルパーで使用できます。

分散キャッシュ タグ ヘルパーでは、[コンストラクターの挿入](xref:fundamentals/dependency-injection#constructor-injection-behavior)が使用されます。 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスは、分散キャッシュ タグ ヘルパーのコンストラクターに渡されます。 `IDistributedCache` (`Startup.ConfigureServices`Startup.cs *) に*  の具体的な実装が作成されていない場合、分散キャッシュ タグ ヘルパーでは、キャッシュされたデータの格納に[キャッシュ タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)と同じメモリ内プロバイダーが使用されます。

## <a name="distributed-cache-tag-helper-attributes"></a>分散キャッシュ タグ ヘルパーの属性

### <a name="attributes-shared-with-the-cache-tag-helper"></a>キャッシュ タグ ヘルパーと共有される属性

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

分散キャッシュ タグ ヘルパーは、キャッシュ タグ ヘルパーと同じクラスから継承されます。 これらの属性に関する説明については、[キャッシュ タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)に関するページをご覧ください。

### <a name="name"></a>name

| 属性の種類 | 例                               |
| -------------- | ------------------------------------- |
| String         | `my-distributed-cache-unique-key-101` |

`name` は必須です。 格納された各キャッシュ インスタンスごとにキーとして `name` 属性が使用されます。 キャッシュ タグ ヘルパーは Razor ページ名と、Razor ページ内での位置に基づいて各インスタンスにキャッシュ キーを割り当てますが、分散キャッシュ タグ ヘルパーのキーが基準とするのは属性 `name` のみです。

例:

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>分散キャッシュ タグ ヘルパー IDistributedCache の実装

ASP.NET Core には <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の 2 つの実装が組み込まれています。 1 つは SQL Server を、もう 1 つは Redis をベースにしています。 [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) など、サードパーティの実装も利用できます。 これらの実装の詳細については、<xref:performance/caching/distributed> を参照してください。 どちらの実装も、`IDistributedCache` での `Startup` インスタンスの設定を伴います。

`IDistributedCache` のいずれかの具体的な実装の使用に、明確に関連付けられている属性はありません。

## <a name="additional-resources"></a>その他のリソース

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
