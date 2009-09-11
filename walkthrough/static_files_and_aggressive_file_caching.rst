.. Static files and aggressive file caching


静的ファイルと積極的なファイルキャッシング
------------------------------------

.. Tornadoで静的ファイルを'serve'するにはアプリケーション設定で``static_path``を指定する必要があります。

.. code-block:: python
  settings = {
      "static_path": os.path.join(os.path.dirname(__file__), "static"),
      "cookie_secret": "61oETzKXQAGaYdkL5gEmGeJJFuYh7EQnp2XdTP1o/Vo=",
      "login_url": "/login",
      "xsrf_cookies": True,
  }
  application = tornado.web.Application([
      (r"/", MainHandler),
      (r"/login", LoginHandler),
  ], **settings)


.. この設定では ``/static/`` で始まるすべてのリクエストを自動的に静的なディレクトリからの'serve'とすることができます。例えば ``http://localhost:8888/static/foo.png <http://localhost:8888/static/foo.png>`` というURLの場合は ``foo.png`` というファイルを指定された静的ディレクトリから 'serve' します。また ``/robots.txt`` や ``/favicon.ico`` も静的ディレクトリから自動的に配信されます。（たとえURLが``/static``から始まらなくても）

.. パフォーマンス向上のために、静的リソースをブラウザが積極的にキャッシュすることは一般的な定石です。これによってブラウザはページのレンダリングを妨げる不必要な``If-Modified-Since``や``Etag``リクエストを送信しなくなります。

.. この機能を利用するには、テンプレートの中で静的ファイルのURLをHTML内で直接記述するのではなく``static_url()``メソッドを使用します。

.. code-block:: html
  <html>
     <head>
        <title>FriendFeed - {{ _("Home") }}</title>
     </head>
     <body>
        <div><img src="{{ static_url("images/logo.png") }}"/></div>
     </body>
  </html>


.. ``static_url()``メソッドは相対パスを``/static/images/logo.png?v=aae54``というようなURIに変換します。``v``という引数は``logo.png``というファイルの中身に対するハッシュであり、この引数によりTornadoサーバはユーザのブラウザがコンテンツを区別してキャッシュするようにキャッシュヘッダを送信します。

.. ``v``引数はファイルの中身に基づいているため、もしファイルをアップデートしてサーバを再起動したら、Tornadoサーバは新しい値を持った``v``を送信します。これによって、ユーザのブラウザは自動的に新しいファイルを取得します。もしファイルの中身が変わっていなければ、ブラウザはサーバ上のファイルが更新されたか確認することなく、ローカルにキャッシュされたファイルのコピーを使用します。これによりレンダリングのパフォーマンスは劇的に改善されます。

.. アプリケーション公開時には`nginx <http://nginx.net/>`のような、より最適化されたファイルサーバから静的ファイルを配信したくなるでしょう。たいていのウェブサーバではこのようなキャッシュ動作をサポートしています。たとえばFriendFeedで行っているnginxの設定は下記のようになります。

.. code-block:: nginx
  location /static/ {
      root /var/friendfeed/static;
      if ($query_string) {
          expires max;
      }
  }
