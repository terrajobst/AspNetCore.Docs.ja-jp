---
title: ASP.NET Core Blazor のコンテンツセキュリティポリシーを適用する
author: guardrex
description: クロスサイトスクリプティング (XSS) 攻撃から保護するために ASP.NET Core Blazor アプリでコンテンツセキュリティポリシー (CSP) を使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 1cfebf7b3d3bbb98a671b6f2db7c6518cda74b65
ms.sourcegitcommit: 51c86c003ab5436598dbc42f26ea4a83a795fd6e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2020
ms.locfileid: "78964536"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor のコンテンツセキュリティポリシーを適用する

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[クロスサイトスクリプティング (XSS)](xref:security/cross-site-scripting)は、攻撃者が1つまたは複数の悪意のあるクライアント側スクリプトをアプリのレンダリングされたコンテンツに配置するセキュリティの脆弱性です。 コンテンツセキュリティポリシー (CSP) は、ブラウザーに有効なことを通知することで、XSS 攻撃から保護するのに役立ちます。

* スクリプト、スタイルシート、画像など、読み込まれたコンテンツのソース。
* ページによって実行されるアクション。フォームの許可された URL ターゲットを指定します。
* 読み込むことができるプラグイン。

CSP をアプリに適用するために、開発者は1つ以上の `Content-Security-Policy` ヘッダーまたは `<meta>` タグに複数の CSP コンテンツセキュリティ*ディレクティブ*を指定します。

ポリシーは、ページの読み込み中にブラウザーによって評価されます。 ブラウザーはページのソースを検査し、コンテンツセキュリティディレクティブの要件を満たしているかどうかを判断します。 リソースのポリシーディレクティブが満たされていない場合、ブラウザーはリソースを読み込みません。 たとえば、サードパーティのスクリプトを許可しないポリシーについて考えてみます。 `src` 属性でサードパーティの元の `<script>` タグがページに含まれている場合、ブラウザーによってスクリプトの読み込みが禁止されます。

CSP は、Chrome、Edge、Firefox、Opera、Safari など、最新のデスクトップおよびモバイルブラウザーでサポートされています。 Blazor アプリには CSP を使用することをお勧めします。

## <a name="policy-directives"></a>ポリシーディレクティブ

最小で、Blazor アプリのディレクティブとソースを指定します。 必要に応じて、ディレクティブとソースを追加します。 この記事の「[ポリシーの適用](#apply-the-policy)」セクションでは、次のディレクティブを使用しています。ここでは、Blazor WebAssembly サーバーのセキュリティポリシーの例を示します。Blazor

* [ベース uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; は、ページの `<base>` タグの Url を制限します。 `self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
* [すべてブロック-混合コンテンツ](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content)&ndash; は、混合 HTTP と HTTPS コンテンツの読み込みを防止します。
* [既定値-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; は、ポリシーによって明示的に指定されていないソースディレクティブのフォールバックを示します。 `self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; は、イメージの有効なソースを示します。
  * `data:` Url からのイメージの読み込みを許可するには `data:` を指定します。
  * HTTPS エンドポイントからのイメージの読み込みを許可するには `https:` を指定します。
* [オブジェクト src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; は、`<object>`、`<embed>`、および `<applet>` タグの有効なソースを示します。 すべての URL ソースを禁止するには、`none` を指定します。
* [スクリプト-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; は、スクリプトの有効なソースを示します。
  * ブートストラップスクリプトの `https://stackpath.bootstrapcdn.com/` ホストソースを指定します。
  * `self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
  * Blazor Webasapp:
    * 次のハッシュを指定して、必要な Blazor のインラインスクリプトの読み込みを許可します。
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * 文字列からコードを作成するために `eval()` およびメソッドを使用するには、`unsafe-eval` を指定します。
  * Blazor サーバーアプリで、スタイルシートのフォールバック検出を実行するインラインスクリプトの `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` ハッシュを指定します。
* [スタイル-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; は、スタイルシートの有効なソースを示します。
  * ブートストラップスタイルシートの `https://stackpath.bootstrapcdn.com/` ホストソースを指定します。
  * `self` を指定して、スキームやポート番号など、アプリの配信元が有効なソースであることを示します。
  * インラインスタイルの使用を許可するには `unsafe-inline` を指定します。 初期要求後にクライアントとサーバーを再接続するために Blazor サーバーアプリの UI にはインライン宣言が必要です。 今後のリリースでは、`unsafe-inline` が不要になるように、インラインスタイルが削除される可能性があります。
* [アップグレード-安全でない要求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests)&ndash; は、セキュリティで保護されていない (HTTP) ソースからのコンテンツ URL を HTTPS 経由で安全に取得する必要があることを示します。

上記のディレクティブは、Microsoft Internet Explorer 以外のすべてのブラウザーでサポートされています。

追加のインラインスクリプトの SHA ハッシュを取得するには、次のようにします。

* 「[ポリシーの適用](#apply-the-policy)」セクションに示されている CSP を適用します。
* アプリをローカルで実行しながら、ブラウザーの開発者ツールコンソールにアクセスします。 CSP ヘッダーまたは `meta` タグが存在する場合、ブラウザーはブロックされたスクリプトのハッシュを計算して表示します。
* ブラウザーによって提供されるハッシュを `script-src` ソースにコピーします。 各ハッシュを囲む単一引用符を使用します。

コンテンツセキュリティポリシーレベル2のブラウザーサポートマトリックスについては、「[使用可能: コンテンツセキュリティポリシーレベル 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)」を参照してください。

## <a name="apply-the-policy"></a>ポリシーの適用

`<meta>` タグを使用してポリシーを適用します。

* `http-equiv` 属性の値を `Content-Security-Policy`に設定します。
* `content` 属性値にディレクティブを配置します。 ディレクティブはセミコロン (`;`) で区切ります。
* 常に `meta` タグを `<head>` のコンテンツに配置します。

次のセクションでは、Blazor WebAssembly サーバーのポリシーの例を示します。Blazor これらの例は、Blazorの各リリースについて、この記事でバージョン管理されています。 リリースに適したバージョンを使用するには、この web ページの**バージョン**ドロップダウンセレクターでドキュメントバージョンを選択します。

### <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

*Wwwroot/index.html*ホストページの `<head>` の内容で、「[ポリシーディレクティブ](#policy-directives)」セクションで説明されているディレクティブを適用します。

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="opno-locblazor-server"></a>Blazor サーバー

[ *Pages/_Host* ] ホストページの `<head>` の内容で、[[ポリシーディレクティブ](#policy-directives)] セクションで説明されているディレクティブを適用します。

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a>Meta タグの制限事項

`<meta>` タグポリシーは、次のディレクティブをサポートしていません。

* [フレーム-先祖](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [レポート先](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [レポート-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [サンドボックス](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

上記のディレクティブをサポートするには、`Content-Security-Policy`という名前のヘッダーを使用します。 ディレクティブ文字列は、ヘッダーの値です。

## <a name="test-a-policy-and-receive-violation-reports"></a>ポリシーをテストして違反レポートを受信する

テストは、初期ポリシーを作成するときに、サードパーティ製のスクリプトが誤ってブロックされていないことを確認するのに役立ちます。

ポリシーディレクティブを適用せずに一定期間にわたってポリシーをテストするには、ヘッダーベースのポリシーの `<meta>` タグの `http-equiv` 属性またはヘッダー名を `Content-Security-Policy-Report-Only`に設定します。 エラーレポートは、JSON ドキュメントとして指定された URL に送信されます。 詳細については、 [MDN web ドキュメント: コンテンツ-セキュリティポリシー-レポートのみ](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)を参照してください。

ポリシーがアクティブになっているときの違反を報告するには、次の記事を参照してください。

* [レポート先](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [レポート-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

`report-uri` の使用は推奨されなくなりましたが、すべての主要なブラウザーで `report-to` がサポートされるようになるまで、両方のディレクティブを使用する必要があります。 `report-uri` のサポートはブラウザーからいつ*でも*削除される可能性があるため、`report-uri` を排他的に使用しないようにしてください。 `report-to` が完全にサポートされている場合は、ポリシー内の `report-uri` のサポートを削除します。 `report-to`の導入を追跡する方法については、「[レポートを使用](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)するには」を参照してください。

アプリのポリシーをリリースするたびにテストして更新します。

## <a name="troubleshoot"></a>[トラブルシューティング]

* エラーは、ブラウザーの開発者ツールコンソールに表示されます。 ブラウザーは次の情報を提供します。
  * ポリシーに準拠していない要素。
  * ブロックされた項目を許可するようにポリシーを変更する方法。
* ポリシーが完全に有効になるのは、クライアントのブラウザーが含まれているすべてのディレクティブをサポートしている場合のみです。 現在のブラウザーのサポートマトリックスについては、「[使用可能: コンテンツ-セキュリティ-ポリシー](https://caniuse.com/#search=Content-Security-Policy)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [MDN web ドキュメント: コンテンツ-セキュリティ-ポリシー](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [コンテンツセキュリティポリシーレベル2](https://www.w3.org/TR/CSP2/)
* [Google CSP エバリュエーター](https://csp-evaluator.withgoogle.com/)
