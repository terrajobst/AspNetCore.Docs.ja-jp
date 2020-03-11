---
title: ASP.NET Core MVC のキャッシュ タグ ヘルパー
author: pkellner
description: キャッシュ タグ ヘルパーを使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: db9e1a968588410f11e5f137dfdd4542df505ebc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653306"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>ASP.NET Core MVC のキャッシュ タグ ヘルパー

著者: [Peter Kellner](https://peterkellner.net)

キャッシュ タグ ヘルパーは、ASP.NET Core アプリの内容を内部 ASP.NET Core キャッシュ プロバイダーにキャッシュすることによって、アプリのパフォーマンスを改善する機能を提供します。

タグ ヘルパーの概要については、「<xref:mvc/views/tag-helpers/intro>」をご覧ください。

次の Razor マークアップは、現在の日付をキャッシュします。

```cshtml
<cache>@DateTime.Now</cache>
```

タグ ヘルパーを含むページに対する最初の要求で、現在の日付が表示されます。 キャッシュの有効期限 (既定値は 20 分) が切れるか、キャッシュされた日付がキャッシュから削除されるまで、それ以降の要求ではキャッシュされた値が表示されます。

## <a name="cache-tag-helper-attributes"></a>キャッシュ タグ ヘルパーの属性

### <a name="enabled"></a>enabled

| 属性の種類  | 例        | 既定値 |
| --------------- | --------------- | ------- |
| Boolean         | `true`, `false` | `true`  |

`enabled` によってキャッシュ タグ ヘルパーで囲まれた内容をキャッシュするかどうかが決定されます。 既定では、 `true`です。 `false` に設定すると、作成された出力はキャッシュ**されません**。

例:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>expires-on

| 属性の種類   | 例                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on` によって、特定の日時でキャッシュされた項目の有効期限を設定します。

次の例では、キャッシュ タグ ヘルパーの内容を 2025 年 1 月 29 日午後 5 時 2 分までキャッシュします。

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>expires-after

| 属性の種類 | 例                      | 既定値    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20 分 |

`expires-after` では、最初の要求があってから内容をキャッシュしている時間の長さが設定されます。

例:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Razor ビュー エンジンでは、`expires-after` の規定値が 20 分に設定されます。

### <a name="expires-sliding"></a>expires-sliding

| 属性の種類 | 例                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

ここで設定した時間だけキャッシュ エントリの値に対するアクセスがなかった場合、キャッシュ エントリを削除します。

例:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>vary-by-header

| 属性の種類 | 例                                    |
| -------------- | ------------------------------------------- |
| String         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header` には、変化したときにキャッシュの更新をトリガーする、コンマで区切ったヘッダー値のリストを指定します。

次の例では、ヘッダー値 `User-Agent` を監視します。 この例は、Web サーバーに提示されるすべての異なる `User-Agent` の内容をキャッシュします。

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>vary-by-query

| 属性の種類 | 例             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-query` には、リストのいずれかのキーの値が変化したときにキャッシュの更新をトリガーする、クエリ文字列 (<xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*>) の <xref:Microsoft.AspNetCore.Http.HttpRequest.Query*> のコンマ区切り値リストを指定します。

次の例では、`Make` と `Model` の値を監視します。 この例は、Web サーバーに提示されるすべての異なる `Make` と `Model` の内容をキャッシュします。

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>vary-by-route

| 属性の種類 | 例             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-route` には、ルート データのパラメーター値が変化したときにキャッシュの更新をトリガーする、コンマで区切ったルート パラメーター名のリストを指定します。

例:

*Startup.cs*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*Index.cshtml*:

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a>vary-by-cookie

| 属性の種類 | 例                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| String         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie` には、Cookie 値が変化したときにキャッシュの更新をトリガーする、コンマで区切った Cookie 名のリストを指定します。

次の例では、ASP.NET Core ID に関連付けられている Cookie を監視します。 ユーザーが認証されると、Id Cookie の変更によってキャッシュの更新がトリガーされます。

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>vary-by-user

| 属性の種類  | 例        | 既定値 |
| --------------- | --------------- | ------- |
| Boolean         | `true`, `false` | `true`  |

`vary-by-user` では、サインインしているユーザー (またはコンテキストのプリンシパル) が変化したときにキャッシュをリセットするかどうかを指定します。 現在のユーザーは要求コンテキストのプリンシパルとも呼ばれ、`@User.Identity.Name` を参照することにより Razor ビューで表示できます。

次の例では、現在ログインしているユーザーを監視して、キャッシュの更新をトリガーします。

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

この属性を使って、ユーザーがサインインしてからサインアウトするまでキャッシュの内容を保持します。 値を `true` に設定すると、認証サイクルによって認証されたユーザーのキャッシュが無効にされます。 ユーザーが認証されると新しい一意の Cookie 値が生成されるため、キャッシュは無効になります。 Cookie が存在しない場合、または Cookie の有効期限が切れている場合は、キャッシュは匿名状態で保持されます。 ユーザーが認証**されない**場合、キャッシュは保持されます。

### <a name="vary-by"></a>vary-by

| 属性の種類 | 例  |
| -------------- | -------- |
| String         | `@Model` |

`vary-by` を使うと、キャッシュ対象のデータをカスタマイズできます。 属性の文字列値によって参照されているオブジェクトが変化すると、キャッシュ タグ ヘルパーの内容が更新されます。 多くの場合、モデル値の文字列を連結したものがこの属性に割り当てられます。 このため、事実上連結されている値のいずれかが更新されると、キャッシュは無効になります。

次の例では、ビューをレンダリングするコントローラー メソッドは 2 つのルート パラメーター `myParam1` と `myParam2` の整数値を合計し、1 つのモデル プロパティとしてその合計値を返すものとします。 この合計が変化すると、キャッシュ タグ ヘルパーの内容がレンダリングされて、再びキャッシュされます。  

アクション:

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index.cshtml*:

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>priority

| 属性の種類      | 例                               | 既定値  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`、`Low`、`NeverRemove`, `Normal` | `Normal` |

`priority` により、組み込みのキャッシュ プロバイダーにキャッシュ削除ガイダンスを提供します。 Web サーバーは、メモリ不足になると、`Low` キャッシュ エントリを最初に削除します。

例:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

`priority` 属性によって、特定レベルのキャッシュ リテンション期間が保証されることはありません。 `CacheItemPriority` は提案するだけです。 この属性を `NeverRemove` に設定しても、キャッシュされた項目が常に保持されるという保証はありません。 詳細については、「[その他のリソース](#additional-resources)」セクションにあるトピックを参照してください。

キャッシュ タグ ヘルパーは、[メモリ キャッシュ サービス](xref:performance/caching/memory)に依存します。 サービスが追加されていない場合、キャッシュ タグ ヘルパーによってサービスが追加されます。

## <a name="additional-resources"></a>その他のリソース

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
