---
title: SignalR の API の設計に関する考慮事項
author: anurse
description: アプリのバージョン間で互換性のための SignalR の Api を設計する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/api-design
ms.openlocfilehash: 3f17bf055b793e8fc91fbcc15f668928ca261f77
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897809"
---
# <a name="signalr-api-design-considerations"></a>SignalR の API の設計に関する考慮事項

作成者: [Andrew Stanton-Nurse](https://twitter.com/anurse)

この記事では、SignalR ベースの API を構築するためのガイダンスを提供します。

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>カスタム オブジェクトのパラメーターを使用して、下位互換性を確保するには

(クライアントまたはサーバー上の) SignalR ハブ メソッドにパラメーターの追加することは、 *互換性に影響がある変更* です。 これは、適切な数のパラメーターのないメソッドを呼び出そうとすると、以前のクライアント/サーバーはエラーが発生することを意味します。 ただし、カスタムオブジェクトのパラメーターにプロパティを追加することは互換性に影響が**ありません**。 クライアントまたはサーバー上の変更に対応する互換性のある API を設計するために使用できます。

たとえば、次のようにサーバー側 API があるとします。

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

JavaScript クライアントは、このメソッドを使用してを呼び出す`invoke`次のようにします。

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

後でサーバー メソッドに 2 番目のパラメーターを追加する場合、古いクライアントは、このパラメーターの値を指定しません。 例:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

古いクライアントがこのメソッドを呼び出す際は、このようなエラーが表示されます。

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

サーバーでは、このようなログ メッセージが表示されます。

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

古いクライアントでは、1 つのパラメーターのみ送信されるが、新しいサーバー API には 2 つのパラメーターが必要です。 カスタムオブジェクトをパラメーターとして使用すると柔軟性が向上します。 カスタムオブジェクトを使用して元の API のデザインを変更しましょう。

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

そして、クライアントではメソッドの呼び出しにオブジェクトを使用します。

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

パラメーターを追加する代わりに、`TotalLengthRequest`オブジェクトにプロパティを追加します。

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

古いクライアントが1つのパラメーターを送信するときは、`Param2`プロパティは`null`になります。`Param2`が`null`であることをチェックして古いクライアントから送信されたメッセージであることを検出し、既定値を適用することができます。 新しいクライアントは、両方のパラメーターを送信できます。

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

同じ手法では、クライアントで定義されているメソッドに対して機能します。 サーバー側から、カスタムオブジェクトを送信できます。

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

クライアント側では、`Message`パラメーターを使用するのではなく、プロパティにアクセスします。

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

後からメッセージ送信者をペイロードに追加する場合は、オブジェクトにプロパティを追加します。

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

古いクライアントには`Sender`値が必要ないためそれを無視します。 新しいクライアントは新しいプロパティを読み取るために更新して、それを受け取ることができます。

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

この場合は、新しいクライアントは`Sender`の値を提供しない古いサーバーに対して寛容です。 古いサーバーは`Sender`の値を提供しないため、クライアントはアクセスする前に存在するかどうかを確認します。
