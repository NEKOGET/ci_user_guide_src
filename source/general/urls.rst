##################
CodeIgniter の URL
##################

デフォルトで、 CodeIgniter の URL は検索エンジンと人間にわかりやすく設計されています。
URL には、動的システムの代名詞である標準的な「クエリ文字列」アプローチではなく、
CodeIgniter は
**セグメントベース** のアプローチを使用しています::

	example.com/news/article/my_article

.. note:: クエリ文字列のURLは必要に応じて有効にすることができます。
	後述します。

URI セグメント
==============

URL セグメントは Model-View-Controller アプローチに従い、
通常次のような特徴を持ちます::

	example.com/class/function/ID

#. 最初のセグメントが呼び出されるコントローラ **クラス**
   を表します。
#. 第 2 のセグメントが呼び出されるクラスの **関数** 、
   またはメソッドを表します。
#. 第 3 のセグメントは任意に追加可能で、
   コントローラに渡されるIDおよび何らかの変数を表します。

:doc:`URIライブラリ <../libraries/uri>`  と :doc:`URLヘルパー
<../helpers/url_helper>` は、 URI データを操作することを簡単にする
機能が含まれています。また、
:doc:`URI ルーティング <routing>` 機能により柔軟に URL を再マッピングすることができます。

index.phpファイルを外す
=======================

デフォルトでは **index.php** ファイルは URL に含まれます::

	example.com/index.php/news/article/my_article

Apache サーバで *mod_rewrite* を有効にしている場合、
.htaccessファイルでいくつかの簡単なルールを使用することでこのファイルを簡単に外すことができます。
つぎのものはそのようなファイルの例です。「ネガティブ」メソッドを利用して
特定のアイテム以外はすべてリダイレクトされるようにしています:

::
	
	RewriteEngine On
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteRule ^(.*)$ index.php/$1 [L]

上記の例では、存在するディレクトリやファイル以外のあらゆるHTTPリクエストは
index.php ファイルの要求として扱われます。

.. note:: これらの具体的なルールはすべてのサーバー構成で動くわけではありません。

.. note:: また、上記のルールは外からアクセスできるようにする必要があるあらゆる
	assets が対象外となっていることを確認してください。

URL サフィックスを追加する
==========================

**config/config.php** ファイルでは CodeIgniter で生成されるすべての
URL に追加されるサフィックスを指定することができます。たとえば、
URL はつぎのものであれば::

	example.com/index.php/products/view/shoes

必要に応じて **.html** のように、ページを特定のタイプのように見える
サフィックスを追加することができます::

	example.com/index.php/products/view/shoes.html

クエリ文字列を有効にする
========================

いくつかのケースでは、クエリ文字列のURLを使用するほうがふさわしいかもしれません::

	index.php?c=products&m=view&id=345

CodeIgniter ではこの機能をサポートしており、必要に応じて
**application/config.php** ファイルで有効にすることができます。設定ファイルを開くと、
次の項目が見つかるでしょう::

	$config['enable_query_strings'] = FALSE;
	$config['controller_trigger'] = 'c';
	$config['function_trigger'] = 'm';

「enable_query_strings」を TRUE に変更した場合、この機能が有効になります。
コントローラおよび機能は、コントローラとメソッドを呼び出すために設定した
「トリガー」の文字を使用してアクセスできるようになります::

	index.php?c=controller&m=method

.. note:: クエリ文字列を使用している場合、セグメントベースの
	URL で動作するように設計されている URL ヘルパーを利用するのではなく、
	独自の URL を構築する必要があります (フォームヘルパーのような、
	URL を生成する他のヘルパーもです) 。