.. Static files and aggressive file caching


��Ū�ե�������Ѷ�Ū�ʥե����륭��å���
------------------------------------

.. Tornado����Ū�ե������'serve'����ˤϥ��ץꥱ������������``static_path``����ꤹ��ɬ�פ�����ޤ���

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


.. ��������Ǥ� ``/static/`` �ǻϤޤ뤹�٤ƤΥꥯ�����Ȥ�ưŪ����Ū�ʥǥ��쥯�ȥ꤫���'serve'�Ȥ��뤳�Ȥ��Ǥ��ޤ����㤨�� ``http://localhost:8888/static/foo.png <http://localhost:8888/static/foo.png>`` �Ȥ���URL�ξ��� ``foo.png`` �Ȥ����ե��������ꤵ�줿��Ū�ǥ��쥯�ȥ꤫�� 'serve' ���ޤ����ޤ� ``/robots.txt`` �� ``/favicon.ico`` ����Ū�ǥ��쥯�ȥ꤫�鼫ưŪ���ۿ�����ޤ����ʤ��Ȥ�URL��``/static``����Ϥޤ�ʤ��Ƥ��

.. �ѥե����ޥ󥹸���Τ���ˡ���Ū�꥽������֥饦�����Ѷ�Ū�˥���å��夹�뤳�Ȥϰ���Ū�����ФǤ�������ˤ�äƥ֥饦���ϥڡ����Υ�����󥰤�˸������ɬ�פ�``If-Modified-Since``��``Etag``�ꥯ�����Ȥ��������ʤ��ʤ�ޤ���

.. ���ε�ǽ�����Ѥ���ˤϡ��ƥ�ץ졼�Ȥ������Ū�ե������URL��HTML���ľ�ܵ��Ҥ���ΤǤϤʤ�``static_url()``�᥽�åɤ���Ѥ��ޤ���

.. code-block:: html
  <html>
     <head>
        <title>FriendFeed - {{ _("Home") }}</title>
     </head>
     <body>
        <div><img src="{{ static_url("images/logo.png") }}"/></div>
     </body>
  </html>


.. ``static_url()``�᥽�åɤ����Хѥ���``/static/images/logo.png?v=aae54``�Ȥ����褦��URI���Ѵ����ޤ���``v``�Ȥ���������``logo.png``�Ȥ����ե��������Ȥ��Ф���ϥå���Ǥ��ꡢ���ΰ����ˤ��Tornado�����Фϥ桼���Υ֥饦��������ƥ�Ĥ���̤��ƥ���å��夹��褦�˥���å���إå����������ޤ���

.. ``v``�����ϥե��������Ȥ˴�Ť��Ƥ��뤿�ᡢ�⤷�ե�����򥢥åץǡ��Ȥ��ƥ����Ф�Ƶ�ư�����顢Tornado�����ФϿ������ͤ���ä�``v``���������ޤ�������ˤ�äơ��桼���Υ֥饦���ϼ�ưŪ�˿������ե������������ޤ����⤷�ե��������Ȥ��Ѥ�äƤ��ʤ���С��֥饦���ϥ����о�Υե����뤬�������줿����ǧ���뤳�Ȥʤ���������˥���å��夵�줿�ե�����Υ��ԡ�����Ѥ��ޤ�������ˤ�������󥰤Υѥե����ޥ󥹤Ϸ�Ū�˲�������ޤ���

.. ���ץꥱ�������������ˤ�`nginx <http://nginx.net/>`�Τ褦�ʡ�����Ŭ�����줿�ե����륵���Ф�����Ū�ե�������ۿ��������ʤ�Ǥ��礦�������Ƥ��Υ����֥����ФǤϤ��Τ褦�ʥ���å���ư��򥵥ݡ��Ȥ��Ƥ��ޤ������Ȥ���FriendFeed�ǹԤäƤ���nginx������ϲ����Τ褦�ˤʤ�ޤ���

.. code-block:: nginx
  location /static/ {
      root /var/friendfeed/static;
      if ($query_string) {
          expires max;
      }
  }
