.. User authentication

�桼��ǧ��
------------------------------------

ǧ�ںѤߤΥ桼���ϡ��ꥯ�����ȥϥ�ɥ���Ǥ� ``self.current_user`` �Ȥ��ơ��ƥ�ץ졼����Ǥ� ``current_user`` �Ȥ��Ƥ��줾�����Ѥ��뤳�Ȥ��Ǥ��ޤ����ǥե���ȤǤ� ``current_user`` �� ``None`` �Ǥ���

���ץꥱ���������ǥ桼��ǧ�ڤ��������ˤϡ��㤨�Х��å������ͤ��Ȥ˥桼�������ꤹ��ˤϡ��ꥯ�����ȥϥ�ɥ����``get_current_user()``�᥽�åɤ򥪡��С��饤�ɤ���ɬ�פ�����ޤ�����������Ǥϡ��桼�������å�����˵�Ͽ����Ƥ���˥å��͡�����Ѥ��ƥ��ץꥱ�������˥����󤹤���ˡ�򼨤��Ƥ��ޤ���
   
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


`Python�ǥ��졼�� <http://www.python.org/dev/peps/pep-0318/>`��``tornado.web.authenticated``���Ѥ��ƥ�����Ѥߥ桼����ꥯ�����Ȥ��뤳�Ȥ��Ǥ��ޤ����⤷�ꥯ�����Ȥ����Υǥ��졼�����դ����᥽�åɤޤ�ã�����Ȥ��˥桼���������󤷤Ƥ��ʤ��ä��顢�ꥯ�����Ȥ�``login_url``�˥�����쥯�Ȥ���ޤ�����``login_url``�����ӥ��ץꥱ������������Ԥ��ޤ��˾嵭����ץ�ϰʲ��Τ褦�˽񤭴����뤳�Ȥ��Ǥ��ޤ���

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


�⤷``post()``�᥽�åɤ�``authenticated``�ǥ��졼���դ��Ǽ�������Ƥ��ơ��桼���������󤷤Ƥ��ʤ��ä���硢�����Ф�``403``�쥹�ݥ󥹤��֤��ޤ���

Tornado��Google OAuth�Τ褦�ʥ����ɥѡ��ƥ���ǧ��������ӥ�ȥ��󥵥ݡ��Ȥ��Ƥ��ޤ����ܺ٤�`auth module <http://github.com/facebook/tornado/blob/master/tornado/auth.py>`�򻲾Ȥ��Ʋ��������桼��ǧ�ڤ��Ѥ������ץꥱ������������ǧ����������Tornado�֥����������������ʤʤ�MySQL�˥桼���ǡ�������¸������⵭�ܤ���Ƥ��ޤ�����