---
title: ASP.NET Core での SameSite cookie の使用
author: rick-anderson
description: を使用して ASP.NET Core で cookie を SameSite する方法について説明します
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: security/samesite
ms.openlocfilehash: b344ed8f539979210980b3421659207edd513f32
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146434"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a>ASP.NET Core での SameSite cookie の使用

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07)は、クロスサイトリクエスト偽造 (csrf) 攻撃に対して何らかの保護を提供するように設計された[IETF](https://ietf.org/about/)ドラフトです。 [SameSite 2019 のドラフト](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):

* クッキーを既定で `SameSite=Lax` として扱います。
* クロスサイト配信を有効にするために `SameSite=None` を明示的にアサートする cookie の状態を `Secure`としてマークする必要があります。

`Lax` は、ほとんどのアプリ cookie で機能します。 [OpenID connect](https://openid.net/connect/) (oidc) や[ws-federation](https://auth0.com/docs/protocols/ws-fed)のような認証形式では、既定で POST ベースのリダイレクトが設定されます。 POST ベースのリダイレクトによって SameSite ブラウザーの保護がトリガーされるため、これらのコンポーネントの SameSite は無効になります。 ほとんどの[OAuth](https://oauth.net/)ログインは、要求フローの違いによって影響を受けません。

`None` パラメーターを指定すると、以前の2016ドラフト標準 (iOS 12 など) を実装したクライアントとの互換性の問題が発生します。 このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。

Cookie を生成する各 ASP.NET Core コンポーネントは、SameSite が適切かどうかを判断する必要があります。

## <a name="api-usage-with-samesite"></a>SameSite を使用した API の使用

SameSite は、既定では `Unspecified`[に設定されます。つまり](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)、cookie に追加された属性はありません。また、クライアントは既定の動作を使用します (新しいブラウザーではなく、古いブラウザーではありません)。 次のコードは、cookie の SameSite 値を `SameSiteMode.Lax`に変更する方法を示しています。

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

Cookie を出力するすべての ASP.NET Core コンポーネントは、前述の既定値よりも、そのシナリオに適した設定を上書きします。 オーバーライドされた前の既定値は変更されていません。

| コンポーネント | Cookie | [既定値] |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [SessionOptions. Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieTempDataProviderOptions](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [アンチ Forgeryoptions. Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [Cookie 認証](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [CookieAuthenticationOptions. Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [TwitterOptions.StateCookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [Remoteauthenticationoptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpContext. Cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

ASP.NET Core 3.1 以降では、次の SameSite サポートが提供されます。

* `SameSiteMode.None` が出力される動作を再定義し `SameSite=None`
* 新しい値 `SameSiteMode.Unspecified` を追加して、SameSite 属性を省略します。
* すべての cookie Api の既定値は `Unspecified`です。 Cookie を使用する一部のコンポーネントでは、そのシナリオにより具体的な値が設定されています。 例については、上の表を参照してください。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 3.0 以降では、一貫性のないクライアントの既定値と競合しないように、SameSite の既定値が変更されました。 次の Api が既定値を `SameSiteMode.Lax ` から `-1` に変更し、これらの cookie の SameSite 属性が出力されないようにしました。

* <xref:Microsoft.AspNetCore.Http.CookieOptions> 使用され[ます。](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)
* <xref:Microsoft.AspNetCore.Http.CookieBuilder> `CookieOptions` のファクトリとして使用されます。
* [CookiePolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>履歴と変更

SameSite サポートは、2.0 の ASP.NET Core で最初に[2016 ドラフト標準](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)を使用して実装されました。 2016標準はオプトインでした。 ASP.NET Core、いくつかの cookie を既定で `Lax` するように設定することによってオプトインできます。 認証でいくつかの[問題](https://github.com/aspnet/Announcements/issues/318)が発生すると、ほとんどの SameSite の使用が[無効に](https://github.com/aspnet/Announcements/issues/348)なりました。

2016標準から2019標準に更新するために、2019年11月に[修正プログラム](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)が発行されました。 [SameSite 仕様の2019ドラフト](https://github.com/aspnet/Announcements/issues/390):

* は、2016ドラフトとの下位互換性が**ありません**。 詳細については、このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。
* Cookie を既定で `SameSite=Lax` として扱うことを指定します。
* クロスサイト配信を有効にするために `SameSite=None` を明示的にアサートする cookie を `Secure`としてマークする必要があることを指定します。 `None` は、オプトアウトする新しいエントリです。
* は、ASP.NET Core 2.1、2.2、および3.0 に対して発行された修正プログラムによってサポートされています。 ASP.NET Core 3.1 には、追加の SameSite サポートがあります。
* [2 月 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)日に既定で[Chrome](https://chromestatus.com/feature/5088147346030592)によって有効になるようにスケジュールされています。 ブラウザーは2019でこの標準への移行を開始しました。

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>2016 SameSite draft standard から2019ドラフト標準への変更によって影響を受ける Api

* [Http. SameSiteMode](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [CookieOptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieBuilder. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookiePolicyOptions. MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>古いブラウザーのサポート

2016 SameSite 標準では、不明な値を `SameSite=Strict` 値として処理する必要があることが義務付けられています。 2016 SameSite 標準をサポートする古いブラウザーからアクセスされるアプリは、値が `None`の SameSite プロパティを取得すると壊れます。 Web apps が古いブラウザーをサポートする予定の場合は、ブラウザーの検出を実装する必要があります。 ユーザーエージェントの値が変動し、頻繁に変更されるため、ASP.NET Core はブラウザーの検出を実装しません。 <xref:Microsoft.AspNetCore.CookiePolicy> の拡張ポイントを使用すると、ユーザーエージェント固有のロジックに接続できます。

`Startup.Configure`で、<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> または cookie を書き込む*任意の*メソッドを呼び出す前に <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> を呼び出すコードを追加します。

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

`Startup.ConfigureServices`で、次のようなコードを追加します。

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

前のサンプルの `MyUserAgentDetectionLib.DisallowsSameSiteNone` は、ユーザーエージェントが SameSite `None`をサポートしていないかどうかを検出するユーザー指定のライブラリです。

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

次のコードは、`DisallowsSameSiteNone` メソッドの例を示しています。

> [!WARNING]
> 次のコードは、デモンストレーションのみを対象としています。
> * 完全であるとは限りません。
> * 管理されていないか、サポートされていません。

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>SameSite 問題のアプリをテストする

サードパーティのログインによってなどのリモートサイトと対話するアプリでは、次の操作を行う必要があります。

* 複数のブラウザーで相互作用をテストします。
* このドキュメントで説明され[ている Cookiepolicy browser の検出と軽減策](#sob)を適用します。

新しい SameSite 動作にオプトインできるクライアントバージョンを使用して、web アプリをテストします。 Chrome、Firefox、Chromium Edge には、テストに使用できる新しいオプトイン機能フラグがあります。 アプリが SameSite パッチを適用したら、古いクライアントバージョン (特に Safari) を使用してテストします。 詳細については、このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。

### <a name="test-with-chrome"></a>Chrome を使用したテスト

Chrome 78 + には一時的な軽減策があるため、誤解を招く結果になります。 Chrome 78 + 一時的な軽減策では、2分前よりも少ない cookie を使用できます。 適切なテストフラグが有効になっている Chrome 76 または77では、より正確な結果が得られます。 新しい SameSite の動作をテストするには、`chrome://flags/#same-site-by-default-cookies` を**有効**に切り替えます。 新しい `None` 設定で失敗するように、Chrome の旧バージョン (75 以降) が報告されます。 このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。

Google では、以前のバージョンの chrome は使用できません。 「 [Chromium のダウンロード](https://www.chromium.org/getting-involved/download-chromium)」の手順に従って、Chrome の旧バージョンをテストします。 以前のバージョンの chrome を検索することによって提供されるリンクから Chrome**をダウンロードしないでください**。

* [Chromium 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Chromium 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

### <a name="test-with-safari"></a>Safari を使用したテスト

Safari 12 では以前のドラフトが厳密に実装されており、新しい `None` 値が cookie に含まれていると失敗します。 このドキュメントでは、[古いブラウザーをサポート](#sob)するブラウザーの検出コードを使用して `None` を回避します。 MSAL、ADAL、使用している任意のライブラリを使用して、Safari 12、Safari 13、WebKit ベースの OS スタイルのログインをテストします。 この問題は、基盤の OS バージョンによって変わります。 OSX Mojave (10.14) と iOS 12 には、新しい SameSite の動作に互換性の問題があることがわかっています。 OS を OSX Catalina.properties (10.15) または iOS 13 にアップグレードすると、問題が解決されます。 Safari には、現在、新しい仕様動作をテストするオプトインフラグがありません。

### <a name="test-with-firefox"></a>Firefox でのテスト

新しい標準の Firefox サポートは、バージョン68以降で、[`about:config`] ページで機能フラグ `network.cookie.sameSite.laxByDefault`をオンにしてテストできます。 以前のバージョンの Firefox との互換性に関する問題は報告されていません。

### <a name="test-with-edge-browser"></a>Edge ブラウザーを使用したテスト

Edge では、古い SameSite 標準がサポートされています。 Edge バージョン44には、新しい標準との互換性に関する既知の問題はありません。

### <a name="test-with-edge-chromium"></a>Edge でテストする (Chromium)

SameSite フラグは `edge://flags/#same-site-by-default-cookies` ページで設定されます。 Edge Chromium で互換性の問題は検出されませんでした。

### <a name="test-with-electron"></a>電子を使用したテスト

Electron の複数のバージョンには、Chromium の古いバージョンが含まれています。 たとえば、チームによって使用されている電子 66 Chromium のバージョンは、以前の動作を示しています。 製品で使用されている電子版を使用して、独自の互換性テストを実行する必要があります。 次のセクションの「[古いブラウザーのサポート](#sob)」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* [Chromium ブログ: 開発者: 新しい SameSite の準備 = None;セキュリティで保護された Cookie の設定](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [SameSite cookie の説明](https://web.dev/samesite-cookies-explained/)
* [2019年11月の修正プログラム](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)
