.. Cookies and secure cookies

���å����Ȱ����ʥ��å���
------------------------------------

.. �桼���Υ֥饦���˥��å�����Ĥ���������set_cookie()�᥽�åɤ��Ѥ��ޤ�
   
.. code-block:: python
  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          if not self.get_cookie("mycookie"):
              self.set_cookie("mycookie", "myvalue")
              self.write("Your cookie was not set yet!")
          else:
              self.write("Your cookie was set!")


.. ���å����ϰ��դΤ��륯�饤����Ȥˤ�ä��ưפ˵�������Ƥ��ޤ��ޤ����㤨�и��ߥ����󤷤Ƥ���桼���Υ桼��ID����¸���뤿��˥��å����򥻥åȤ��������ϡ���¤���ɤ�����ˤ��ʤ��Υ��å������̾����ɬ�פ�����ޤ���Tornado�Ǥϥ��󥹥ȡ���ľ��Ǥ�set_secure_cookie()��get_secure_cookie()�᥽�åɤ��Ѥ��뤳�ȤǤ����¸��Ǥ��ޤ��������Υ᥽�åɤ��Ѥ���ˤϥ��ץꥱ���������ۤ���ݤ�cookie_secret�Ȥ�����̩������ꤹ��ɬ�פ�����ޤ�������ϥ��ץꥱ�������������ǥ�����ɰ����Ȥ��ƥ��ץꥱ���������Ϥ����Ȥ��Ǥ��ޤ���

.. code-block:: python

  application = tornado.web.Application([
      (r"/", MainHandler),
  ], cookie_secret="61oETzKXQAGaYdkL5gEmGeJJFuYh7EQnp2XdTP1o/Vo=")


.. ��̾�Ѥߥ��å����ˤϥ����ॹ����פ�HMAC��̾�˲ä��ƥ��å����Υ��󥳡��ɤ��줿�ͤ��ޤޤ�Ƥ��ޤ����⤷���å������Ť����뤤�Ͻ�̾��Ŭ�礷�ʤ���С�get_secure_cookie()�᥽�åɤ��������⥯�å��������åȤ���Ƥ��ʤ����Τ褦��None���֤��ޤ����嵭���������ʥ��å����Ȥ������ꤹ����ϰʲ��Τ褦�ʥ����ɤˤʤ�ޤ���

.. code-block:: python
  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          if not self.get_secure_cookie("mycookie"):
              self.set_secure_cookie("mycookie", "myvalue")
              self.write("Your cookie was not set yet!")
          else:
              self.write("Your cookie was set!")

