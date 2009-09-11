.. UI modules

ユーザインタフェースモジュール
------------------------------

.. Tornado supports UI modules to make it easy to support standard, reusable UI widgets across your application. UI modules are like special functional calls to render components of your page, and they can come packaged with their own CSS and JavaScript.

Tornadoではアプリケーション全体で標準的で再利用可能なユーザインタフェースウィジットユーザインタフェースモジュールを簡単に利用しやすくするために、ユーザインタフェースモジュールを提供しています。ユーザインタフェースモジュールはウェブページ内のコンポーネントをレンダリングするための特別な関数呼び出しのようなもので、それぞれ独自のCSSとJavaScriptとともに提供されます。

.. For example, if you are implementing a blog, and you want to have blog entries appear on both the blog home page and on each blog entry page, you can make an Entry module to render them on both pages. First, create a Python module for your UI modules, e.g., uimodules.py:

たとえばあなたがブログを実装しているとして、ブログエントリがブログのホームページとそれぞれのエントリページの両方に表示されるようにしたいときに、``Entry``モジュールを両方のページをレンダリングするように実装することができます。まず、あなたのユーザインタフェースモジュールに``uimodules.py``のようなPythonモジュールを作成します。

.. code-block:: python

  class Entry(tornado.web.UIModule):
      def render(self, entry, show_comments=False):
          return self.render_string(
              "module-entry.html", show_comments=show_comments)

.. Tell Tornado to use uimodules.py using the ui_modules setting in your application:
Tornadoにアプリケーションで``ui_modules``という設定を使っている``uimodules.py``を利用するよう明記します。

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
``home.html``の中でHTMLを直接記述するのではなく、``Entry``モジュールを参照します。

.. code-block:: django

  {% for entry in entries %}
    {{ modules.Entry(entry) }}
  {% end %}

.. Within entry.html, you reference the Entry module with the show_comments argument to show the expanded form of the entry:
``entry.html``の中でエントリの拡張されたフォームが表示されるように``Entry``モジュールを``show_comments``引数とともに参照します。


.. code-block:: django

  {{ modules.Entry(entry, show_comments=True) }}

.. Modules can include custom CSS and JavaScript functions by overriding the embedded_css, embedded_javascript, javascript_file, or css_file methods:
モジュールでは``embedded_css()``,``embedded_javascript()``,``javascript_file()``,``css_file()``メソッドを各々オーバライドすることによりカスタムのCSSとJavaScriptを取り込むことができます。

.. code-block:: python

  class Entry(tornado.web.UIModule):
      def embedded_css(self):
          return ".entry { margin-bottom: 1em; }"

      def render(self, entry, show_comments=False):
          return self.render_string(
              "module-entry.html", show_comments=show_comments)

.. Module CSS and JavaScript will be included once no matter how many times a module is used on a page. CSS is always included in the <head> of the page, and JavaScript is always included just before the </body> tag at the end of the page.
``CSS``モジュールと``JavaSctipt``モジュールは1ページに何回読み込まれたとしても1度だけ読み込まれます。``CSS``は常にページの``<head>``タグ内に含まれ、``JavaScript``は常にページの最後の``</body>``タグの直前に含まれます。
