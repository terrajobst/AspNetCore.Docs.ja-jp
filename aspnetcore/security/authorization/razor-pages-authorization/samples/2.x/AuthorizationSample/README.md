# <a name="aspnet-core-authorization-sample"></a>ASP.NET Core 承認のサンプル

このサンプルでは、規約による Razor Pages 承認の使用方法を示します。 このサンプルでは、 [Razor Pages の承認規則](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization)に関するトピックで説明されている機能を示します。

このサンプルのユーザー承認では、 [ASP.NET Core id を使用しない cookie 認証の使用](https://docs.microsoft.com/aspnet/core/security/authentication/cookie)に関するトピックで説明されている cookie 認証機能を使用します。 このトピックで示す概念と例は、ASP.NET Core Id を使用するアプリにも同様に適用されます。 ASP.NET Core Id の使用の詳細については、「 [ASP.NET Core の id の概要](https://docs.microsoft.com/aspnet/core/security/authentication/identity)」を参照してください。

電子メールアドレス **maria.rodriguez@contoso.com** を使用して、任意のパスワードを使用してユーザーを認証します。 ユーザーは、 *Pages/Account/Login. cshtml. .cs*ファイルの `AuthenticateUser` メソッドで認証されます。 実際の例では、ユーザーはデータベースに対して認証されます。

## <a name="examples-in-this-sample"></a>このサンプルの例

| 機能 | 説明 |
| --- | --- |
| [[AuthorizePage]](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | 指定されたパスのページに[Authorizefilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を追加します。 |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | 指定されたパスを持つフォルダー内のすべてのページに、 [Authorizefilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)を追加します。 |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | 指定されたパスのページに[AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter)を追加します。 |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | 指定されたパスを持つフォルダー内のすべてのページに[AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter)を追加します。 |
