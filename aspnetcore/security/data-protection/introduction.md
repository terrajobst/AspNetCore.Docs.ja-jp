---
title: データ保護の ASP.NET Core
author: rick-anderson
description: データ保護の概念と、ASP.NET Core データ保護 Api の設計原則について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/introduction
ms.openlocfilehash: 37f170a3e8a46ef2215b0999358d46dd402636df
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653840"
---
# <a name="aspnet-core-data-protection"></a>データ保護の ASP.NET Core

多くの場合、Web アプリケーションは、セキュリティを重視するデータを格納する必要があります。 Windows では、デスクトップアプリケーション用に DPAPI が提供されていますが、これは web アプリケーションには適していません。 ASP.NET Core データ保護スタックは、開発者がキーの管理やローテーションなどのデータを保護するために使用できる、シンプルで使いやすい暗号化 API を提供します。

ASP.NET Core データ保護スタックは、ASP.NET 1.x の &lt;machineKey&gt; 要素の長期的な置換として機能するように設計されています。 これは、古い暗号化スタックの多くの欠点に対処するように設計されており、最新のアプリケーションが発生する可能性のあるほとんどのユースケースに対して、すぐに使用できるソリューションを提供しています。

## <a name="problem-statement"></a>問題の説明

全体的な問題の説明は、1つの文で簡潔に記述できます。後で取得するために信頼できる情報を保持する必要がありますが、永続化メカニズムを信頼していません。 Web 用語では、"信頼された状態を信頼されていないクライアント経由でラウンドトリップする必要がある" として記述されている可能性があります。

この例では、認証 cookie またはベアラートークンが使用されています。 サーバーは "I am Groot と" xyz アクセス許可 "トークンを生成し、クライアントに渡します。 将来の日付では、クライアントはそのトークンをサーバーに返しますが、サーバーでは、クライアントがトークンを偽造していないことを保証する必要があります。 そのため、最初の要件: 信頼性 ( 整合性、改ざん防止)。

永続化された状態はサーバーによって信頼されているため、この状態には、オペレーティング環境に固有の情報が含まれている可能性があります。 ファイルパス、アクセス許可、ハンドルまたはその他の間接参照、またはサーバー固有のその他のデータの形式を使用できます。 通常、このような情報は、信頼されていないクライアントに公開することはできません。 そのため、2番目の要件は機密性です。

最後に、最新のアプリケーションがコンポーネント化されているため、システム内の他のコンポーネントに関係なく、個々のコンポーネントでこのシステムを利用することが求められています。 たとえば、ベアラートークンコンポーネントがこのスタックを使用している場合は、同じスタックを使用している可能性のある CSRF メカニズムからの干渉なしで動作する必要があります。 そのため、最終的な要件は分離です。

要件の範囲を絞るために、さらに制約を与えることができます。 ここでは、cryptosystem 内で動作するすべてのサービスが同じように信頼されており、直接制御しているサービスの外部でデータを生成または使用する必要がないことを想定しています。 さらに、web サービスに対する各要求が cryptosystem を1回以上通過する可能性があるため、可能な限り高速操作を行う必要があります。 これにより、対称暗号化がシナリオに適したものになるため、必要な時間まで非対称暗号化を割引できます。

## <a name="design-philosophy"></a>設計思想

まず、既存のスタックに関する問題を特定しました。 これを行った後は、既存のソリューションのランドスケープを調査し、既存のソリューションには、お探しの機能がまったくないという結論を与えました。 次に、いくつかの基本原則に基づいてソリューションを設計しています。

* システムは、構成を簡単にする必要があります。 理想的には、システムがゼロ構成であり、開発者が実行を開始する可能性があります。 開発者が特定の側面 (キーリポジトリなど) を構成する必要がある場合は、これらの特定の構成を単純にすることを考慮する必要があります。

* コンシューマー向けのシンプルな API を提供します。 Api は、正しく使用するのが簡単で、不適切に使用するのは困難です。

* 開発者は、キー管理の原則を習得するべきではありません。 システムは、開発者の代わりに、アルゴリズムの選択とキーの有効期間を処理する必要があります。 理想的には、開発者が未加工のキーマテリアルにアクセスできないようにする必要があります。

* 可能な場合は、保存時にキーを保護する必要があります。 システムは、適切な既定の保護メカニズムを見つけて、自動的に適用する必要があります。

これらの原則を念頭に置いて、シンプルで使い[やすい](xref:security/data-protection/using-data-protection)データ保護スタックを開発しました。

ASP.NET Core データ保護 Api は、主に機密ペイロードの永続的な永続化のためのものではありません。 [WINDOWS CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx)や[Azure Rights Management](/rights-management/)などのその他のテクノロジは、無期限のストレージのシナリオに適しています。また、強力なキー管理機能も備えています。 ただし、社外秘データの長期的な保護には、ASP.NET Core データ保護 Api を使用した開発者の禁止はありません。

## <a name="audience"></a>対象者

データ保護システムは、5つのメインパッケージに分割されています。 これらの Api のさまざまな側面は、3つの主要な対象ユーザーを対象とします。

1. [コンシューマー api の概要](xref:security/data-protection/consumer-apis/overview)については、アプリケーションとフレームワークの開発者を対象としています。

   「スタックの動作方法や構成方法について知りたくありません。 単に、Api を正常に使用できる確率で、可能な限り単純な方法で操作を実行したいと考えています。」

2. [構成 api](xref:security/data-protection/configuration/overview)は、アプリケーション開発者とシステム管理者を対象としています。

   「データ保護システムに、既定以外のパスや設定が必要であることを知らせる必要があります。」

3. 拡張 Api は、カスタムポリシーの実装を担当する開発者を対象としています。 これらの Api の使用は、まれな状況や経験豊富な開発者に限定されます。

   「システム内のコンポーネント全体を置き換える必要があります。これは、本当に独特な行動要件があるためです。 要件を満たすプラグインを構築するために、API サーフェイスの一般的に使用されない部分について学習します。

## <a name="package-layout"></a>パッケージのレイアウト

データ保護スタックは、5つのパッケージで構成されます。

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/)には、データ保護サービスを作成するための <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> および <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> インターフェイスが含まれています。 また、これらの型を操作するための便利な拡張メソッドも含まれています (たとえば、 [IDataProtector](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*))。 データ保護システムが他の場所でインスタンス化されており、API を使用している場合は、`Microsoft.AspNetCore.DataProtection.Abstractions`を参照してください。

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/)には、データ保護システムのコア実装が含まれています。これには、主要な暗号化操作、キー管理、構成、および拡張機能が含まれます。 データ保護システムをインスタンス化する (たとえば、<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>に追加する)、または動作を変更または拡張するには、`Microsoft.AspNetCore.DataProtection`を参照してください。

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)には、開発者が役に立つがコアパッケージに属さない追加の api が含まれています。 たとえば、このパッケージには、データ保護システムをインスタンス化して、依存関係を挿入せずにファイルシステム上の場所にキーを格納するファクトリメソッドが含まれています (<xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>を参照してください)。 また、保護されたペイロードの有効期間を制限するための拡張メソッドも含まれています (<xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector>を参照してください)。

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/)を既存の ASP.NET 4.x アプリにインストールして、新しい ASP.NET Core データ保護スタックを使用するように `<machineKey>` 操作をリダイレクトすることができます。 詳細については、<xref:security/data-protection/compatibility/replacing-machinekey> を参照してください。

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/)は、PBKDF2 パスワードハッシュルーチンの実装を提供し、ユーザーパスワードを安全に処理する必要があるシステムで使用できます。 詳細については、<xref:security/data-protection/consumer-apis/password-hashing> を参照してください。

## <a name="additional-resources"></a>その他のリソース

<xref:host-and-deploy/web-farm>
