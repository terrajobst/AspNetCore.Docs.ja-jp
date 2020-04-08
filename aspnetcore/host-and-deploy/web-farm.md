---
title: Web ファームでの ASP.NET Core のホスト
author: rick-anderson
description: Web ファーム環境での共有リソースを使用して、ASP.NET Core アプリのインスタンスを複数ホストする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/web-farm
ms.openlocfilehash: 316c87e5f49593c05991a94cbe5e55d175a49bb3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647342"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Web ファームでの ASP.NET Core のホスト

作成者: [Chris Ross](https://github.com/Tratcher)

"*Web ファーム*" とは、アプリのインスタンスを複数ホストする、2 つ以上の Web サーバー (または "*ノード*") のグループのことです。 ユーザーからの要求が Web ファームに到着すると、"*ロード バランサー*" が要求を Web ファームのノードに分散します。 Web ファームの利点は次のとおりです。

* **信頼性/可用性** &ndash; 1 つまたは複数のノードに障害が発生しても、ロード バランサーによって要求がルーティングされ、要求の処理を続行することができます。
* **容量/パフォーマンス** &ndash; 複数のノードは 1 台のサーバーよりもより多くの要求を処理できます。 ロード バランサーは、ノードへの要求を分散することにでワークロードのバランスをとります。
* **スケーラビリティ** &ndash; より多くの (またはより少ない) 容量が必要になると、ワークロードに一致するようにアクティブなノードの数を増加 (または減少) させることができます。 [Azure App Service](https://azure.microsoft.com/services/app-service/) などの Web ファーム プラットフォーム テクノロジでは、システム管理者の要求に応じて、または人の介入なしに、自動的にノードを追加したり削除したりできます。
* **保守容易性** &ndash; Web ファームのノードでは一連の共有サービスを利用できるため、システム管理が簡単になります。 たとえば、Web ファームのノードにおいて、画像やダウンロード可能ファイルなどの静的リソースのために、単一のデータベース サーバーと共通のネットワークの場所を利用することができます。

このトピックでは、共有リソースを利用する Web ファームでホストされている ASP.NET Core アプリ用の構成と依存関係について説明します。

## <a name="general-configuration"></a>全般構成

<xref:host-and-deploy/index>  
ホスティング環境を設定し、ASP.NET Core アプリを展開する方法を学習します。 Web ファームの各ノード上のプロセス マネージャーを構成して、アプリの起動と再起動を自動化します。 各ノードには ASP.NET Core ランタイムが必要です。 詳細については、ドキュメントの[ホストと展開](xref:host-and-deploy/index)に関する部分のトピックをご覧ください。

<xref:host-and-deploy/proxy-load-balancer>  
プロキシ サーバーとロード バランサーの背後にホストされているアプリの構成について説明します。このような構成では、要求の重要な情報がわからなくなることがよくあります。

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service](https://azure.microsoft.com/services/app-service/) は ASP.NET Core を含む Web アプリをホストするための [Microsoft クラウド コンピューティング プラットフォーム サービス](https://azure.microsoft.com/)です。 App Service は、自動スケーリング、負荷分散、修正プログラムの適用、および継続的配置を提供する、フル マネージドのプラットフォームです。

## <a name="app-data"></a>アプリのデータ

アプリが複数のインスタンスに対してスケーリングされると、ノード間での共有を必要とするアプリの状態が出現する可能性があります。 この状態が一時的である場合は、[IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) を共有することを検討してください。 共有状態を永続的に保つ必要がある場合は、共有状態をデータベースに格納することを検討してください。

## <a name="required-configuration"></a>必要な構成

Web ファームに展開されるアプリに対して、データの保護とキャッシュを構成する必要があります。

### <a name="data-protection"></a>データの保護

アプリでは、データを保護するために [ASP.NET Core データ保護システム](xref:security/data-protection/introduction)が使用されます。 データ保護では、"*キー リング*" に格納されている一連の暗号化キーが利用されます。 データ保護システムを初期化すると、キー リングをローカルに格納する[既定の設定](xref:security/data-protection/configuration/default-settings)が適用されます。 既定の構成では、Web ファームの各ノード上に一意のキー リングが格納されます。 このため、Web ファームの各ノードは、他のノード上のアプリが暗号化したデータを暗号化解除することはできません。 既定の構成が Web ファームでのアプリのホスト全般に対して適切であるわけではありません。 共有キー リングを実装する代わりに、ユーザー要求を常に同じノードにルーティングすることもできます。 Web ファームの展開に向けたデータ保護システムの構成について詳しくは、<xref:security/data-protection/configuration/overview> をご覧ください。

### <a name="caching"></a>キャッシュ

Web ファーム環境におけるキャッシュのメカニズムでは、Web ファームのノード間でキャッシュされる項目を共有する必要があります。 キャッシュは、一般的な Redis Cache (SQL Server 共有データベース) を利用するか、またはキャッシュされる項目を Web ファーム間で共有するカスタム実装のキャッシュを利用する必要があります。 詳細については、「<xref:performance/caching/distributed>」を参照してください。

## <a name="dependent-components"></a>依存コンポーネント

次のシナリオに追加構成は必要ありませんが、シナリオが依存するテクノロジには、Web ファーム用の構成が必要です。

| シナリオ | 依存先 &hellip; |
| -------- | ------------------- |
| 認証 | データ保護 (<xref:security/data-protection/configuration/overview> を参照)。<br><br>詳細については、次のトピックを参照してください。 <xref:security/authentication/cookie> および <xref:security/cookie-sharing> |
| Identity | 認証とデータベースの構成。<br><br>詳細については、「<xref:security/authentication/identity>」を参照してください。 |
| セッション | データ保護 (暗号化された Cookie) (<xref:security/data-protection/configuration/overview> を参照) とキャッシュ (<xref:performance/caching/distributed> を参照)。<br><br>詳細については、[セッションと状態の管理に関するページの「セッション状態」](xref:fundamentals/app-state#session-state)を参照してください。 |
| TempData | データ保護 (暗号化された Cookie) (<xref:security/data-protection/configuration/overview> を参照) またはセッション ([セッションと状態の管理に関するページの「セッション状態」](xref:fundamentals/app-state#session-state)を参照)。<br><br>詳細については、[セッションと状態の管理に関するページの「TempData」](xref:fundamentals/app-state#tempdata)を参照してください。 |
| 偽造防止 | データ保護 (<xref:security/data-protection/configuration/overview> を参照)。<br><br>詳細については、「<xref:security/anti-request-forgery>」を参照してください。 |

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="data-protection-and-caching"></a>データ保護とキャッシュ

データ保護またはキャッシュが Web ファーム環境用に構成されていない場合、要求の処理中に断続的にエラーが発生します。 このエラーは、ノード間で同じリソースが共有されておらず、ユーザー要求が同じノードにルーティングされない場合があるために発生します。

ユーザーが Cookie 認証を使用してアプリにサインインする場合を考えてみます。 このユーザーは、1 つの Web ファームのノード上にあるアプリにサインインします。 ユーザーの次の要求が、ユーザーがサインインしたノードと同じノードに届いた場合、アプリは認証 Cookie の暗号化を解除して、アプリのリソースへのアクセスを許可することができます。 ユーザーの次の要求が異なるノードに届いた場合、アプリはユーザーがサインインしたノードの認証 Cookie の暗号化を解除できず、要求されたリソースに対する認証は失敗します。

次の現象のいずれかが**断続的に**発生するときは、多くの場合、データ保護またはキャッシュが Web ファーム環境に向けて適切に構成されていないことが問題の原因です。

* 認証の中断 &ndash; 認証 Cookie が正しく構成されていない、または暗号化解除できない。 OAuth (Facebook、Microsoft、Twitter) ログインまたは OpenIdConnect ログインが「関連付けできませんでした」というエラーで失敗する。
* 承認の中断 &ndash; Id が失われる。
* セッション状態でデータが失われる。
* キャッシュされた項目が消える。
* TempData が失敗する。
* POST の失敗 &ndash; 偽造防止チェックが失敗する。

Web ファームの展開に向けたデータ保護の構成について詳しくは、<xref:security/data-protection/configuration/overview> をご覧ください。 Web ファームの展開に向けたキャッシュの構成について詳しくは、「<xref:performance/caching/distributed>」をご覧ください。

## <a name="obtain-data-from-apps"></a>アプリからデータを取得する

Web ファーム アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。 詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* [Windows でのカスタムのスクリプト拡張機能](/azure/virtual-machines/extensions/custom-script-windows) &ndash; Azure 仮想マシンにスクリプトをダウンロードして実行します。これは、デプロイ後の構成とソフトウェアのインストールに役立ちます。
* <xref:host-and-deploy/proxy-load-balancer>
 