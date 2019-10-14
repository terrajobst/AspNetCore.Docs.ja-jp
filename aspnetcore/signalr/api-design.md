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

作成者: [Andrew Stanton-nurse](https://twitter.com/anurse)

この記事では、SignalR ベースの Api を構築するためのガイダンスを提供します。

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>カスタム オブジェクトのパラメーターを使用して、下位互換性を確保するには

(クライアントまたはサーバー) 上の SignalR ハブ メソッドにパラメーターの追加は、*互換性に影響する変更*します。 これは、適切な数のパラメーターのないメソッドを呼び出すしようとすると、以前のクライアント/サーバーのエラーが発生することを意味します。 ただし、カスタム オブジェクトのパラメーターのプロパティを追加することは**いない**重大な変更。 クライアントまたはサーバー上の変更に対応する互換性のある Api を設計するために使用できます。

たとえば、次のようにサーバー側 API があるとします。

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

JavaScript クライアントは、このメソッドを使用してを呼び出す`invoke`次のようにします。

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

後でサーバー メソッドに 2 番目のパラメーターを追加する場合、古いクライアントは、このパラメーターの値を指定しません。 例:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

古いクライアントは、このメソッドを呼び出す際は、このようなエラーが表示されます。

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

サーバーで、このようなログ メッセージが表示されます。

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

古いクライアントでは、1 つのパラメーターのみ送信されるが、新しいサーバー API には 2 つのパラメーターが必要です。 カスタム オブジェクトをパラメーターとして使用して、柔軟性が。 カスタム オブジェクトを使用して元の API のデザインを変更しましょう。

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

次に、クライアントは、メソッドを呼び出すオブジェクトを使用します。

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

パラメーターを追加するには、代わりにプロパティを追加、`TotalLengthRequest`オブジェクト。

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

古いクライアントが、追加の 1 つのパラメーターを送信するときに`Param2`プロパティが 0.020`null`します。 チェックして、古いクライアントから送信されたメッセージを検出することができます、`Param2`の`null`し、既定値を適用します。 新しいクライアントは、両方のパラメーターを送信できます。

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

同じ手法では、クライアントで定義されているメソッドに対して機能します。 サーバー側からは、カスタム オブジェクトを送信できます。

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

アクセスするクライアント側で、`Message`パラメーターを使用するのではなく、プロパティ。

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

後で、メッセージの送信者をペイロードに追加する場合は、オブジェクトにプロパティを追加します。

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

以前のクライアントは必要はありません、`Sender`値であるため、それを無視します。 新しいクライアントを新しいプロパティを更新して、受け入れることができます。

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

ここでは、新しいクライアントは提供しない以前のサーバーのトレラント、`Sender`値。 古いサーバーが提供されないため、`Sender`値をクライアントは、アクセスする前に存在するかどうかを確認します。
