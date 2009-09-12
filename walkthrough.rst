.. Tornado walkthrough_

Tornadoウォークスルー
=====================

.. Request handlers and request arguments

.. index::
   pair: メソッド; tornado.web.RequestHandler.get_argument()
   pair: メソッド; tornado.web.RequestHandler.get()
   pair: メソッド; tornado.web.RequestHandler.post()
   pair: 設定; URLマッピング
   pair: 例外; tornado.web.HTTPError
   pair: 属性; tornado.web.RequestHandler.request
   pair: 属性; tornado.web.HTTPRequest.arguments
   pair: 属性; tornado.web.HTTPRequest.files
   pair: 属性; tornado.web.HTTPRequest.path
   pair: 属性; tornado.web.HTTPRequest.headers
   single: リクエスト
   pair: 実装; リクエストハンドラ

リクエストハンドラと、リクエスト引数
------------------------------------

.. A Tornado web application maps URLs or URL patterns to subclasses of 
   tornado.web.RequestHandler. Those classes define get() or post() methods 
   to handle HTTP GET or POST requests to that URL.

Tornadoウェブアプリケーションでは、URLあるいはURLパターンを :class:`tornado.web.RequestHandler` のサブクラスにマップします。これらのクラスではリクエストされたURLへのHTTP GET/POSTリクエストを処理する :meth:`get()` / :meth:`post()` メソッドを定義する必要があります。

.. This code maps the root URL / to MainHandler and the URL pattern 
   /story/([0-9]+) to StoryHandler. Regular expression groups are passed as 
   arguments to the RequestHandler methods:

下記のコードではルートURL ``/`` を :class:`MainHandler` に、URLパターン ``/story/([0-9]+)`` を :class:`StoryHandler` にマップしています。正規表現化された箇所は :meth:`RequestHandler` メソッドに引数として渡されます。

.. code-block:: python

  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          self.write("You requested the main page")

  class StoryHandler(tornado.web.RequestHandler):
      def get(self, story_id):
          self.write("You requested the story " + story_id)

  application = tornado.web.Application([
      (r"/", MainHandler),
      (r"/story/([0-9]+)", StoryHandler),
  ])

.. You can get query string arguments and parse POST bodies with the 
   get_argument() method:

:meth:`get_argument()` メソッドによってクエリ文字列引数の受け取りとPOSTの本体のパースを行うことができます。

.. code-block:: python

  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          self.write('<html><body><form action="/" method="post">'
                     '<input type="text" name="message">'
                     '<input type="submit" value="Submit">'
                     '</form></body></html>')

      def post(self):
          self.set_header("Content-Type", "text/plain")
          self.write("You wrote " + self.get_argument("message"))

.. If you want to send an error response to the client, e.g., 
   403 Unauthorized, you can just raise a tornado.web.HTTPError exception:


:exc:`tornado.web.HTTPError` 例外を投げると、クライアントに403 Unauthorizedのようなエラーレスポンスを返すことができます。

.. code-block:: python

  if not self.user_is_logged_in():
    raise tornado.web.HTTPError(403)

.. The request handler can access the object representing the current 
   request with self.request. The HTTPRequest object includes a number 
   of useful attribute, including:

リクエストハンドラは現在のリクエストを表すオブジェクトの :attr:`self.request` でアクセス可能です。 :class:`HTTPRequest` オブジェクトは多くの便利な属性があります。

.. * arguments - all of the GET and POST arguments
.. * files - all of the uploaded files (via multipart/form-data POST requests)
.. * path - the request path (everything before the ?)
.. * headers - the request headers

* :data:`arguments` - すべてのGETとPOSTの引数
* :data:`files` - すべてのアップロードされたファイル（multipart/form-data POSTリクエスト経由）
* :data:`path` - リクエストパス（?以前すべて）
* :data:`headers` - リクエストヘッダ

.. See the class definition for HTTPRequest in httpserver for a complete 
   list of attributes.

:mod:`httpserver` 内にあるHTTPRequestのクラス定義を参照すると、すべての属性を見ることができます。

.. Templates

.. index::
   pair: テンプレート関数; escape
   pair: テンプレート関数; url_escape
   pair: テンプレート関数; json_encode
   single: テンプレート関数; 自作
   single: テンプレート
   pair: モジュール; tornado.template

テンプレート
------------

.. You can use any template language supported by Python, but Tornado ships 
   with its own templating language that is a lot faster and more flexible 
   than many of the most popular templating systems out there. See the 
   template module documentation for complete documentation.

Pythonがサポートしているあらゆるテンプレート言語を用いることができますが、Tornadoでは他の有名なテンプレートシステムと比較して、格段に速くより柔軟な独自のテンプレート言語を提供しています。完全なドキュメントは `templateモジュール <http://github.com/facebook/tornado/blob/master/tornado/template.py>`_ のドキュメントを参照してください。

.. A Tornado template is just HTML (or any other text-based format) with 
   Python control sequences and expressions embedded within the markup:

TornadoテンプレートはPython制御構造と表現がマークアップによって組み込まれた単なるHTML（あるいは他のテキストベースフォーマット）です。

.. code-block:: html

  <html>
    <head>
      <title>{{ title }}</title>
    </head>
    <body>
      <ul>
        {% for item in items %}
          <li>{{ escape(item) }}</li>
        {% end %}
      </ul>
    </body>
  </html>

.. If you saved this template as "template.html" and put it in the same 
   directory as your Python file, you could render this template with:

このテンプレートを :file:`template.html` としてPythonファイルと同じディレクトリに保存した場合、以下のコードでレンダリングできます。

.. code-block:: python

  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          items = ["Item 1", "Item 2", "Item 3"]
          self.render("template.html", title="My title", items=items)

.. Tornado templates support control statements and expressions. Control 
   statements are surronded by {% and %}, e.g., {% if len(items) > 2 %}. 
   Expressions are surrounded by {{ and }}, e.g., {{ items[0] }}.

Tornadoテンプレートは制御構造と表現をサポートします。制御構造は ``{%`` と ``%}`` で囲むことによって表されます。たとえば ``{% if len(item) > 2 %}`` のような形です。表現は ``{{`` と ``}}`` で囲むことによって表現します。たとえば ``{{ items[0] }}`` といった具合です。

.. Control statements more or less map exactly to Python statements. 
   We support if, for, while, and try, all of which are terminated with 
   {% end %}. We also support template inheritance using the extends and 
   block statements, which are described in detail in the documentation 
   for the template module.

制御構造はほぼPythonの制御構造の表現と対応しています。 ``if, for, while, try`` がサポートされていて、終了は{% end %}で宣言します。また、 ``extends`` や ``block`` 宣言によりテンプレートの継承も可能です。詳しくは `templateモジュール <http://github.com/facebook/tornado/blob/master/tornado/template.py>`_ のドキュメントを参照してください。

.. Expressions can be any Python expression, including function calls. 
   We support the functions escape, url_escape, and json_encode by default, 
   and you can pass other functions into the template simply by passing them 
   as keyword arguments to the template render function:

表現はどのような関数呼び出しを含む、あらゆるPython表現が可能です。Tornadoではデフォルトで ``escape``, ``url_escape``, ``json_encode`` をサポートしており、さらに他の関数もテンプレートレンダリング関数にキーワード引数として渡すことで、テンプレート上で使用可能となります。

.. code-block:: python

  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          self.render("template.html", add=self.add)

      def add(self, x, y):
          return x + y

.. When you are building a real application, you are going to want to use 
   all of the features of Tornado templates, especially template inheritance. 
   Read all about those features in the template module section.

実アプリケーションを構築する際にはテンプレート継承といったTornadoテンプレートのすべての機能を利用したくなることでしょう。詳しくは `templateモジュール <http://github.com/facebook/tornado/blob/master/tornado/template.py>`_ の章に記載してあります。

.. Under the hood, Tornado templates are translated directly to Python. 
   The expressions you include in your template are copied verbatim into a 
   Python function representing your template. We don't try to prevent 
   anything in the template language; we created it explicitly to provide 
   the flexibility that other, stricter templating systems prevent. 
   Consequently, if you write random stuff inside of your template 
   expressions, you will get random Python errors when you execute the template.

Tornadoのテンプレートエンジンによって、Tornadoテンプレートは直接Pythonに変換されます。テンプレートに書かれた表現は逐一Python関数としてコピーされます。 Tornadoのテンプレート言語は他のテンプレート言語とは異なりテンプレート上であらゆる表現が可能で、明確な意味で柔軟性を実現します。 逆にテンプレート上で書いた表現があらゆるPythonのエラーを引き起こす可能性があることに注意してください。

.. Cookies and secure cookies

.. index::
   pair: 実装; クッキー
   pair: メソッド; tornado.web.RequestHandler.set_cookie()
   pair: メソッド; tornado.web.RequestHandler.get_secure_cookie()

クッキーと、安全なクッキー
--------------------------

.. You can set cookies in the user's browser with the set_cookie method:

ユーザのブラウザにクッキーを残したい場合は :meth:`set_cookie()` メソッドを用います:

.. code-block:: python

  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          if not self.get_cookie("mycookie"):
              self.set_cookie("mycookie", "myvalue")
              self.write("Your cookie was not set yet!")
          else:
              self.write("Your cookie was set!")

.. Cookies are easily forged by malicious clients. If you need to set 
   cookies to, e.g., save the user ID of the currently logged in user, 
   you need to sign your cookies to prevent forgery. Tornado supports 
   this out of the box with the set_secure_cookie and get_secure_cookie 
   methods. To use these methods, you need to specify a secret key named 
   cookie_secret when you create your application. You can pass in 
   application settings as keyword arguments to your application:

クッキーは悪意のあるクライアントによって容易に偽装されてしまいます。例えば現在ログインしているユーザのユーザIDを保存するためにクッキーをセットしたい場合は、偽造を防ぐためにあなたのクッキーを署名する必要があります。Tornadoではインストール直後でも\ :meth:`set_secure_cookie()`\ と\ :meth:`get_secure_cookie()`\ メソッドを用いることでこれを実現できます。これらのメソッドを用いるにはアプリケーションを構築する際に\ :data:`cookie_secret`\ という秘密鍵を指定する必要があります。これはアプリケーション設定内でキーワード引数としてアプリケーションに渡すことができます。

.. code-block:: python

  application = tornado.web.Application([
      (r"/", MainHandler),
  ], cookie_secret="61oETzKXQAGaYdkL5gEmGeJJFuYh7EQnp2XdTP1o/Vo=")

.. Signed cookies contain the encoded value of the cookie in addition to a 
   timestamp and an HMAC signature. If the cookie is old or if the signature 
   doesn't match, get_secure_cookie will return None just as if the cookie 
   isn't set. The secure version of the example above:

署名済みクッキーにはタイムスタンプと\ `HMAC署名 <http://en.wikipedia.org/wiki/HMAC>`_\ (\ `日本語 <http://ja.wikipedia.org/wiki/HMAC>`_\ )に加えてクッキーのエンコードされた値が含まれています。もしクッキーが古いあるいは署名が適合しなければ、\ :meth:`get_secure_cookie()`\ メソッドがあたかもクッキーがセットされていないかのように ``None`` を返します。上記の例を安全なクッキーとして設定する場合は以下のようなコードになります。

.. code-block:: python

  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          if not self.get_secure_cookie("mycookie"):
              self.set_secure_cookie("mycookie", "myvalue")
              self.write("Your cookie was not set yet!")
          else:
              self.write("Your cookie was set!")

.. User authentication

.. index::
   pair: 実装; ユーザ認証
   pair: デコレータ; tornado.web.authenticated
   pair: 属性; tornado.web.RequestHandler.current_user
   pair: テンプレート変数; current_user
   pair: 設定; login_url

ユーザ認証
----------

.. The currently authenticated user is available in every request handler as 
   self.current_user, and in every template as current_user. By default, 
   current_user is None.

認証済みのユーザは、リクエストハンドラ内では  :attr:`self.current_user` として、テンプレート内では :data:`current_user` としてそれぞれ利用することができます。デフォルトでは :attr:`current_user` は ``None`` です。

.. To implement user authentication in your application, you need to override 
   the get_current_user() method in your request handlers to determine the 
   current user based on, e.g., the value of a cookie. Here is an example 
   that lets users log into the application simply by specifying a nickname, 
   which is then saved in a cookie:

アプリケーション内でユーザ認証を実装するには、例えばクッキーの値をもとにユーザを断定するには、リクエストハンドラ内で :meth:`get_current_user()` メソッドをオーバーライドする必要があります。下記の例では、ユーザがクッキー内に記録されているニックネームを用いてアプリケーションにログインする方法を示してします。

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

.. You can require that the user be logged in using the Python decorator tornado.web.authenticated. If a request goes to a method with this decorator, and the user is not logged in, they will be redirected to login_url (another application setting). The example above could be rewritten:

`Pythonデコレータ <http://www.python.org/dev/peps/pep-0318/>`_\ の\ :func:`tornado.web.authenticated`\ を用いてログイン済みユーザのリクエストのみを処理するコードを書くことができます。もしリクエストがこのデコレータが付いたメソッドまで達したときにユーザがログインしていなかったら、リクエストは[ ``login_url``\ にリダイレクトされます。（\ ``login_url``\ は別途アプリケーション設定を行います）上記サンプルは以下のように書き換えることができます:

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

.. If you decorate post() methods with the authenticated decorator, and the 
   user is not logged in, the server will send a 403 response.

もし\ :meth:`post()`\ メソッドが\ :func:`authenticated`\ デコレータ付きで実装されていて、ユーザがログインしていなかった場合、サーバは\ ``403``\ レスポンスを返します。


.. Tornado comes with built-in support for third-party authentication 
   schemes like Google OAuth. See the auth module for more details. 
   Check out the Tornado Blog example application for a complete 
   example that uses authentication (and stores user data in a MySQL database).

TornadoはGoogle OAuthのようなサードパーティの認証方式もビルトインサポートしています。詳細は\ `auth module <http://github.com/facebook/tornado/blob/master/tornado/auth.py>`_\ を参照して下さい。ユーザ認証を用いたアプリケーションの例を確認したい場合はTornadoブログをご覧ください。（なおMySQLにユーザデータを保存する例も記載されています。）

.. index::
   single: クロスサイトリクエストフォージェリ
   single: XSRF
   pair: クッキー; セキュリティ
   pair: 設定; xsrf_cookies
   pair: テンプレート関数; xsrf_from_html()
   pair: 実装; XSRFからの保護

.. Cross-site request forgery protection

クロスサイトリクエストフォージェリからの保護
--------------------------------------------

.. Cross-site request forgery, or XSRF, is a common problem for personalized 
   web applications. See the Wikipedia article for more information on how 
   XSRF works.

`クロスサイトリクエストフォージェリ（XSRF) <http://en.wikipedia.org/wiki/Cross-site_request_forgery>`_ (`日本語 <http://ja.wikipedia.org/wiki/クロスサイトリクエストフォージェリ>`_)は、ウェブアプリケーションにおける一般的な問題です。XSRFがどの様な悪さをするのかは、Wikipediaの当該ページを参照してください。

.. The generally accepted solution to prevent XSRF is to cookie every user 
   with an unpredictable value and include that value as an additional 
   argument with every form submission on your site. If the cookie and the 
   value in the form submission do not match, then the request is likely forged.

一般的なXSRFに対する防衛策としては、ユーザ毎に予測できない値をクッキーとして格納し、ウェブサイトへのフォームの送信ごとにその値を追加の引数として入れるということが行われます。もしクッキーの値と、送信されたフォームの値が異なったら、そのリクエストはニセ者であるとみなします。

.. Tornado comes with built-in XSRF protection. To include it in your site, 
   include the application setting xsrf_cookies:

Tornadoは、XSRFプロテクション機能を持っています。アプリケーション設定内で :data:`xsrf_cookies` を有効にする事であなたのサイトでXSRFプロテクションを利用する事ができます:

.. code-block:: python

  settings = {
      "cookie_secret": "61oETzKXQAGaYdkL5gEmGeJJFuYh7EQnp2XdTP1o/Vo=",
      "login_url": "/login",
      "xsrf_cookies": True,
  }

  application = tornado.web.Application([
      (r"/", MainHandler),
      (r"/login", LoginHandler),
  ], **settings)

.. If xsrf_cookies is set, the Tornado web application will set the _xsrf 
   cookie for all users and reject all POST requests hat do not contain a 
   correct _xsrf value. If you turn this setting on, you need to instrument 
   all forms that submit via POST to contain this field. You can do this with 
   the special function xsrf_form_html(), available in all templates:

:data:`xsrf_cookies` が設定されていると、Tornadoウェブアプリケーションは、 :data:`_xsrf` クッキーをすべてのユーザにセットします。 そして、正式な :data:`_xsrf` クッキーを持たないすべてのPOSTリクエストを拒否します。 もし、この設定を有効にした場合には、すべてのformのsubmit操作時に :data:`_xsrf` 値を付加する必要があります。 :func:`xsrf_from_html()` をテンプレート内のフォームに適用する事で、 :data:`_xsrf` 値を付加する事ができます:

.. code-block:: html

  <form method="/login" method="post">
    {{ xsrf_form_html() }}
    <div>Username: <input type="text" name="username"/></div>
    <div>Password: <input type="password" name="password"/></div>
    <div><input type="submit" value="Sign in"/></div>
  </form>

.. If you submit AJAX POST requests, you will also need to instrument your 
   JavaScript to include the _xsrf value with each request. This is the 
   jQuery function we use at FriendFeed for AJAX POST requests that 
   automatically adds the _xsrf value to all requests:

もし、AJAXのPOSTリクエストを行う場合には、リクエスト毎に :data:`_xsrf` 値をJavascriptで埋め込む必要があります。 FriendFeedで使用している `jQuery <http://jquery.com/>`_ を利用して自動で_xsrf値を付加するサンプルを以下に示します:

.. code-block:: javascript

  function getCookie(name) {
      var r = document.cookie.match("¥¥b" + name + "=([^;]*)¥¥b");
      return r ? r[1] : undefined;
  }

  jQuery.postJSON = function(url, args, callback) {
      args._xsrf = getCookie("_xsrf");
      $.ajax({url: url, data: $.param(args), dataType: "text", type: "POST",
          success: function(response) {
          callback(eval("(" + response + ")"));
      }});
  };

.. Static files and aggressive file caching

.. index:: 
   single: 静的ファイル
   single: テンプレート関数; static_url()
   single: 設定; static_path
   single: nginx; 静的ファイル

静的ファイルと積極的なファイルキャッシュ
---------------------------------------------

.. You can serve static files from Tornado by specifying the static_path 
   setting in your application:

Tornadoで静的ファイルを提供するにはアプリケーション設定で :data:`static_path` を指定する必要があります:

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

.. This setting will automatically make all requests that start with /static/ serve from that static directory, e.g., http://localhost:8888/static/foo.png will serve the file foo.png from the specified static directory. We also automatically serve /robots.txt and /favicon.ico from the static directory (even though they don't start with the /static/ prefix).

この設定では ``/static/`` で始まるすべてのリクエストを自動的に静的なディレクトリからの'serve'とすることができます。例えば :file:`http://localhost:8888/static/foo.png` というURLの場合は :file:`foo.png` というファイルを指定された静的ディレクトリから提供します。また :file:`/robots.txt` や :file:`/favicon.ico` も静的ディレクトリから自動的に配信されます。（たとえURLが :file:`/static` から始まらなくても）

.. To improve performance, it is generally a good idea for browsers to cache static resources aggressively so browsers won't send unnecessary If-Modified-Since or Etag requests that might block the rendering of the page. Tornado supports this out of the box with static content versioning.

パフォーマンス向上の定石として、静的リソースをブラウザが積極的にキャッシュするという一般的な方法があります。これによってブラウザはページのレンダリングを妨げる不必要な ``If-Modified-Since`` や ``Etag`` リクエストを送信しなくなります。

.. To use this feature, use the static_url() method in your templates rather than typing the URL of the static file directly in your HTML:

この機能を利用するには、テンプレートの中で静的ファイルのURLをHTML内で直接記述するのではなく :meth:`static_url()` メソッドを使用します。

.. code-block:: html

  <html>
     <head>
        <title>FriendFeed - {{ _("Home") }}</title>
     </head>
     <body>
       <div><img src="{{ static_url("images/logo.png") }}"/></div>
     </body>
   </html>

.. The static_url() function will translate that relative path to a URI that looks like /static/images/logo.png?v=aae54. The v argument is a hash of the content in logo.png, and its presence makes the Tornado server send cache headers to the user's browser that will make the browser cache the content indefinitely.

:meth:`static_url()` メソッドは相対パスを ``/static/images/logo.png?v=aae54`` というようなURIに変換します。 :data:`v` という引数は :file:`logo.png` というファイルの中身に対するハッシュであり、この引数によりTornadoサーバはユーザのブラウザがコンテンツを区別してキャッシュするようにキャッシュヘッダを送信します。

.. Since the v argument is based on the content of the file, if you update a file and restart your server, it will start sending a new v value, so the user's browser will automatically fetch the new file. If the file's contents don't change, the browser will continue to use a locally cached copy without ever checking for updates on the server, significantly improving rendering performance.

:data:`v` 引数はファイルの中身に基づいているため、もしファイルをアップデートしてサーバを再起動したら、Tornadoサーバは新しい値を持った ``v`` を送信します。これによって、ユーザのブラウザは自動的に新しいファイルを取得します。もしファイルの中身が変わっていなければ、ブラウザはサーバ上のファイルが更新されたか確認することなく、ローカルにキャッシュされたファイルのコピーを使用します。これによりレンダリングのパフォーマンスは劇的に改善されます。

.. In production, you probably want to serve static files from a more optimized static file server like nginx. You can configure most any web server to support these caching semantics. Here is the nginx configuration we use at FriendFeed:

アプリケーション公開時には `nginx <http://nginx.net/>`_ のような、より最適化されたファイルサーバから静的ファイルを配信したくなるでしょう。たいていのウェブサーバではこのようなキャッシュ動作をサポートしています。たとえばFriendFeedで行っているnginxの設定は下記のようになります:

.. code-block:: text

  location /static/ {
      root /var/friendfeed/static;
      if ($query_string) {
          expires max;
      }
   }

.. Localization

.. index::
   pair: リクエストハンドラ; locale属性
   pair: テンプレート変数; locale
   pair: 関数; _()
   pair: 関数; tornado.locale.get_supported_locales()
   pair: モジュール; tornado.locale
   single: 多言語化
   single: 翻訳
   single: 実装; 翻訳

多言語化
--------

.. The locale of the current user (whether they are logged in or not) is always available as self.locale in the request handler and as locale in templates.

ユーザのロケールは、ユーザがログインしているかどうかに関わらず、リクエストハンドラの :attr:`self.locale` やテンプレートの :data:`locale` で取得できます。

..  The name of the locale (e.g., en_US) is available as locale.name, and you can translate strings with the locale.translate method. 

ロケールの名前(en_USなど)は :attr:`locale.name` で取得できます。また、 :meth:`locale.translate` メソッドを使用することで翻訳を行うことができます。

.. Templates also have the global function call _() available for string translation. The translate function has two forms:

テンプレート内ではグローバル関数 :func:`_()` を翻訳に使うことができます。この関数は2通りの使い方があります:

.. code-block:: python

  _("Translate this string")

.. which translates the string directly based on the current locale, and

この呼び方では文字列を現在のロケールに基づいて翻訳します。

.. code-block:: python

  _("A person liked this", "%(num)d people liked this", len(people)) % {"num": len(people)}

.. which translates a string that can be singular or plural based on the value of the third argument. 

この呼びかたでは、単数と複数で異なった形を取る文字列を第三引数の値に基づいて翻訳することができます。

.. In the example above, a translation of the first string will be returned if len(people) is 1, or a translation of the second string will be returned otherwise.

上記の例では ``len(people)`` が1の時には最初の文字列が返され、それ以外の場合には二番目の文字列が返されます。

.. The most common pattern for translations is to use Python named placeholders for variables (the %(num)d in the example above) since placeholders can move around on translation.

翻訳文で変数を使う場合はPythonの名前付きプレースホルダー(上記の例では ``%(num)d``)を使うのが一般的です。これはプレースホルダーを翻訳文の好きな位置に置けるようにするためです。

.. Here is a properly localized template:

適切に多言語化されたテンプレートの例を下に示します:

.. code-block:: html

  <html>
     <head>
        <title>FriendFeed - {{ _("Sign in") }}</title>
     </head>
     <body>
       <form action="{{ request.path }}" method="post">
         <div>{{ _("Username") }} <input type="text" name="username"/></div>
         <div>{{ _("Password") }} <input type="password" name="password"/></div>
         <div><input type="submit" value="{{ _("Sign in") }}"/></div>
         {{ xsrf_form_html() }}
       </form>
     </body>
   </html>

.. By default, we detect the user's locale using the Accept-Language header sent by the user's browser. We choose en_US if we can't find an appropriate Accept-Language value. If you let user's set their locale as a preference, you can override this default locale selection by overriding get_user_locale in your request handler:

デフォルトでは、ユーザのブラウザが送る ``Accept-Language`` ヘッダの値をユーザのロケールを判断します。適切な値の ``Accept-Language`` ヘッダが見つからない場合は ``en_US`` を使います。ユーザにロケールを設定させる場合は、リクエストハンドラの :meth:`get_user_locale` メソッドをオーバーライドすることでこの挙動を上書きすることができます。

.. code-block:: python

  class BaseHandler(tornado.web.RequestHandler):
      def get_current_user(self):
          user_id = self.get_secure_cookie("user")
          if not user_id: return None
          return self.backend.get_user_by_id(user_id)

      def get_user_locale(self):
          if "locale" not in self.current_user.prefs:
              # Use the Accept-Language header
              return None
          return self.current_user.prefs["locale"]

.. If get_user_locale returns None, we fall back on the Accept-Language header.

:meth:`get_user_locale` メソッドの返り値が ``None`` の場合には、 ``Accept-Language`` ヘッダの値に基づいてロケールを決定します。

.. You can load all the translations for your application using the tornado.locale.load_translations method. It takes in the name of the directory which should contain CSV files named after the locales whose translations they contain, e.g., es_GT.csv or fr_CA.csv. The method loads all the translations from those CSV files and infers the list of supported locales based on the presence of each CSV file. You typically call this method once in the main() method of your server:

:meth:`tornado.locale.load_translations` メソッドで、すべての翻訳ファイルをロードすることができます。このメソッドは翻訳ファイルが入っているディレクトリ名を引数に取ります。翻訳ファイルはロケールの名前に基づいた名前(例: :file:`es_GT.csv`, :file:`fr_CA.csv`)のCSVファイルです。このメソッドはCVSファイルから翻訳文をロードし、各CVSファイルの有無を元にどのロケールがサポートされているかを決定します。通常はこのメソッドは :meth:`main()` メソッドの中で一度だけ呼びます。

.. code-block:: python

  def main():
      tornado.locale.load_translations(
          os.path.join(os.path.dirname(__file__), "translations"))
      start_server()

.. You can get the list of supported locales in your application with tornado.locale.get_supported_locales(). The user's locale is chosen to be the closest match based on the supported locales. For example, if the user's locale is es_GT, and the es locale is supported, self.locale will be es for that request. We fall back on en_US if no close match can be found.

サポートされているロケールの一覧は :func:`tornado.locale.get_supported_locales()` で取得できます。ユーザのロケールは、サポートされているロケールの中で最も近いものが選ばれます。例えばユーザのロケールが ``es_GT`` で、 ``es`` ロケールがサポートされている場合、そのリクエストの :attr:`self.locale` は ``es`` になります。近い名前が見つからない場合は ``en_US`` になります。

.. See the locale module documentation for detailed information on the CSV format and other localization methods.

CSVファイルのフォーマットや他の他言語化の方法についての詳細は `localeモジュール <http://github.com/facebook/tornado/blob/master/tornado/locale.py>`_ のドキュメントを参照してください。

.. UI modules

.. index::
   single: ユーザインタフェース
   pair: 設定; ui_modules
   pair: クラス; tornado.web.UIModule

ユーザインタフェースモジュール
------------------------------

.. Tornado supports UI modules to make it easy to support standard, reusable UI widgets across your application. UI modules are like special functional calls to render components of your page, and they can come packaged with their own CSS and JavaScript.

Tornadoではアプリケーション全体で標準的で再利用可能なユーザインタフェースモジュールを簡単に利用しやすくするために、ユーザインタフェースモジュールを提供しています。ユーザインタフェースモジュールはウェブページ内のコンポーネントをレンダリングするための特別な関数呼び出しのようなもので、それぞれ独自のCSSとJavaScriptとともに提供されます。

.. For example, if you are implementing a blog, and you want to have blog entries appear on both the blog home page and on each blog entry page, you can make an Entry module to render them on both pages. First, create a Python module for your UI modules, e.g., uimodules.py:

たとえばあなたがブログを実装しているとして、ブログエントリがブログのホームページとそれぞれのエントリページの両方に表示されるようにしたいときに、\ :class:`Entry`\ モジュールを両方のページをレンダリングするように実装することができます。まず、あなたのユーザインタフェースモジュールに :file:`uimodules.py` のようなPythonモジュールを作成します:

.. code-block:: python

  class Entry(tornado.web.UIModule):
      def render(self, entry, show_comments=False):
          return self.render_string(
              "module-entry.html", show_comments=show_comments)

.. Tell Tornado to use uimodules.py using the ui_modules setting in your application:

Tornadoにアプリケーションで\ ``ui_modules``\ という設定を使用して\ ``uimodules.py``\ を利用するように設定します:

.. code-block:: python

  class HomeHandler(tornado.web.RequestHandler):
      def get(self):
          entries = self.db.query("SELECT * FROM entries ORDER BY date DESC")
          self.render("home.html", entries=entries)

  class EntryHandler(tornado.web.RequestHandler):
      def get(self, entry_id):
          entry = self.db.get("SELECT * FROM entries WHERE id = %s", entry_id)
          if not entry: raise tornado.web.HTTPError(404)
          self.render("entry.html", entry=entry)

  settings = {
      "ui_modules": uimodules,
  }

  application = tornado.web.Application([
      (r"/", HomeHandler),
      (r"/entry/([0-9]+)", EntryHandler),
  ], **settings)

.. Within home.html, you reference the Entry module rather than printing the HTML directly:

:file:`home.html`\ の中でHTMLを直接記述するのではなく、\ :class:`Entry`\ モジュールを参照します。


.. code-block:: django

  {% for entry in entries %}
    {{ modules.Entry(entry) }}
  {% end %}

.. Within entry.html, you reference the Entry module with the show_comments argument to show the expanded form of the entry:

:file:`entry.html`\ の中でエントリの拡張されたフォームが表示されるように\ :class:`Entry`\ モジュールを\ ``show_comments``\ 引数とともに参照します。

.. code-block:: django

  {{ modules.Entry(entry, show_comments=True) }}

.. Modules can include custom CSS and JavaScript functions by overriding the embedded_css, embedded_javascript, javascript_file, or css_file methods:

モジュールでは :meth:`embedded_css()`, :meth:`embedded_javascript()`, :meth:`javascript_file()`, :meth:`css_file()` メソッドを各々オーバライドすることによりカスタムのCSSとJavaScriptを取り込むことができます:

.. code-block:: python

  class Entry(tornado.web.UIModule):
      def embedded_css(self):
          return ".entry { margin-bottom: 1em; }"

      def render(self, entry, show_comments=False):
          return self.render_string(
              "module-entry.html", show_comments=show_comments)

.. Module CSS and JavaScript will be included once no matter how many times a module is used on a page. CSS is always included in the <head> of the page, and JavaScript is always included just before the </body> tag at the end of the page.

 ``CSS``モジュール と ``JavaSctipt`` モジュールは1ページに何回読み込まれたとしても1度だけ読み込まれます。 ``CSS`` は常にページの ``<head>`` タグ内に含まれ、 ``JavaScript`` は常にページの最後の ``</body>`` タグの直前に含まれます。

.. Non-blocking, asynchronous requests

.. index::
   pair: デコレータ; tornado.web.asynchronous
   pair: メソッド; tornado.web.RequestHandler.async_callback
   pair: ノンブロッキング; 実装
   single: 非同期リクエスト

ノンブロッキング, 非同期リクエスト
----------------------------------

.. When a request handler is executed, the request is automatically finished. Since Tornado uses a non-blocking I/O style, you can override this default behavior if you want a request to remain open after the main request handler method returns using the tornado.web.asynchronous decorator.

リクエストハンドラが実行されたとき、リクエストは自動的に終了します。TornadoはノンブロッキングI/Oスタイルを使用するため、もしメインリクエストハンドラメソッドが値を返したあとに、リクエストを開けたままにしたい場合は :func:`tornado.web.asynchronous` デコレータを用いてデフォルトの振舞いをオーバーライドすることができます。

.. When you use this decorator, it is your responsibility to call self.finish() to finish the HTTP request, or the user's browser will simply hang:

このデコレータを用いる場合は、HTTPリクエストを終了する場合は ``self.finish()`` メソッドをきちんと呼んであげなければいけません。そうしないとユーザのブラウザがハングしてしまいます:

.. code-block:: python

  class MainHandler(tornado.web.RequestHandler):
      @tornado.web.asynchronous
      def get(self):
          self.write("Hello, world")
          self.finish()

.. Here is a real example that makes a call to the FriendFeed API using Tornado's built-in asynchronous HTTP client:

ここでTornadoのビルトイン非同期HTTPクライアントを用いてFriendFeed APIを呼び出す実際の例をご紹介します:

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

:meth:`get()` メソッドが値を戻しても、リクエストは終わっていません。いずれまたHTTPクライアントが :meth:`on_response()` メソッドを呼び出し、リクエストがまだ開いていたとき、レスポンスは最終的には :meth:`self.finish()` を呼び出すことでクライアントにフラッシュされます。

.. If you make calls to asynchronous library functions that require a callback (like the HTTP fetch function above), you should always wrap your callbacks with self.async_callback. This simple wrapper ensures that if your callback function raises an exception or has a programming error, a proper HTTP error response will be sent to the browser, and the connection will be properly closed.

もしコールバックを要求するような非同期ライブラリ関数を呼び出す場合は、コールバックを ``self.async_callback`` で常にラップすべきです。（例えば上の例でのHTTPフェッチ関数のような場合です）このシンプルなラッパーを用いることによって、コールバック関数が例外を発生させた場合あるいはプログラミングエラーがあった場合に、適切なHTTPエラーレスポンスがブラウザに送信され、コネクションが適切に閉じられることが確実になります。

.. For a more advanced asynchronous example, take a look at the chat example application, which implements an AJAX chat room using long polling.

さらに上級的な例を参考したい場合は、ロングポーリングを用いてAJAXのチャットルームを実装した例を見てください。

.. Third party authentication

.. index::
   pair: 認証; サードパーティ
   pair: モジュール; tornado.auth

サードパーティ認証
------------------

.. Tornado's auth module implements the authentication and authorization 
   protocols for a number of the most popular sites on the web, including 
   Google/Gmail, Facebook, Twitter, Yahoo, and FriendFeed. The module 
   includes methods to log users in via these sites and, where applicable, 
   methods to authorize access to the service so you can, e.g., download 
   a user's address book or publish a Twitter message on their behalf.

Tornadoの認証モジュールは、いくつかのメジャーなWebサービスの認証と承認に対応しています。サービスは、Google/Gmail、Facebook、Twitter、Yahoo、FriendFeedが利用出来ます。このモジュールを使う事で、これらのサイトに、認証済みのアクセスを出来ます。例えばあなたのアドレスブックに載っている友達のTwitterのメッセージをダウンロードすることができます。

.. Here is an example handler that uses Google for authentication, 
   saving the Google credentials in a cookie for later access:

参考にグーグルの認証を使用したサンプルを紹介します。 このサンプルは、継続的なアクセスを行うために、グーグルの認証済みクッキーを保存します:

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
          # set_secure_cookie() などを使用してユーザを保存します。

.. See the auth module documentation for more details.

更に詳しい情報は、認証モジュール（auth module)のドキュメントを参照してください。