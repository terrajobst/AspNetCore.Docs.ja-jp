---
page_type: sample
description: このチュートリアルは、MongoDB NoSQL データベースを使用して ASP.NET Core Web API を作成する方法を説明します。
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 402b25f3f7c1644a52832b5c8566269773932e95
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886293"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>ASP.NET Core と MongoDB で Web API を作成する

このチュートリアルでは、[MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL データベース上で Create、Read、Update、Delete の各操作 (CRUD) を実行するWeb API を作成します。

このチュートリアルでは、次の作業を行う方法について説明します。

* MongoDB を構成する
* MongoDB データベースを作成する
* MongoDB のコレクションとスキーマを定義する
* Web API から MongoDB CRUD 操作を実行する
* JSON のシリアル化のカスタマイズ

## <a name="prerequisites"></a>必須コンポーネント

* [.NET Core SDK 3.0 以降](https://www.microsoft.com/net/download/all)
* [Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) と **ASP.NET および Web 開発**ワークロード
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a>MongoDB を構成する

Windows を使用する場合、MongoDB は既定では *C:\\Program Files\\MongoDB* にインストールされます。 *C:\\Program Files\\MongoDB\\Server\\\<バージョン番号>\\bin* を `Path` 環境変数に追加します。 この変更により、開発用コンピューターのどこからでも MongoDB にアクセスできるようになります。

次の手順では mongo シェルを使用して、データベースを作成し、コレクションを作成し、ドキュメントを保存します。 mongo のシェル コマンドについて詳しくは、「[Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)」(mongo シェルの使用) をご覧ください。

1. データを格納するために開発用コンピューター上のディレクトリを選択します。 たとえば、Windows では *C:\\BooksData* です。 存在しない場合はディレクトリを作成します。 mongo シェルでは新しいディレクトリは作成されません。
1. コマンド シェルを開きます。 次のコマンドを実行して、既定のポート 27017 で MongoDB に接続します。 忘れずに、前の手順で選択したディレクトリで `<data_directory_path>` を置き換えます。

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. 別のコマンド シェル インスタンスを開きます。 次のコマンドを実行して、既定のテスト データベースに接続します。

    ```console
    mongo
    ```

1. コマンド シェルで次を実行します。

    ```console
    use BookstoreDb
    ```

    これがまだ存在していない場合、*BookstoreDb* という名前のデータベースが作成されます。 データベースが存在する場合は、トランザクションのために接続されます。

1. 次のコマンドを使用して `Books` コレクションを作成します。

    ```console
    db.createCollection('Books')
    ```

    次のような結果が表示されます。

    ```console
    { "ok" : 1 }
    ```

1. 次のコマンドを使用して、`Books` コレクションのスキーマを定義し、2 つのドキュメントを挿入します。

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    次のような結果が表示されます。

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

  > [!NOTE]
  > この記事に示されている ID は、このサンプルを実行するときの ID とは一致していません。

1. 次のコマンドを使用して、データベース内のドキュメントを表示します。

    ```console
    db.Books.find({}).pretty()
    ```

    次のような結果が表示されます。

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    スキーマによって、自動生成された型が `ObjectId` の `_id` プロパティが各ドキュメントに追加されます。

データベースの準備ができました。 ASP.NET Core Web API の作成を開始できます。

## <a name="create-the-aspnet-core-web-api-project"></a>ASP.NET Core Web API プロジェクトを作成する

1. **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** に移動します。
1. **[ASP.NET Core Web アプリケーション]** プロジェクトの種類を選択し、 **[次へ]** を選択します。
1. プロジェクトに *BooksApi* という名前を付けて、 **[作成]** を選択します。
1. **[.NET Core]** ターゲット フレームワークと **[ASP.NET Core 3.0]** を選択します。 **[API]** プロジェクト テンプレートを選択し、 **[作成]** を選択します。
1. [NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。 **[パッケージ マネージャー コンソール]** ウィンドウで、プロジェクトのルートに移動します。 次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a>モデルにエンティティを追加する

1. *Models* ディレクトリをプロジェクトのルートに追加します。
1. 次のコードを使用して、`Book` クラスを *Models* ディレクトリに追加します。

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;

    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }

            [BsonElement("Name")]
            public string BookName { get; set; }

            public decimal Price { get; set; }

            public string Category { get; set; }

            public string Author { get; set; }
        }
    }
    ```

    上記のクラスでは、`Id` プロパティは:

    * 共通言語ランタイム (CLR) オブジェクトを MongoDB コレクションにマッピングするために必須です。
    * ドキュメントの主キーとしてこのプロパティを指定するために、[[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) で注釈を付けられています。
    * パラメーターを [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 構造体ではなく型 `string` として渡すことができるように、[[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) で注釈が付けられています。 Mongo によって `string` から `ObjectId` への変換が処理されます。

    `BookName` プロパティには、[[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 属性を使用して注釈が付けられます。 属性の値 `Name` は、MongoDB コレクションでのプロパティ名を表します。

## <a name="add-a-configuration-model"></a>構成モデルを追加する

1. 次のデータベース構成値を *appsettings.json* に追加します。

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. 次のコードを使用して、*BookstoreDatabaseSettings.cs* ファイルを *Models* ディレクトリに追加します。

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    前述の `BookstoreDatabaseSettings` クラスは、*appsettings.json* ファイルの `BookstoreDatabaseSettings` プロパティ値を格納するために使用されます。 JSON と C# のプロパティ名には、マッピング処理を簡単にするために同じ名前が付けられています。

1. 次の強調表示されたコードを `Startup.ConfigureServices` に追加します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    上のコードでは以下の操作が行われます。

    * *appsettings.json* ファイルの `BookstoreDatabaseSettings` セクションがバインドされている構成インスタンスは、Dependency Injection (DI) コンテナーに登録されています。 たとえば、`BookstoreDatabaseSettings` オブジェクトの `ConnectionString` プロパティには、*appsettings.json* の `BookstoreDatabaseSettings:ConnectionString` プロパティが設定されています。
    * `IBookstoreDatabaseSettings` インターフェイスは、シングルトン [サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で DI に登録されています。 挿入されると、インターフェイス インスタンスは `BookstoreDatabaseSettings` オブジェクトに解決されます。

1. 次のコードを *Startup.cs* の先頭に追加して、`BookstoreDatabaseSettings` と `IBookstoreDatabaseSettings` の参照を解決します。

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a>CRUD 操作のサービスを追加する

1. *Services* ディレクトリをプロジェクトのルートに追加します。
1. 次のコードを使用して、`BookService` クラスを *Services* ディレクトリに追加します。

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    前述のコードでは、`IBookstoreDatabaseSettings` インスタンスがコンストラクターの挿入によって DI から取得されます。 この手法で、「[構成モデルを追加する](#add-a-configuration-model)」セクションで追加した *appsettings.json* 構成値にアクセスできます。

1. 次の強調表示されたコードを `Startup.ConfigureServices` に追加します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    前述のコードでは、消費クラスへのコンストラクターの挿入をサポートする `BookService` クラスが DI に登録されています。 `BookService` が `MongoClient` に直接依存しているため、シングルトン サービスの有効期間が最も適切です。 公式の [Mongo Client 再利用ガイドライン](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)に従い、シングルトン サービスの有効期間を使用して DI に `MongoClient` を登録する必要があります。

1. 次のコードを *Startup.cs* の先頭に追加して、`BookService` の参照を解決します。


    ```csharp
    using BooksApi.Services;
    ```

`BookService` クラスは次の `MongoDB.Driver` メンバーを使用して、データベースに対する CRUD 操作を実行します。

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; データベース操作を実行するためにサーバー インスタンスを読み取ります。 このクラスのコンストラクターには MongoDB 接続文字列が提供されます。

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; 操作を実行する Mongo データベースを表します。 このチュートリアルでは、インターフェイスで汎用の [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) メソッドを使って、特定のコレクション内のデータにアクセスします。 このメソッドが呼び出された後に、CRUD 操作をこのコレクションに対して実行します。 `GetCollection<TDocument>(collection)` メソッドの呼び出しの内容は次のとおりです。
  * `collection` はコレクション名です。
  * `TDocument` はコレクションに格納されている CLR オブジェクト型です。

`GetCollection<TDocument>(collection)` から、コレクションを表す [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) オブジェクトが返されます。 このチュートリアルでは、コレクションに対して次のメソッドを呼び出します。

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; 指定された検索基準と一致する 1 つのドキュメントを削除します。
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; 指定された検索基準と一致するコレクション内のすべてのドキュメントを返します。
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; 指定されたオブジェクトを新しいドキュメントとしてコレクションに挿入します。
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; 指定された検索基準と一致する 1 つのドキュメントを、指定されたオブジェクトで置き換えます。

## <a name="add-a-controller"></a>コントローラーの追加

次のコードを使用して、`BooksController` クラスを *Controllers* ディレクトリに追加します。

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

上記の Web API コントローラー:

* `BookService` クラスを使用して CRUD 操作を実行します。
* GET、POST、PUT、DELETE HTTP 要求をサポートするアクション メソッドが含まれます。
* `Create` アクション メソッドで <xref:System.Web.Http.ApiController.CreatedAtRoute*> を呼び出して、[HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 応答を返します。 状態コード 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。 `CreatedAtRoute` によって、応答に `Location` ヘッダーも追加されます。 `Location` ヘッダーでは、新しく作成されたブックの URI を指定します。

## <a name="test-the-web-api"></a>Web API をテストする

1. アプリケーションをビルドし、実行します。

1. `http://localhost:<port>/api/books` に移動して、コントローラーのパラメーターなしの `Get` アクション メソッドをテストします。 次の JSON 応答が示されます。

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

1. `http://localhost:<port>/api/books/{id here}` に移動して、コントローラーのオーバーロードされた `Get` アクション メソッドをテストします。 次の JSON 応答が示されます。

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a>JSON シリアル化オプションを構成する

「[Web API をテストする](#test-the-web-api)」セクションで返される JSON 応答について変更すべき 2 つの詳細があります。

* プロパティ名の既定の camel 形式は、CLR オブジェクトのプロパティ名の Pascal 形式と一致するように変更する必要があります
* `bookName` プロパティは `Name` として返される必要があります。

上記の要件を満たすには、次の変更を行います。

1. JSON.NET は ASP.NET 共有フレームワークから削除されています。 [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) へのパッケージ参照を追加します。

1. `Startup.ConfigureServices` で、次の強調表示されたコードを `AddMvc` メソッド呼び出しにチェーンします。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    上記の変更により、Web API のシリアル化された JSON 応答内のプロパティ名は、CLR のオブジェクトの種類での対応するプロパティ名と一致しています。 たとえば、`Book` クラスの `Author` プロパティは `Author` としてシリアル化されます。

1. *Models/Book.cs* では、`BookName` プロパティに次の [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 属性を使用して注釈を付けます。

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    `[JsonProperty]`属性の値 `Name` は、Web API のシリアル化された JSON 応答内のプロパティ名を表します。

1. 次のコードを *Models/Book.cs* の先頭に追加して、`[JsonProperty]` 属性の参照を解決します。

    ```csharp
    using Newtonsoft.Json;
    ```

1. 「[Web API をテストする](#test-the-web-api)」セクションで定義されている手順を繰り返します。 JSON プロパティ名の違いに注意してください。

## <a name="next-steps"></a>次の手順

ASP.NET Core Web API の構築について詳しくは、次のリソースを参照してください。

* [この記事の YouTube バージョン](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [ASP.NET Core を使って Web API を作成する](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
