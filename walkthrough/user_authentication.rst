.. User authentication

ユーザ認証
------------------------------------

認証済みのユーザは、リクエストハンドラ内では ``self.current_user`` として、テンプレート内では ``current_user`` としてそれぞれ利用することができます。デフォルトでは ``current_user`` は ``None`` です。

アプリケーション内でユーザ認証を実装するには、例えばクッキーの値をもとにユーザを断定するには、リクエストハンドラ内で``get_current_user()``メソッドをオーバーライドする必要があります。下記の例では、ユーザがクッキー内に記録されているニックネームを用いてアプリケーションにログインする方法を示してします。
   
.. code-block:: python
  class BaseHandler(tornado.web.RequestHandler):
      def get_current_user(self):
          return self.get_secure_cookie("user")

  class MainHandler(BaseHandler):
      def get(self):
          if not self.current_user:
              self.redirect("/login")
              return
          name = tornado.escape.xhtml_escape(self.current_user)
          self.write("Hello, " + name)

  class LoginHandler(BaseHandler):
      def get(self):
          self.write('<html><body><form action="/login" method="post">'
                     'Name: <input type="text" name="name">'
                     '<input type="submit" value="Sign in">'
                     '</form></body></html>')

      def post(self):
          self.set_secure_cookie("user", self.get_argument("name"))
          self.redirect("/")

  application = tornado.web.Application([
      (r"/", MainHandler),
      (r"/login", LoginHandler),
  ], cookie_secret="61oETzKXQAGaYdkL5gEmGeJJFuYh7EQnp2XdTP1o/Vo=")


`Pythonデコレータ <http://www.python.org/dev/peps/pep-0318/>`の``tornado.web.authenticated``を用いてログイン済みユーザをリクエストすることができます。もしリクエストがこのデコレータが付いたメソッドまで達したときにユーザがログインしていなかったら、リクエストは``login_url``にリダイレクトされます。（``login_url``は別途アプリケーション設定を行います）上記サンプルは以下のように書き換えることができます。

.. code-block:: python
  class MainHandler(BaseHandler):
      @tornado.web.authenticated
      def get(self):
          name = tornado.escape.xhtml_escape(self.current_user)
          self.write("Hello, " + name)

  settings = {
      "cookie_secret": "61oETzKXQAGaYdkL5gEmGeJJFuYh7EQnp2XdTP1o/Vo=",
      "login_url": "/login",
  }
  application = tornado.web.Application([
      (r"/", MainHandler),
      (r"/login", LoginHandler),
  ], **settings)


もし``post()``メソッドが``authenticated``デコレータ付きで実装されていて、ユーザがログインしていなかった場合、サーバは``403``レスポンスを返します。

TornadoはGoogle OAuthのようなサードパーティの認証方式もビルトインサポートしています。詳細は`auth module <http://github.com/facebook/tornado/blob/master/tornado/auth.py>`を参照して下さい。ユーザ認証を用いたアプリケーションの例を確認したい場合はTornadoブログをご覧ください。（なおMySQLにユーザデータを保存する例も記載されています。）