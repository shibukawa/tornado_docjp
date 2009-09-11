クロスサイトリクエストフォージェリの防衛
----------------------------------------

.. Cross-site request forgery, or XSRF, is a common problem for personalized web applications. See the Wikipedia article for more information on how XSRF works.

クロスサイトリクエストフォージェリ（XSRF)は、ウェブアプリケーションにおける一般的な問題です。XSRFがどの様な悪さをするのかは、Wikipediaの当該ページを参照してください。

.. The generally accepted solution to prevent XSRF is to cookie every user with an unpredictable value and include that value as an additional argument with every form submission on your site. If the cookie and the value in the form submission do not match, then the request is likely forged.

XSRFに対する防護策としてたいていの場合は、ユーザ毎のクッキー 予測できない値　そして　含む　値を追加する　

.. Tornado comes with built-in XSRF protection. To include it in your site, include the application setting xsrf_cookies:

Tornadoは、XSRFプロテクション機能を持っています。アプリケーション設定内でxsrf_cookiesを有効にする事であなたのサイトでXSRFプロテクションを利用する事が出来ます。

.. code-block:: python

  settings = {
      "cookie_secret": "61oETzKXQAGaYdkL5gEmGeJJFuYh7EQnp2XdTP1o/Vo=",
      "login_url": "/login",
      "xsrf_cookies": True,
  }

  application = tornado.web.Application([
      (r"/", MainHandler),
      (r"/login", LoginHandler),
  ], ** settings)


.. If xsrf_cookies is set, the Tornado web application will set the _xsrf cookie for all users and reject all POST requests hat do not contain a correct _xsrf value. If you turn this setting on, you need to instrument all forms that submit via POST to contain this field. You can do this with the special function xsrf_form_html(), available in all templates: 

xsrf_cookiesが設定されている時、Tornadoウェブアプリケーションは、_xsrfクッキーをすべてのユーザにセットします。そして、正式な_xsrfクッキーを持たないすべてのPOSTリクエストを拒否します。もし、あなたがこの設定を有効にした場合、すべてのformのsubmit操作時に_xsrf値を付加する必要があります。xsrf_from_html()をテンプレート内のフォームに適用する事で、_xsrf値を付加する事が出来ます。

.. code-block:: html

  <form method="/login" method="post">
    {{ xsrf_form_html() }}
    <div>Username: <input type="text" name="username"/></div>
    <div>Password: <input type="password" name="password"/></div>
    <div><input type="submit" value="Sign in"/></div>
  </form>

.. If you submit AJAX POST requests, you will also need to instrument your JavaScript to include the _xsrf value with each request. This is the jQuery function we use at FriendFeed for AJAX POST requests that automatically adds the _xsrf value to all requests:

もし、あなたがAJAXのPOSTリクエストをした場合は、あなたはリクエスト毎に_xsrf値をJavascriptで埋め込む必要があります。FriendFeedで使用しているjQueryを利用した自動で_xsrf値を付加するサンプルを以下に示します。

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

