# <a name="additional-claims-sample-app"></a>追加の要求のサンプルアプリ

サンプルアプリでは、次の方法を示しています。

* Google からユーザーの名前と姓を取得し、Google から提供された値を使用して名前クレームを格納します。
* Google アクセストークンをユーザーの `AuthenticationProperties`に格納します。

サンプルアプリを使用するには:

1. アプリを登録し、Google 認証用の有効なクライアント ID とクライアントシークレットを取得します。 詳細については、「 [Google external login setup](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins)」を参照してください。
1. `Startup.ConfigureServices`の[GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)で、クライアント ID とクライアントシークレットをアプリに提供します。
1. アプリを実行し、[要求] ページを要求します。 ユーザーがサインインしていない場合、アプリは Google にリダイレクトします。 Google でサインインします。 Google は、ユーザーをアプリにリダイレクトします (`/MyClaims`)。 ユーザーが認証され、[自分の要求] ページが読み込まれます。 指定された名前と姓の要求は、Google によって指定された値を持つ**ユーザー要求**の下に存在します。 アクセストークンは **[認証プロパティ]** の下に表示されます。
