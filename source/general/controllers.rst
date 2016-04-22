############
コントローラ
############

コントローラはアプリケーションの心臓部です、
HTTP リクエストをどのように処理するかを決定します。

.. contents:: ページの目次

コントローラとは何ですか？
==========================

**コントローラは、 URI と関連づけることができるように命名されている
単純なクラスファイルです。**

つぎの URI を見てみましょう::

	example.com/index.php/blog/

上記の例では、 CodeIgniter は Blog.php という名前のコントローラを見つけだし、
それをロードしようとします。

**コントローラの名前が URI の最初のセグメントに一致した場合、
それがロードされます。**

やってみましょう: Hello World!
==============================

簡単なコントローラを作成してみて、その動きを見てみましょう。
テキストエディタを使って Blog.php というファイルを作成し、
その中に次のコードを配置します::

	<?php
	class Blog extends CI_Controller {

		public function index()
		{
			echo 'Hello World!';
		}
	}

次に、そのファイルを *application/controllers/* ディレクトリに保存します。

.. important:: このファイルは「 Blog.php 」、大文字の B で始まる必要があります。

さて、次のような URL でサイトを表示してください::

	example.com/index.php/blog/

正しく作業できたなら、次のように表示されます:

	Hello World!

.. important:: クラス名は大文字で始まる必要があります。

次のものは有効です::

	<?php
	class Blog extends CI_Controller {

	}
	
次のものは有効では **ありません** ::

	<?php
	class blog extends CI_Controller {

	}

また、コントローラは常に親コントローラクラスを継承し、
すべてのメソッドを受け継げるようにしてください。

メソッド
========

上記の例では、メソッド名は ``index()`` です。「 index 」メソッドは
URI の **第 2 セグメント** が空の場合、常にデフォルトでロードされます。
「 Hello World 」のメッセージを表示する別の方法は、次のようになります::

	example.com/index.php/blog/index/

**URI の第 2 セグメントはコントローラのどのメソッドを呼び出すかを
決定します。**

試してみましょう。コントローラに新しいメソッドを追加します::

	<?php
	class Blog extends CI_Controller {

		public function index()
		{
			echo 'Hello World!';
		}

		public function comments()
		{
			echo 'Look at this!';
		}
	}

さあ、次の URL で comment メソッドを表示してみましょう::

	example.com/index.php/blog/comments/

新しいメッセージが表示されるはずです。

メソッドに URI セグメントを渡す
===============================

URI に 2 つより多くのセグメントが含まれている場合、
それらはパラメータとしてメソッドに渡されます。

たとえば、次のような URI をがあるとしましょう::

	example.com/index.php/products/shoes/sandals/123

メソッドには URI セグメント 3 と 4 (「 sandals 」と「 123 」) が渡されます::

	<?php
	class Products extends CI_Controller {

		public function shoes($sandals, $id)
		{
			echo $sandals;
			echo $id;
		}
	}

.. important:: :doc:`URI ルーティング <routing>` 機能を使用している場合、
	メソッドに渡されるセグメントは
	再ルーティングされたものになります。

デフォルトコントローラの定義
============================

CodeIgniter は URI が提供されない場合にデフォルトのコントローラを
ロードするように指定することができます。ルート URL だけが要求されたような場合です。
デフォルトのコントローラを指定するには、 **application/config/routes.php** ファイルを開き、
次の変数を設定します::

	$route['default_controller'] = 'blog';

「 blog 」が書かれている箇所が使用したいコントローラクラスの名前を書くところです。
いま URI セグメントになにもを指定せずにメインの index.php ファイルをロードすれば、
デフォルトとして「 Hello World 」のメッセージが表示されます。

詳細については :doc:`URI ルーティング <routing>` ドキュメントの「予約済みルート」セクションを
参照してください。

メソッド呼び出しの再マッピング
==============================

上述のように、 URI の第 2 セグメントは通常、
コントローラのどのメソッドを呼び出すかを決定します。CodeIgniter では
``_remap()`` メソッドを使用してこの動作をオーバーライドすることができます::

	public function _remap()
	{
		// Some code here...
	}

.. important:: コントローラに _remap() という名前のメソッドが含まれている場合、
	URI がどうであるかに関係なく **常に** 呼び出されます。これは
	URI により呼び出されるメソッドを決定する通常の動作をオーバーライドし、
	独自のメソッドルーティングルールを定義することができます。

オーバーライドされるメソッド名 (典型的には URI の第 2 セグメント) が
``_remap()`` メソッドにパラメータとして渡されます::

	public function _remap($method)
	{
		if ($method === 'some_method')
		{
			$this->$method();
		}
		else
		{
			$this->default_method();
		}
	}

メソッド名の後の追加のセグメントは、オプションの 2 番目のパラメータとして ``_remap()`` に
渡されます。この配列は
PHP の `call_user_func_array() <http://php.net/call_user_func_array>`_ と組み合わせて使用することで、
CodeIgniterのデフォルト動作をエミュレートできます。

例::

	public function _remap($method, $params = array())
	{
		$method = 'process_'.$method;
		if (method_exists($this, $method))
		{
			return call_user_func_array(array($this, $method), $params);
		}
		show_404();
	}

出力処理
========

CodeIgniter には出力クラスがあり、Webブラウザにレンダリングされる
最終的なデータを送信する面倒を自動的にみてくれます。詳細な情報は
:doc:`ビュー <views>` と :doc:`出力クラス
<../libraries/output>` ページで見つけることができます。しかしながらいくつかのケースにおいて、
最終データをいくらか後処理をして自分自身でブラウザに送信したいこともあるでしょう。
CodeIgniter では ``_output()`` メソッドをコントローラに追加することで、
確定した出力データを受け取ることができます。

.. important:: コントローラに ``_output()`` という名前のメソッドが含まれている場合、
	最終データを直接出力するかわりとして、出力クラスから **常に** 呼び出されます。
	メソッドの最初のパラメータに
	最終出力が含まれます。

例を示します::

	public function _output($output)
	{
		echo $output;
	}

.. note::

	``_output()`` メソッドは、確定状態でデータを受信することに注意してください。
	``_output()`` メソッドに渡される前に、
	ベンチマークとメモリ使用量データはレンダリングされ、
	(キャッシュを有効にしている場合) キャッシュファイルは書き込まれており、
	(その :doc:`機能 <../libraries/output>` を使っていれば)
	ヘッダは送信されています。
	コントローラの出力を適切にキャッシュするため、
	``_output()`` メソッドで次のようにできます::

		if ($this->output->cache_expiration > 0)
		{
			$this->output->_write_cache($output);
		}

	この機能を使用している場合、ページの実行タイマおよび
	メモリ使用量の統計は完全な正確さを保てないでしょう、
	それらはあなたの追加処理を取得できないからです。
	最終処理のいずれかが行われる *前に* 出力を制御する
	別の方法については、
	:doc:`出力ライブラリ <../libraries/output>` で利用可能なメソッドを参照してください 。

プライベートメソッド
====================

いくつかのケースでは、特定のメソッドをパブリックアクセスから隠したいことがあります。
そうするためには、単に private または protected としてメソッドを宣言すれば、
URL リクエストとしては提供されません。たとえば、
次のようなメソッドがあるとします::

	private function _utility()
	{
		// some code
	}

つぎのように URL からアクセスしようとしても、動作しません::

	example.com/index.php/blog/_utility/

.. note:: メソッド名の前にアンダースコアを付けることによっても
	呼び出しを防ぐことができます。
	これは後方互換のために残されている古い機能です。

サブディレクトリにコントローラを整理する
========================================

大規模なアプリケーションを構築している場合、
階層的に整理したりサブディレクトリにコントローラを構築したいことでしょう。
CodeIgniter はできます。

単純にメインの *application/controllers/* の下にサブディレクトリを作成し、
その中にコントローラクラスを配置します。

.. note:: この機能を使用する場合、 URI の最初のセグメントには
	そのフォルダを指定する必要があります。たとえば、
	次のようにコントローラを持っているとしましょう::

		application/controllers/products/Shoes.php

	上記のコントローラを呼び出すには、 URI は次のようになります::

		example.com/index.php/products/shoes/show/123

それぞれのサブディレクトリには、
URL にサブディレクトリ *のみ* が含まれている場合に呼び出される
デフォルトのコントローラを持っていたいでしょう。シンプルに「 default_controller 」の設定を
*application/config/routes.php* ファイルで行い、名前が一致するようにコントローラを置いてください。

CodeIgniter はまた、 :doc:`URI
ルーティング <routing>` 機能により URI を再マッピングすることもできます。

クラスのコンストラクタ
======================

コントローラのいずれかでコンストラクタを使用する場合、
**必ず** その中に次のコード行を配置してください::

	parent::__construct();

この行が必要な理由は、
そのクラスのコンストラクタが親コントローラクラスのコンストラクタを上書きしてしまうので、
手動で呼び出す必要があります。

例::

	<?php
	class Blog extends CI_Controller {

		public function __construct()
		{
			parent::__construct();
			// Your own constructor code
		}
	}

コンストラクタはいくつかのデフォルト値を設定したりする必要があるときに便利です、
クラスがインスタンス化されるときにデフォルトで処理が走るからです。
コンストラクタは値を返すことはできませんが、いくつかのデフォルト作業を行うことができます。

予約済みのメソッド名
====================

あなたのコントローラクラスはメインのアプリケーションコントローラを継承しますので、
親クラスで使っているメソッド名と同一の名前をつけないように注意する必要があります。
さもなくばあなたの関数がそれらをオーバーライドしてしまいます。
:doc:`予約名 <reserved_names>`
の全リストを参照してください。

.. important:: また、そのクラス名と同じ名前のメソッドを作ってもいけません。
	そうしてしまうと、クラス内に ``__construct()`` メソッドがない場合、
	たとえば ``index:::index()`` メソッドが
	クラスのコンストラクタとして実行されます！　これは PHP4
	の後方互換機能です。

これでおしまいです！
====================

これが、つまり、コントローラについて知っておくべきすべてです。