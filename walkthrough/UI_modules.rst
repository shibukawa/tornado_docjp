.. UI modules

�桼�����󥿥ե������⥸�塼��
------------------------------

.. Tornado supports UI modules to make it easy to support standard, reusable UI widgets across your application. UI modules are like special functional calls to render components of your page, and they can come packaged with their own CSS and JavaScript.

Tornado�Ǥϥ��ץꥱ����������Τ�ɸ��Ū�Ǻ����Ѳ�ǽ�ʥ桼�����󥿥ե������������åȥ桼�����󥿥ե������⥸�塼����ñ�����Ѥ��䤹�����뤿��ˡ��桼�����󥿥ե������⥸�塼����󶡤��Ƥ��ޤ����桼�����󥿥ե������⥸�塼��ϥ����֥ڡ�����Υ���ݡ��ͥ�Ȥ������󥰤��뤿������̤ʴؿ��ƤӽФ��Τ褦�ʤ�Τǡ����줾���ȼ���CSS��JavaScript�ȤȤ���󶡤���ޤ���

.. For example, if you are implementing a blog, and you want to have blog entries appear on both the blog home page and on each blog entry page, you can make an Entry module to render them on both pages. First, create a Python module for your UI modules, e.g., uimodules.py:

���Ȥ��Ф��ʤ����֥���������Ƥ���Ȥ��ơ��֥�����ȥ꤬�֥��Υۡ���ڡ����Ȥ��줾��Υ���ȥ�ڡ�����ξ����ɽ�������褦�ˤ������Ȥ��ˡ�``Entry``�⥸�塼���ξ���Υڡ����������󥰤���褦�˼������뤳�Ȥ��Ǥ��ޤ����ޤ������ʤ��Υ桼�����󥿥ե������⥸�塼���``uimodules.py``�Τ褦��Python�⥸�塼���������ޤ���

.. code-block:: python

  class Entry(tornado.web.UIModule):
      def render(self, entry, show_comments=False):
          return self.render_string(
              "module-entry.html", show_comments=show_comments)

.. Tell Tornado to use uimodules.py using the ui_modules setting in your application:
Tornado�˥��ץꥱ��������``ui_modules``�Ȥ��������ȤäƤ���``uimodules.py``�����Ѥ���褦�������ޤ���

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
``home.html``�����HTML��ľ�ܵ��Ҥ���ΤǤϤʤ���``Entry``�⥸�塼��򻲾Ȥ��ޤ���

.. code-block:: django

  {% for entry in entries %}
    {{ modules.Entry(entry) }}
  {% end %}

.. Within entry.html, you reference the Entry module with the show_comments argument to show the expanded form of the entry:
``entry.html``����ǥ���ȥ�γ�ĥ���줿�ե����बɽ�������褦��``Entry``�⥸�塼���``show_comments``�����ȤȤ�˻��Ȥ��ޤ���


.. code-block:: django

  {{ modules.Entry(entry, show_comments=True) }}

.. Modules can include custom CSS and JavaScript functions by overriding the embedded_css, embedded_javascript, javascript_file, or css_file methods:
�⥸�塼��Ǥ�``embedded_css()``,``embedded_javascript()``,``javascript_file()``,``css_file()``�᥽�åɤ�ơ������Х饤�ɤ��뤳�Ȥˤ�ꥫ�������CSS��JavaScript������ळ�Ȥ��Ǥ��ޤ���

.. code-block:: python

  class Entry(tornado.web.UIModule):
      def embedded_css(self):
          return ".entry { margin-bottom: 1em; }"

      def render(self, entry, show_comments=False):
          return self.render_string(
              "module-entry.html", show_comments=show_comments)

.. Module CSS and JavaScript will be included once no matter how many times a module is used on a page. CSS is always included in the <head> of the page, and JavaScript is always included just before the </body> tag at the end of the page.
``CSS``�⥸�塼���``JavaSctipt``�⥸�塼���1�ڡ����˲����ɤ߹��ޤ줿�Ȥ��Ƥ�1�٤����ɤ߹��ޤ�ޤ���``CSS``�Ͼ�˥ڡ�����``<head>``������˴ޤޤ졢``JavaScript``�Ͼ�˥ڡ����κǸ��``</body>``������ľ���˴ޤޤ�ޤ���
