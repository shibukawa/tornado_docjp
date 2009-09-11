============================
Third party authentication
============================

.. Tornado's auth module implements the authentication and authorization protocols for a number of the most popular sites on the web, including Google/Gmail, Facebook, Twitter, Yahoo, and FriendFeed. 
.. The module includes methods to log users in via these sites and, where applicable, methods to authorize access to the service so you can, e.g., download a user's address book or publish a Twitter message on their behalf.

Tornadoの認証モジュールは、いくつかのメジャーなWebサービスの認証と承認に対応しています。サービスは、Google/Gmail、Facebook、Twitter、Yahoo、FriendFeedが利用出来ます。このモジュールを使う事で、これらのサイトに、認証済みのアクセスを出来ます。
例えばあなたのアドレスブックに載っている友達のTwitterのメッセージをダウンロードできます。

.. Here is an example handler that uses Google for authentication,saving the Google credentials in a cookie for later access:

参考にグーグルの認証を使用したサンプルを紹介します。 このサンプルは、継続アクセスの為にグーグルの認証済みクッキーを保存します。

.. code-block:: python

  class GoogleHandler(tornado.web.RequestHandler, tornado.auth.GoogleMixin):
      @tornado.web.asynchronous
      def get(self):
          if self.get_argument("openid.mode", None):
              self.get_authenticated_user(self.async_callback(self._on_auth))
              return
          self.authenticate_redirect()

      def _on_auth(self, user):
          if not user:
              self.authenticate_redirect()
              return
          # Save the user with, e.g., set_secure_cookie()

更に詳しい情報は、認証モジュール（auth module)の章を参照してください。

