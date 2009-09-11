.. Non-blocking, asynchronous requests

�Υ�֥�å���, ��Ʊ���ꥯ������
----------------------------------

.. When a request handler is executed, the request is automatically finished. Since Tornado uses a non-blocking I/O style, you can override this default behavior if you want a request to remain open after the main request handler method returns using the tornado.web.asynchronous decorator.

�ꥯ�����ȥϥ�ɥ餬�¹Ԥ��줿�Ȥ����ꥯ�����Ȥϼ�ưŪ�˽�λ���ޤ���Tornado�ϥΥ�֥�å���I/O�����������Ѥ��뤿�ᡢ�⤷�ᥤ��ꥯ�����ȥϥ�ɥ�᥽�åɤ��ͤ��֤������Ȥˡ��ꥯ�����Ȥ򳫤����ޤޤˤ���������``tornado.web.asynchronous``�ǥ��졼�����Ѥ��ƥǥե���Ȥο��񤤤򥪡��С��饤�ɤ��뤳�Ȥ��Ǥ��ޤ���

.. When you use this decorator, it is your responsibility to call self.finish() to finish the HTTP request, or the user's browser will simply hang:
���Υǥ��졼�����Ѥ�����ϡ�HTTP�ꥯ�����Ȥ�λ�������``self.finish()``�᥽�åɤ򤭤���ȸƤ�Ǥ����ʤ���Ф����ޤ��󡣤������ʤ��ȥ桼���Υ֥饦�����ϥ󥰤��Ƥ��ޤ��ޤ���

.. code-block:: python

  class MainHandler(tornado.web.RequestHandler):
      @tornado.web.asynchronous
      def get(self):
          self.write("Hello, world")
          self.finish()

.. Here is a real example that makes a call to the FriendFeed API using Tornado's built-in asynchronous HTTP client:
������Tornado�Υӥ�ȥ�����Ʊ��HTTP���饤����Ȥ��������FriendFeed API��ƤӽФ��ºݤ���򤴾Ҳ𤷤ޤ���

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

``get()``�᥽�åɤ��ͤ��ᤷ�Ƥ⡢�ꥯ�����ȤϽ���äƤ��ޤ��󡣤�����ޤ�HTTP���饤����Ȥ�``on_response()``�᥽�åɤ�ƤӽФ����ꥯ�����Ȥ��ޤ������Ƥ����Ȥ����쥹�ݥ󥹤Ϻǽ�Ū�ˤ�``self.finish()``��ƤӽФ����Ȥǥ��饤����Ȥ˥ե�å��夵��ޤ���

.. If you make calls to asynchronous library functions that require a callback (like the HTTP fetch function above), you should always wrap your callbacks with self.async_callback. This simple wrapper ensures that if your callback function raises an exception or has a programming error, a proper HTTP error response will be sent to the browser, and the connection will be properly closed.

�⤷������Хå����׵᤹��褦����Ʊ���饤�֥��ؿ���ƤӽФ����ϡ�������Хå���``self.async_callback``�Ǿ�˥�åפ��٤��Ǥ������㤨�о����Ǥ�HTTP�ե��å��ؿ��Τ褦�ʾ��Ǥ��ˤ��Υ���ץ�ʥ�åѡ����Ѥ��뤳�Ȥˤ�äơ�������Хå��ؿ����㳰��ȯ����������礢�뤤�ϥץ���ߥ󥰥��顼�����ä����ˡ�Ŭ�ڤ�HTTP���顼�쥹�ݥ󥹤��֥饦�����������졢���ͥ������Ŭ�ڤ��Ĥ����뤳�Ȥ��μ¤ˤʤ�ޤ���

.. For a more advanced asynchronous example, take a look at the chat example application, which implements an AJAX chat room using long polling.

����˾��Ū����򻲹ͤ��������ϡ���󥰥ݡ���󥰤��Ѥ���AJAX�Υ���åȥ롼������������򸫤Ƥ���������


