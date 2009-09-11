.. Module index

モジュールインデックス
======================

.. The most important module is web, which is the web framework that 
   includes most of the meat of the Tornado package. The other modules 
   are tools that make web more useful. See Tornado walkthrough below 
   for a detailed walkthrough of the web package.

.. Main modules

メインモジュール
----------------

.. web - The web framework on which FriendFeed is built. web incorporates most of the important features of Tornado
   escape - XHTML, JSON, and URL encoding/decoding methods
   database - A simple wrapper around MySQLdb to make MySQL easier to use
   template - A Python-based web templating language
   httpclient - A non-blocking HTTP client designed to work with web and httpserver
   auth - Implementation of third party authentication and authorization schemes (Google OpenID/OAuth, Facebook Platform, Yahoo BBAuth, FriendFeed OpenID/OAuth, Twitter OAuth)
   locale - Localization/translation support
   options - Command line and config file parsing, optimized for server environments

`web <http://github.com/facebook/tornado/blob/master/tornado/web.py>`_
   FriendFeedを構築するのに使用されているウェブフレームワークです。
   :mod:`web` はTornadoのもっとも重要な機能を含んでいます。

`escape <http://github.com/facebook/tornado/blob/master/tornado/escape.py>`_
   XHTML, JSON, URLエンコーディング/デコーディングのためのメソッド群です。

`database <http://github.com/facebook/tornado/blob/master/tornado/database.py>`_
   MySQLを簡単に使用するための、 :mod:`MySQLdb` のシンプルなラッパーです。

`template <http://github.com/facebook/tornado/blob/master/tornado/template.py>`_
   Pythonで実装された、ウェブテンプレート言語です。

`httpclient <http://github.com/facebook/tornado/blob/master/tornado/httpclient>`_
   :mod:`web` や :mod:`httpserver` と連携して動くように設計されたノンブロッキングのHTTPクライアントです。

`auth <http://github.com/facebook/tornado/blob/master/tornado/>`_
   サードパーティの認証、承認の形式の実装です。 Google OpenID/OAuth, Facebook Platform, Yahoo BBAuth, FriendFeed OpenID/OAuth, Twitter OAuth をサポートしています。

`locale <http://github.com/facebook/tornado/blob/master/tornado/>`_
   多言語/翻訳のサポートです。

`options <http://github.com/facebook/tornado/blob/master/tornado/>`_
   コマンドラインおよびコンフィグファイルのパース、サーバ環境の最適化を行います。

.. Low-level modules

低レベルモジュール
------------------



.. httpserver - A very simple HTTP server built on which web is built
   iostream - A simple wrapper around non-blocking sockets to aide common reading and writing patterns
   ioloop - Core I/O loop

`httpwerver <http://github.com/facebook/tornado/blob/master/tornado/httpserver.py>`_
   :mod:`web` を構築するのに使用される、シンプルなHTTPサーバです。

`iostream <http://github.com/facebook/tornado/blob/master/tornado/iostream.py>`_
   良く使用する読み書きのパターンをサポートする、ノンブロッキングのソケットのシンプルなラッパーです。

`ioloop <http://github.com/facebook/tornado/blob/master/tornado/ioloop.py>`_
   コアとなる I/O のループです。

.. Random modules

その他のモジュール
------------------

.. s3server - A web server that implements most of the Amazon S3 interface, 
   backed by local file storage

`s3server <http://github.com/facebook/tornado/blob/master/tornado/s3server.py>`_
   ほとんどの `Amazon S3 <http://aws.amazon.com/s3/>`_ のインタフェースを実装したウェブサーバです。ローカルのファイルストレージに対して処理を行います。 (訳注：S3のモック?)

