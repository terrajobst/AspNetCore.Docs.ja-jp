---
title: ASP.NET Core でのモデル バインド
author: rick-anderson
description: ASP.NET Core でのモデル バインドのしくみと、その動作のカスタマイズ方法について説明します。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 11/15/2019
uid: mvc/models/model-binding
ms.openlocfilehash: a025419a5b4d2c2e3e5c5a7850df281ddd3164ea
ms.sourcegitcommit: f91d322f790123d41ec3271fa084ae20ed9f89a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155043"
---
# <a name="model-binding-in-aspnet-core"></a>ASP.NET Core でのモデル バインド

この記事では、モデル バインドとは何か、そのしくみ、その動作のカスタマイズ方法を説明します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="what-is-model-binding"></a>モデル バインドとは何か

コントローラーおよび Razor Pages では、HTTP 要求からのデータが使用されます。 たとえば、ルート データからはレコード キーが提供され、ポストされたフォーム フィールドからはモデルのプロパティ用の値が提供されます。 これらの各値を取得してそれらを文字列から .NET 型に変換するためのコードを記述するのは、面倒で間違いも起こりやすいでしょう。 モデル バインドを使用すれば、このプロセスを自動化できます。 モデル バインド システムでは次のことが行われます。

* ルート データ、フォーム フィールド、クエリ文字列などのさまざまなソースからデータを取得します。
* メソッド パラメーターとパブリック プロパティでコントローラーと Razor Pages にデータを提供します。
* 文字列データを .NET 型に変換します。
* 複合型のプロパティを更新します。

## <a name="example"></a>例

次のアクション メソッドがあるとします。

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

さらにアプリでは、この URL を使用して要求が受信されます。

```
http://contoso.com/api/pets/2?DogsOnly=true
```

ルーティング システムでアクション メソッドが選択されたら、モデル バインドでは次の手順が実行されます。

* `GetByID` の最初のパラメーター (`id` という名前の整数型) を検索します。
* HTTP 要求内で利用可能なソースを調べ、ルート データ内で `id` = "2" を検索します。
* 文字列 "2" を整数 2 に変換します。
* `GetByID` の次のパラメーター (`dogsOnly` という名前のブール型) を検索します。
* 該当するソース内を調べ、クエリ文字列内で "DogsOnly=true" を検索します。 名前の照合では大文字と小文字が区別されません。
* 文字列 "true" をブール型の `true` に変換します。

次にフレームワークによって `GetById` メソッドが呼び出され、`id` パラメーターには 2 が、`dogsOnly` パラメーターには `true` が渡されます。

上記の例で、モデル バインディング ターゲットは単純型のメソッド パラメーターになっています。 ターゲットは複合型のプロパティになる場合もあります。 各プロパティが正常にバインドされたら、そのプロパティに対して[モデル検証](xref:mvc/models/validation)が行われます。 どのようなデータがモデルにバインドされているかを示す記録、バインド エラー、または検証のエラーは、[ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) または [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) に格納されます。 このプロセスが正常終了したかどうかを確認するために、アプリでは [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) フラグが調べられます。

## <a name="targets"></a>ターゲット

モデル バインドでは、次の種類のターゲットの値について検索が試みられます。

* 要求のルーティング先であるコントローラー アクション メソッドのパラメーター。
* 要求のルーティング先である Razor Pages ハンドラー メソッドのパラメーター。 
* 属性によって指定されている場合は、コントローラーまたは `PageModel` クラスのパブリック プロパティ。

### <a name="bindproperty-attribute"></a>[BindProperty] 属性

コントローラーまたは `PageModel` クラスのパブリック プロパティに適用できます。これによってモデル バインドはそのプロパティをターゲットとするようになります。

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a>[BindProperties] 属性

ASP.NET Core 2.1 以降で使用できます。  コントローラーまたは `PageModel` クラスに適用できます。これによってモデル バインドはクラスのすべてのパブリック プロパティをターゲットとするように指示されます。

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>HTTP GET 要求のモデル バインド

既定では、プロパティは HTTP GET 要求にバインドされません。 通常、GET 要求に必要なのはレコード ID パラメーターのみです。 レコード ID は、データベース内の項目の検索に使用されます。 そのため、モデルのインスタンスを保持するプロパティをバインドする必要はありません。 GET 要求からのデータにプロパティがバインドされるようにするシナリオでは、`SupportsGet` プロパティを `true` に設定します。

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>ソース

既定では、モデル バインドでは HTTP 要求内の次のソースからキーと値のペアの形式でデータが取得されます。

1. フォーム フィールド 
1. 要求本文 ([[ApiController] 属性を持つコントローラー](xref:web-api/index#binding-source-parameter-inference) の場合)。
1. ルート データ
1. クエリ文字列のパラメーター
1. アップロード済みのファイル 

ターゲット パラメーターまたはプロパティごとに、この一覧に示されている順序でソースがスキャンされます。 次のようにいくつかの例外があります。

* ルート データとクエリ文字列の値は単純型にのみ使用されます。
* アップロード済みのファイルは、`IFormFile` または `IEnumerable<IFormFile>` を実装するターゲットの種類にのみバインドされます。

既定の動作によって正しい結果が得られない場合は、次のいずれかの属性を使用して、特定のターゲットに使用するソースを指定できます。 

* [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - クエリ文字列から値を取得します。 
* [[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - ルート データから値を取得します。
* [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - ポストされたフォーム フィールドから値を取得します。
* [[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - 要求本文から値を取得します。
* [[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - HTTP ヘッダーから値を取得します。

これらの属性:

* 次の例のように、(モデル クラスにではなく) モデル プロパティに個別に追加されます。

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* 必要に応じて、コンストラクター内のモデル名の値を受け取ります。 このオプションは、プロパティ名と要求内の値とが一致しない場合に指定されます。 たとえば、要求内の値は、次の例のようにその名前にハイフンが含まれている場合、ヘッダーである可能性があります。

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>[FromBody] 属性

要求本文のデータは、要求のコンテンツの種類に固有の入力フォーマッタを使用して解析されます。 入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。

アクション メソッドごとに `[FromBody]` を複数のパラメーターに適用しないでください。 ASP.NET Core ランタイムでは、要求ストリームを読み取る責任が入力フォーマッタに委任されます。 要求ストリームがいったん読み取られると、他の `[FromBody]` パラメーターをバインドするためにそれを再度読み取ることはできません。

### <a name="additional-sources"></a>その他のソース

ソース データは、"*値プロバイダー*" によってモデル バインド システムに提供されます。 モデル バインド用に、他のソースからデータを取得するカスタムの値プロバイダーを作成して登録することができます。 たとえば、cookie またはセッション状態からのデータが必要だとします。 新しいソースからデータを取得するには:

* `IValueProvider` を実装するクラスを作成します。
* `IValueProviderFactory` を実装するクラスを作成します。
* `Startup.ConfigureServices` 内のファクトリ クラスを登録します。

サンプル アプリには、cookie から値を取得する[値プロバイダー](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs)と[ファクトリ](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs)の例が含まれています。 `Startup.ConfigureServices` 内の登録コードを次に示します。

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

表示したコードでは、すべての組み込み値プロバイダーの後にカスタムの値プロバイダーが配置されています。  それをリストの最初に持ってくるには、`Add` ではなく `Insert(0, new CookieValueProviderFactory())` を呼び出します。

## <a name="no-source-for-a-model-property"></a>モデル プロパティ用のソースがない

既定では、モデル プロパティ用の値が見つからない場合、モデル状態エラーは作成されません。 プロパティは次のように null 値または既定値に設定されます。

* null 許容単純型は `null` に設定されます。
* null 非許容値型は `default(T)` に設定されます。 たとえば、パラメーター `int id` は 0 に設定されます。
* 複合型の場合、モデル バインドでは、プロパティを設定せずに既定のコンストラクターを使用して、インスタンスが作成されます。
* 配列は `Array.Empty<T>()` に設定されます。例外として、`byte[]` 配列は `null` に設定されます。

モデル プロパティ用のフォーム フィールド内で何も見つからないときモデル状態を無効にする必要がある場合は、[[BindRequired] 属性](#bindrequired-attribute)を使用します。

この `[BindRequired]` 動作は、要求本文内の JSON または XML データに対してではなく、ポストされたフォーム データからのモデル バインドに適用されることに注意してください。 要求本文データは、[入力フォーマッタ](#input-formatters)によって処理されます。

## <a name="type-conversion-errors"></a>型変換エラー

ソースは見つかってもそれをターゲットの種類に変換できない場合、無効であることを示すフラグがモデル状態に付けられます。 前のセクションで説明したように、ターゲットのパラメーターまたはプロパティは null または既定値に設定されます。

`[ApiController]` 属性を持つ API コントローラーでは、モデル状態が無効であると、HTTP 400 の自動応答が生成されます。

Razor ページでは、エラー メッセージを含むページが再表示されます。

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

クライアント側の検証では、それを行わないなら Razor Pages フォームに送信されてしまう不適切なデータのほとんどがキャッチされます。 この検証により、前の強調表示されたコードをトリガーするのが難しくなります。 サンプル アプリには、 **[Submit with Invalid Date]** ボタンが含まれており、これを使用すると、 **[Hire Date]** フィールドに不適切なデータが入力され、そのフォームが送信されます。 このボタンを使用すると、データ変換エラーが発生したときにページを再表示するためのコードがどのように機能するかを表示できます。

上のコードでページが再表示されると、無効な入力はフォーム フィールドに表示されません。 これは、モデル プロパティが null または既定値に設定されているためです。 無効な入力はエラー メッセージに表示されます。 しかし、フォーム フィールドに不適切なデータを再表示したい場合は、モデル プロパティを文字列にしてデータ変換を手動で行うことを検討してください。

型変換エラーが結果的にモデル状態エラーになることを望まない場合も同じ方法をお勧めします。 その場合は、モデル プロパティを文字列にします。

## <a name="simple-types"></a>単純型

モデル バインダーでソース文字列の変換先とすることができる単純型には次のものがあります。

* [Boolean](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Decimal](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [Enum](xref:System.ComponentModel.EnumConverter)
* [Guid](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)
* [Uri](xref:System.UriTypeConverter)
* [Version](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>複合型

複合型には、バインドする既定のパブリック コンストラクターと書き込み可能なパブリック プロパティが必要です。 モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化されます。 

複合型のプロパティごとに、モデル バインドでは名前パターン *prefix.property_name* がないかソースが調べられます。 何も見つからない場合は、プレフィックスなしで *property_name* だけが探索されます。

パラメーターにバインドする場合、プレフィックスはパラメーター名です。 `PageModel` パブリック プロパティにバインドする場合、プレフィックスはパブリック プロパティ名です。 一部の属性には、パラメーター名またはプロパティ名の既定の使用をオーバーライドするための `Prefix` プロパティがあります。

たとえば、複合型が次の `Instructor` クラスであるとします。

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>プレフィックス = パラメーター名

バインドされるモデルが `instructorToUpdate` という名前のパラメーターである場合:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

モデル バインドでは、キー `instructorToUpdate.ID` がないかソースを調べることから始まります。 見つからない場合は、プレフィックスなしで `ID` が探索されます。

### <a name="prefix--property-name"></a>プレフィックス = プロパティ名

バインドされるモデルがコントローラーの `Instructor` という名前のプロパティか、または `PageModel` クラスである場合:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。 見つからない場合は、プレフィックスなしで `ID` が探索されます。

### <a name="custom-prefix"></a>カスタム プレフィックス

バインドされるモデルが `instructorToUpdate` という名前のパラメーターであり、かつ `Bind` 属性でプレフィックスとして `Instructor` が指定されている場合:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。 見つからない場合は、プレフィックスなしで `ID` が探索されます。

### <a name="attributes-for-complex-type-targets"></a>複合型のターゲットの属性

複合型のモデル バインドを制御するために利用できる組み込みの属性がいくつかあります。

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> ポストされたフォーム データが値のソースである場合、これらの属性はモデル バインドに影響します。 ポストされた JSON および XML 要求本文を処理する入力フォーマッタには影響しません。 入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。
>
> [モデル検証](xref:mvc/models/validation#required-attribute)に関するページにある `[Required]` 属性の説明も参照してください。

### <a name="bindrequired-attribute"></a>[BindRequired] 属性

モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。 モデルのプロパティに対してバインドを実行できない場合に、モデル バインドがモデル状態エラーを追加できるようにします。 次に例を示します。

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>[BindNever] 属性

モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。 モデル バインドがモデルのプロパティを設定できないようにします。 次に例を示します。

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>[Bind] 属性

クラスまたはメソッド パラメーターに適用できます。 モデルのどのプロパティをモデル バインドに含めるかを指定します。

次の例では、任意のハンドラーまたはアクション メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

次の例では、`OnPost` メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

`[Bind]` 属性を使用すれば、"*作成*" シナリオにおいて過剰ポスティングから保護することができます。 除外されたプロパティはそのままにしておくのではなく null または既定値に設定されるので、この属性は編集シナリオではうまく機能しません。 過剰ポスティングを防ぐ場合は、`[Bind]` 属性ではなくビュー モデルをお勧めします。 詳細については、「[過剰ポスティングに関するセキュリティの注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)」を参照してください。

## <a name="collections"></a>コレクション

ターゲットが単純型のコレクションである場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。 一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。 次に例を示します。

* バインドされるパラメーターが `selectedCourses` という名前の配列であるとした場合:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* フォームまたはクエリ文字列データは、次のいずれかの形式とすることができます。
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* 次の形式は、フォーム データでのみサポートされます。

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* 上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る配列が `selectedCourses` パラメーターに渡されます。

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  添え字番号 (... [0] ... [1] ...) を使用するデータ フォーマットでは、確実にそれらがゼロから始まる連続した番号になるようにする必要があります。 添え字の番号付けで欠落している番号がある場合、欠落している番号の後の項目はすべて無視されます。 たとえば、添え字が 0、1 の並びではなく、0、2 の並びで振られている場合、2 番目の項目は無視されます。

## <a name="dictionaries"></a>ディクショナリ

`Dictionary` ターゲットの場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。 一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。 次に例を示します。

* ターゲット パラメーターが `selectedCourses` という名前の `Dictionary<int, string>` であるとします:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* ポストされたフォームまたはクエリ文字列データは、次のいずれかの例のようになります。

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* 上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る辞書が `selectedCourses` パラメーターに渡されます。

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"

## <a name="special-data-types"></a>特別なデータ型

モデル バインドで処理できる特殊なデータ型がいくつかあります。

### <a name="iformfile-and-iformfilecollection"></a>IFormFile と IFormFileCollection

HTTP 要求に含まれたアップロード済みファイル。  また、複数のファイルに対して `IEnumerable<IFormFile>` もサポートされています。

### <a name="cancellationtoken"></a>CancellationToken

非同期コントローラーでアクティビティをキャンセルするために使用されます。

### <a name="formcollection"></a>FormCollection

ポストされたフォーム データからすべての値を取得するために使用します。

## <a name="input-formatters"></a>入力フォーマッタ

要求本文内のデータは、JSON、XML、またはその他のいくつかの形式にすることができます。 このデータを解析するために、モデル バインドでは、特定のコンテンツの種類を処理するように構成された "*入力フォーマッタ*" が使用されます。 既定では、ASP.NET Core には JSON データ処理用の JSON ベースの入力フォーマッタが含まれます。 他のコンテンツの種類については対応する他のフォーマッタを追加することができます。

ASP.NET Core では、[Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性に基づいて入力フォーマッタが選択されます。 属性が存在しない場合は、[Content-Type ヘッダー](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)が使用されます。

組み込みの XML 入力フォーマッタを使用するには:

* `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet パッケージをインストールします。

* `Startup.ConfigureServices` で、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> または <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出します。

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* 要求本文で XML を必要とするコントローラー クラスまたはアクション メソッドに `Consumes` 属性を適用します。

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  詳細については、「[XML シリアル化の概要](/dotnet/standard/serialization/introducing-xml-serialization)」を参照してください。

## <a name="exclude-specified-types-from-model-binding"></a>指定された型をモデル バインドから除外する

モデル バインドおよび検証システムの動作は、[ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。 `ModelMetadata` については、詳細プロバイダーを [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) に追加してカスタマイズできます。 組み込みの詳細プロバイダーは、指定された型に対してモデル バインドまたは検証を無効にする場合に使用できます。

指定された型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> を追加します。 たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

指定された型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> を追加します。 たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>カスタム モデル バインダー

モデル バインドを拡張するには、カスタム モデル バインダーを記述し、`[ModelBinder]` 属性を使用してそれを特定のターゲット向けに選択します。 詳細については、「[custom model binding](xref:mvc/advanced/custom-model-binding)」 (カスタム モデル バインド) を参照してください。

## <a name="manual-model-binding"></a>手動によるモデル バインド

モデル バインドは、<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> メソッドを使用して手動で呼び出すことができます。 このメソッドは `ControllerBase` クラスと `PageModel` クラスの両方で定義されています。 メソッドのオーバーロードにより、使用するプレフィックスと値プロバイダーを指定できます。 モデル バインドが失敗した場合は、メソッドから `false` が返されます。 次に例を示します。

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>[FromServices] 属性

この属性の名前は、データ ソースを指定するモデル バインド属性のパターンに従います。 ただし、それは、値プロバイダーからのデータ バインドを説明するものではありません。 [依存関係挿入](xref:fundamentals/dependency-injection)コンテナーから型のインスタンスが取得されます。 その目的は、特定のメソッドが呼び出された場合にのみサービスを必要するときにコンストラクターの挿入の代替手段を提供することにあります。

## <a name="additional-resources"></a>その他の技術情報

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
