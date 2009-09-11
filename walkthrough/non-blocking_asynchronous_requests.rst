.. Non-blocking, asynchronous requests

ノンブロッキング, 非同期リクエスト
----------------------------------

.. When a request handler is executed, the request is automatically finished. Since Tornado uses a non-blocking I/O style, you can override this default behavior if you want a request to remain open after the main request handler method returns using the tornado.web.asynchronous decorator.

リクエストハンドラが実行されたとき、リクエストは自動的に終了します。TornadoはノンブロッキングI/Oスタイルを使用するため、もしメインリクエストハンドラメソッドが値を返したあとに、リクエストを開けたままにしたい場合は``tornado.web.asynchronous``デコレータを用いてデフォルトの振舞いをオーバーライドすることができます。

.. When you use this decorator, it is your responsibility to call self.finish() to finish the HTTP request, or the user's browser will simply hang:
このデコレータを用いる場合は、HTTPリクエストを終了する場合は``self.finish()``メソッドをきちんと呼んであげなければいけません。そうしないとユーザのブラウザがハングしてしまいます。

.. code-block:: python

  class MainHandler(tornado.web.RequestHandler):
      @tornado.web.asynchronous
      def get(self):
          self.write("Hello, world")
          self.finish()

.. Here is a real example that makes a call to the FriendFeed API using Tornado's built-in asynchronous HTTP client:
ここでTornadoのビルトイン非同期HTTPクライアントを持ちいてFriendFeed APIを呼び出す実際の例をご紹介します。

.. code-block:: python

  class MainHandler(tornado.web.RequestHandler):
      @tornado.web.asynchronous
      def get(self):
          http = tornado.httpclient.AsyncHTTPClient()
          http.fetch("http://friendfeed-api.com/v2/feed/bret",
                     callback=self.async_callback(self.on_response))

      def on_response(self, response):
          if response.error: raise tornado.web.HTTPError(500)
          json = tornado.escape.json_decode(response.body)
          self.write("Fetched " + str(len(json["entries"])) + " entries "
                     "from the FriendFeed API")
          self.finish()

.. When get() returns, the request has not finished. When the HTTP client eventually calls on_response(), the request is still open, and the response is finally flushed to the client with the call to self.finish().

``get()``メソッドが値を戻しても、リクエストは終わっていません。いずれまたHTTPクライアントが``on_response()``メソッドを呼び出し、リクエストがまだ開いていたとき、レスポンスは最終的には``self.finish()``を呼び出すことでクライアントにフラッシュされます。

.. If you make calls to asynchronous library functions that require a callback (like the HTTP fetch function above), you should always wrap your callbacks with self.async_callback. This simple wrapper ensures that if your callback function raises an exception or has a programming error, a proper HTTP error response will be sent to the browser, and the connection will be properly closed.

もしコールバックを要求するような非同期ライブラリ関数を呼び出す場合は、コールバックを``self.async_callback``で常にラップすべきです。（例えば上の例でのHTTPフェッチ関数のような場合です）このシンプルなラッパーを用いることによって、コールバック関数が例外を発生させた場合あるいはプログラミングエラーがあった場合に、適切なHTTPエラーレスポンスがブラウザに送信され、コネクションが適切に閉じられることが確実になります。

.. For a more advanced asynchronous example, take a look at the chat example application, which implements an AJAX chat room using long polling.

さらに上級的な例を参考したい場合は、ロングポーリングを用いてAJAXのチャットルームを実装した例を見てください。


