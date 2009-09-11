.. Cookies and secure cookies

クッキーと安全なクッキー
------------------------------------

.. ユーザのブラウザにクッキーを残したい場合はset_cookie()メソッドを用います
   
.. code-block:: python
  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          if not self.get_cookie("mycookie"):
              self.set_cookie("mycookie", "myvalue")
              self.write("Your cookie was not set yet!")
          else:
              self.write("Your cookie was set!")


.. クッキーは悪意のあるクライアントによって容易に偽装されてしまいます。例えば現在ログインしているユーザのユーザIDを保存するためにクッキーをセットしたい場合は、偽造を防ぐためにあなたのクッキーを署名する必要があります。Tornadoではインストール直後でもset_secure_cookie()とget_secure_cookie()メソッドを用いることでこれを実現できます。これらのメソッドを用いるにはアプリケーションを構築する際にcookie_secretという秘密鍵を指定する必要があります。これはアプリケーション設定内でキーワード引数としてアプリケーションに渡すことができます。

.. code-block:: python

  application = tornado.web.Application([
      (r"/", MainHandler),
  ], cookie_secret="61oETzKXQAGaYdkL5gEmGeJJFuYh7EQnp2XdTP1o/Vo=")


.. 署名済みクッキーにはタイムスタンプとHMAC署名に加えてクッキーのエンコードされた値が含まれています。もしクッキーが古いあるいは署名が適合しなければ、get_secure_cookie()メソッドがあたかもクッキーがセットされていないかのようにNoneを返します。上記の例を安全なクッキーとして設定する場合は以下のようなコードになります。

.. code-block:: python
  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          if not self.get_secure_cookie("mycookie"):
              self.set_secure_cookie("mycookie", "myvalue")
              self.write("Your cookie was not set yet!")
          else:
              self.write("Your cookie was set!")

