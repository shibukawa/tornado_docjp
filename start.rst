.. image:: tornado.png

.. Overview

概要
====

.. FriendFeed's web server is a relatively simple, non-blocking web 
   server written in Python. The FriendFeed application is written using 
   a web framework that looks a bit like web.py 
   or Google's webapp, but with additional tools and optimizations to take 
   advantage of the non-blocking web server and tools.

`FriendFeed <http://friendfeed.com/>`_ では、Pythonで書かれた、比較的シンプルで、ノンブロッキング実装のされたウェブサーバを使用しています。FriendFeedアプリケーションは、 `web.py <http://webpy.org/>`_ や、Googleの `webapp <http://code.google.com/appengine/docs/python/tools/webapp/>`_ に良く似たウェブのフレームワークを使用して書かれていますが、このノンブロッキングウェブサーバと周辺ツールを利用することによるアドバンテージを生かして、追加のツールや最適化が行われています。

.. Tornado is an open source version of this web server and some of the tools 
   we use most often at FriendFeed. The framework is distinct from most 
   mainstream web server frameworks (and certainly most Python frameworks) 
   because it is non-blocking and reasonably fast. 
   Because it is non-blocking and uses epoll, it can handle 1000s of 
   simultaneous standing connections, which means the framework is ideal 
   for real-time web services. We built the web server specifically to handle 
   FriendFeed's real-time features - every active user
   of FriendFeed maintains an open connection to the FriendFeed servers. 
   (For more information on scaling servers to support thousands of clients, 
   see The C10K problem.)

`Tornade <http://github.com/facebook/tornado>`_ はこのウェブサーバと、私たちがFriendFeedで頻繁に使用しているツールのうちのいくつかを含んだ、オープンソースバージョンです。このフレームワークはノンブロッキングで、適切な速さを備えているということで、主流となっている、ほとんどのウェブサーバフレームワーク(特に、ほとんどのPythonのフレームワーク)とは異なっています。速さの理由としては、ノンブロッキングで、なおかつ `epoll <http://www.kernel.org/doc/man-pages/online/pages/man4/epoll.4.html>`_ を利用しているということがあげられ、その結果として数千のコネクションを同時に扱うことができます。これは、このフレームワークがリアルタイムのウェブサービスにとって理想的なものであるということを示しています。私たちは、主に、FriendFeedのリアルタイムで提供される機能をサポートするためにウェブサーバを開発しました。FrinedFeedのアクティブユーザは全員、FriendFeedに対してオープンなコネクションを常に張っています。数千のクライアントをサポートするためのサーバーのスケーリングの情報については、 `C10K問題 <http://www.kegel.com/c10k.html>`_ を参照してください。

.. Here is the canonical "Hello, world" example app:

規律に従って、伝統の"Hello, world"のサンプルアプリケーションを紹介します:

.. code-block:: python

  import tornado.httpserver
  import tornado.ioloop
  import tornado.web

  class MainHandler(tornado.web.RequestHandler):
      def get(self):
          self.write("Hello, world")

  application = tornado.web.Application([
      (r"/", MainHandler),
  ])

  if __name__ == "__main__":
      http_server = tornado.httpserver.HTTPServer(application)
      http_server.listen(8888)
      tornado.ioloop.IOLoop.instance().start()

.. See Tornado walkthrough below for a detailed walkthrough of the 
   tornado.web package.

tornado.web パッケージの詳細のウォークスルーについては、後半の章に書いてあります。

.. We attempted to clean up the code base to reduce interdependencies 
   between modules, so you should (theoretically) be able to use any of 
   the modules independently in your project without using the whole package.

私たちは、モジュール間の相互依存性を減らそうと、コードベースをきれいにしようとしています。理論的には、パッケージ全体を使用するのではなく、必要なモジュールを個別に使用できるようにすべきです。

.. Download

ダウンロード
============

.. Download the most recent version of Tornado from GitHub:

Tornadoの一番最新のバージョンGitHubからダウンロードします:

  `tornado-0.1.tar.gz <http://www.tornadoweb.org/static/tornado-0.1.tar.gz>`_

.. You can also browse the source on GitHub. To install Tornado:

GitHub上で `ソースコードをブラウズ <http://github.com/facebook/tornado>`_ することもできます。Tornadoをインストールするには以下のようにします:

.. code-block:: bash

  tar xvzf tornado-0.1.tar.gz
  cd tornado-0.1
  python setup.py build
  sudo python setup.py install

.. After installation, you should be able to run any of the demos in the 
   demos directory included with the Tornado package.

インストールが終わったら、Tornadoのパッケージに含まれる :file:`demos` ディレクトリ内のデモをいくつか実行してみてください:

.. code-block: bash

  ./demos/helloworld/helloworld.py

.. Prerequisites

Tornadoのインストールに必要なもの
---------------------------------

.. Tornado has been tested on Python 2.5 and 2.6. To use all of the 
   features of Tornado, you need to have PycURL and a JSON library like 
   simplejson installed. Complete installation instructions for Mac OS X 
   and Ubuntu are included below for convenience.

TornadoはPython 2.5, 2.6でテストされています。Tornadoのすべての機能を使用するためには、 `PycURL <http://pycurl.sourceforge.net/>`_ と `simplejson <http://pypi.python.org/pypi/simplejson/>`_ などのJSONライブラリをインストールする必要があります。Mac OS XとUbuntuで必要なものを一括でインストールするには、以下のようにします。

Mac OS X 10.5/10.6
~~~~~~~~~~~~~~~~~~

.. code-block:: bash

  sudo easy_install setuptools pycurl==7.16.2.1 simplejson

Ubuntu Linux
~~~~~~~~~~~~

.. code-block:: bash

  sudo apt-get install python-dev python-pycurl python-simplejson
