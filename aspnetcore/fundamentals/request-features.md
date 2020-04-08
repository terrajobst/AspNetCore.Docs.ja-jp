---
title: ASP.NET Core での要求機能
author: ardalis
description: ASP.NET Core のインターフェイスに定義されている HTTP 要求と応答に関連する Web サーバーの実装に関する詳細を学習します。
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416174"
---
# <a name="request-features-in-aspnet-core"></a>ASP.NET Core での要求機能

作成者: [Steve Smith](https://ardalis.com/)

HTTP の要求と応答に関連する Web サーバーの実装の詳細が、インターフェイスで定義されています。 これらのインターフェイスは、アプリケーションのホスティング パイプラインを作成および変更するために、サーバーの実装とミドルウェアで使用されます。

## <a name="feature-interfaces"></a>機能インターフェイス

ASP.NET Core には、サーバーがサポートする機能を識別するために使用される `Microsoft.AspNetCore.Http.Features` に、多数の HTTP 機能インターフェイスが定義されています。 次の機能インターフェイスにより、要求が処理され、応答が返されます。

`IHttpRequestFeature` プロトコル、パス、クエリ文字列、ヘッダーおよび本文などの HTTP 要求の構造を定義します。

`IHttpResponseFeature` 状態コード、ヘッダー、応答の本文などの HTTP 応答の構造を定義します。

`IHttpAuthenticationFeature``ClaimsPrincipal` に基づいてユーザーを識別し、認証ハンドラーを指定するサポートを定義します。

`IHttpUpgradeFeature`[HTTP アップグレード](https://tools.ietf.org/html/rfc2616.html#section-14.42)のサポートを定義します。これにより、サーバーでプロトコルを切り替えたい場合、使用するその他のプロトコルを指定することが可能になります。

`IHttpBufferingFeature` 要求および応答のバッファーを無効化する方法を定義します。

`IHttpConnectionFeature` ローカルおよびリモート アドレスおよびポートのプロパティを定義します。

`IHttpRequestLifetimeFeature` 接続の中止、クライアントの切断により要求が途中で中止されたかどうかの検出のサポートを定義します。

`IHttpSendFileFeature` ファイルを非同期的に送信するためのメソッドを定義します。

`IHttpWebSocketFeature` Web ソケットをサポートする API を定義します。

`IHttpRequestIdentifierFeature` 要求を一意に識別するために実装できるプロパティを追加します。

`ISessionFeature` ユーザー セッションをサポートする `ISessionFactory` と `ISession` の抽象化を定義します。

`ITlsConnectionFeature` クライアント証明書を取得する API を定義します。

`ITlsTokenBindingFeature` TLS トークンのバインド パラメーターを使用するメソッドを定義します。

> [!NOTE]
> `ISessionFeature` はサーバー機能ではありませんが、`SessionMiddleware` によって実装されています (「[Managing Application State](app-state.md)」 (アプリケーションの状態の管理) を参照)。

## <a name="feature-collections"></a>機能のコレクション

`Features` の `HttpContext` プロパティは、現在の要求で利用可能な HTTP 機能を取得および設定するためのインターフェイスです。 機能のコレクションは要求のコンテキスト内でも変更可能であるため、コレクションの変更と、その他の機能のサポートの追加にはミドルウェアを使用できます。

## <a name="middleware-and-request-features"></a>ミドルウェアおよび要求機能

サーバーは、機能コレクションの作成を行い、ミドルウェアはこのコレクションの追加と、このコレクションの機能の使用の両方を行います。 たとえば、`StaticFileMiddleware` は、`IHttpSendFileFeature` 機能にアクセスします。 この機能が存在する場合、これは、その物理パスから、要求された静的ファイルを送信するために使用されます。 ない場合、代わりの低速な方法でファイルを送信します。 使用可能な場合、`IHttpSendFileFeature` はオペレーティング システムがファイルを開き、ネットワーク カードに直接カーネル モード コピーを実行できるようにします。

さらに、ミドルウェアは、サーバーによって確立された機能コレクションに追加できます。 ミドルウェアでは、既存の機能の代わりをすることも可能で、サーバーの機能を補うことができます。 コレクションに追加された機能は、その他のミドルウェアまたは基礎となっているアプリケーション自体で後で要求パイプラインで使用可能です。

サーバーのカスタムの実装と特定のミドルウェアの機能強化を組み合わせることにより、アプリケーションで必要な正確な機能セットを構築できます。 これにより、サーバーを変更せずに不足している機能を追加できます。また、最小限の機能を公開することにより、外部から攻撃を受ける可能性を減らして、パフォーマンスを向上させることができます。

## <a name="summary"></a>まとめ

機能インターフェイスは、特定の要求がサポートする可能性がある特定の HTTP 機能を定義します。 サーバーでは、機能のコレクションとそのサーバーによってサポートされる機能の初期セットを定義しますが、ミドルウェアは、これらの機能を強化するために使用できます。

## <a name="additional-resources"></a>その他の技術情報

* [サーバー](xref:fundamentals/servers/index)
* [ミドルウェア](xref:fundamentals/middleware/index)
* [Open Web Interface for .NET (OWIN)](xref:fundamentals/owin)
