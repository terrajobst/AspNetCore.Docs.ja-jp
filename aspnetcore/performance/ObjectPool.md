---
title: ASP.NET Core の ObjectPool を使用したオブジェクトの再利用
author: rick-anderson
description: ObjectPool を使用して ASP.NET Core アプリのパフォーマンスを向上させるためのヒントです。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654380"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>ASP.NET Core の ObjectPool を使用したオブジェクトの再利用

([上田 Gordon](https://twitter.com/stevejgordon)、[ライアン Nowak](https://github.com/rynowak)、 [Rick Anderson](https://twitter.com/RickAndMSFT) )

<xref:Microsoft.Extensions.ObjectPool> は、オブジェクトのガベージコレクションを許可するのではなく、オブジェクトのグループをメモリに保持して再利用できるようにする ASP.NET Core インフラストラクチャの一部です。

管理されているオブジェクトが次のような場合に、オブジェクトプールを使用することができます。

- 割り当て/初期化にコストがかかります。
- 限られたリソースを表します。
- 予測と頻繁に使用されます。

たとえば、ASP.NET Core framework は、<xref:System.Text.StringBuilder> インスタンスを再利用するために、いくつかの場所でオブジェクトプールを使用します。 `StringBuilder` は、文字データを保持する独自のバッファーを割り当てて管理します。 ASP.NET Core は `StringBuilder` を定期的に使用して機能を実装し、再利用することでパフォーマンスが向上します。

オブジェクトプーリングでは、常にパフォーマンスが向上するわけではありません。

- オブジェクトの初期化コストが高い場合を除き、通常は、プールからオブジェクトを取得する方が遅くなります。
- プールによって管理されるオブジェクトは、プールが割り当て解除されるまで割り当てられません。

オブジェクトプールは、アプリまたはライブラリの現実的なシナリオを使用してパフォーマンスデータを収集した後にのみ使用してください。

**警告: `ObjectPool` は `IDisposable`を実装していません。破棄が必要な型では使用しないことをお勧めします。**

**注: ObjectPool では、割り当てられるオブジェクトの数に制限は設定されません。これにより、保持するオブジェクトの数に制限が適用されます。**

## <a name="concepts"></a>概念

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-基本的なオブジェクトプールの抽象化です。 オブジェクトを取得して返すために使用します。

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-これを実装して、オブジェクトの作成方法と、プールに返されたときの*リセット*方法をカスタマイズします。 これは、直接構築するオブジェクトプールに渡すことができます...もしくは

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> は、オブジェクトプールを作成するためのファクトリとして機能します。
<!-- REview, there is no ObjectPoolProvider<T> -->

ObjectPool は、次のような複数の方法でアプリで使用できます。

* プールをインスタンス化しています。
* [依存関係の挿入](xref:fundamentals/dependency-injection)(DI) にプールをインスタンスとして登録する。
* `ObjectPoolProvider<>` を DI に登録し、ファクトリとして使用します。

## <a name="how-to-use-objectpool"></a>ObjectPool の使用方法

オブジェクトを取得し、オブジェクトを返すために <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> を呼び出します。  すべてのオブジェクトを返す必要はありません。 オブジェクトを返さない場合は、ガベージコレクションが実行されます。

## <a name="objectpool-sample"></a>ObjectPool サンプル

次のコードは、次の処理を実行します。

* [依存関係挿入](xref:fundamentals/dependency-injection)(DI) コンテナーに `ObjectPoolProvider` を追加します。
* DI コンテナーに `ObjectPool<StringBuilder>` を追加して構成します。
* `BirthdayMiddleware`を追加します。

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

次のコードでは、を実装してい `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
