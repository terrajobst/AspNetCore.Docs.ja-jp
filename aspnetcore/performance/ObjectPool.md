---
title: ASP.NET Core で ObjectPool でオブジェクトを再利用
author: rick-anderson
description: ObjectPool を使用して ASP.NET Core アプリでのパフォーマンスを高めるためのヒント。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815526"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>ASP.NET Core で ObjectPool でオブジェクトを再利用

作成者: [Steve Gordon](https://twitter.com/stevejgordon)、 [Ryan Nowak](https://github.com/rynowak)、および[Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool> 収集されたガベージ オブジェクトではなく、再利用するためのメモリ内オブジェクトのグループを維持することをサポートする ASP.NET Core インフラストラクチャの一部です。

管理されているオブジェクトがある場合は、オブジェクト プールを使用する場合があります。

- 負荷の高いを割り当てまたは初期化します。
- いくつか制限されているリソースを表します。
- 予測的かつ頻繁に使用されます。

たとえば、ASP.NET Core フレームワークを使用してオブジェクト プールいくつかの場所で再利用する<xref:System.Text.StringBuilder>インスタンス。 `StringBuilder` 割り当てられ、文字データを保持するバッファーを管理します。 ASP.NET Core を使用して定期的に`StringBuilder`機能を実装して、再利用することにより、パフォーマンスが向上します。

オブジェクト プールは、パフォーマンスを向上することでは常に。

- オブジェクトの初期化コストが高でない限りは、通常、プールからオブジェクトの取得に時間がかかります。
- プールで管理されるオブジェクトは、プールが割り当て解除済みになるまで割り当て解除済みではありません。

オブジェクトは、現実的なシナリオを使用して、アプリまたはライブラリのパフォーマンス データを収集した後でのみプールを使用します。

**警告:`ObjectPool`を実装していない`IDisposable`します。破棄が必要な型で使用することお勧めしません。**

**注:割り当てるにはオブジェクトの数に制限を設定しない、ObjectPool が保持されますオブジェクトの数に制限がかかります。**

## <a name="concepts"></a>概念

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -基本的なオブジェクト プールの抽象化します。 取得し、オブジェクトを返すために使用します。

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -このオブジェクトを作成する方法と方法をカスタマイズする機能を実装*リセット*プールに返されるとします。 これは、オブジェクト プールを直接構築することに渡すことができます.OR

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> オブジェクト プールを作成するためのファクトリとして機能します。
<!-- REview, there is no ObjectPoolProvider<T> -->

ObjectPool は、複数の方法でのアプリで使用できます。

* プールをインスタンス化します。
* 内のプールを登録する[依存関係の注入](xref:fundamentals/dependency-injection)(DI) としてインスタンス。
* 登録、 `ObjectPoolProvider<>` DI およびファクトリとして使用します。

## <a name="how-to-use-objectpool"></a>ObjectPool を使用する方法

呼び出す<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>オブジェクトを取得し、<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*>オブジェクトを取得します。  要件のすべてのオブジェクトを返すことはありません。 オブジェクトを返さない、ガベージ コレクションことができます。

## <a name="objectpool-sample"></a>ObjectPool サンプル

コード例を次に示します。

* 追加`ObjectPoolProvider`を[依存関係の注入](xref:fundamentals/dependency-injection)(DI) コンテナー。
* 設定を追加して`ObjectPool<StringBuilder>`DI コンテナーにします。
* 追加、`BirthdayMiddleware`します。

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

次のコードを実装します。 `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
