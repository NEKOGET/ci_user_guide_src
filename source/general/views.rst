######
ビュー
######

ビューは簡単にウェブページ、またはヘッダー、フッター、
サイドバーなどのようなページフラグメントです。実際、ビューは柔軟にほかのビューの中に埋め込むことができます
(さらにそのビューもほかの中に、中に、中に……) そのような階層が必要なら、です。

ビューは直接呼び出されることはなく、 :doc:`コントローラ <controllers>` によってロードされる必要があります。
MVC フレームワークを使っていることを忘れないでください、
コントローラは交通整理役として振る舞うので、
特定のビューを取得する責任があるのです。
:doc:`コントローラ <controllers>` ページを読んでいない場合、
先にそちらを読んでください。

:doc:` <controllers>` コントローラページで作成したコントローラの例を使って、
ビューを追加してみましょう。

ビューの作成
============

テキストエディタを使って blogview.php というファイルを作り、
それに次の内容を書きます::

	<html>
	<head>
		<title>わたしのブログ</title>
	</head>
	<body>
		<h1>わたしのブログにようこそ！</h1>
	</body>
	</html>
	
このファイルは *application/views/* ディレクトリに保存します。

ビューの読み込み
================

特定のビューファイルをロードするには、次のメソッドを使用します::

	$this->load->view('name');

name の箇所は、ビューファイルの名前です。

.. note:: .php の以外のものを使用しない限り、
	.php ファイル拡張子を指定する必要はありません。

さて、前章で作ったコントローラ Blog.php を開き、
echo 文をビュー読み込みメソッドで置き換えます::

	<?php
	class Blog extends CI_Controller {

		public function index()
		{
			$this->load->view('blogview');
		}
	}

前章で使った URL を使用してサイトを開けば、新しいビューが表示されるはずです。
URL は次のようなものです::

	example.com/index.php/blog/

複数のビューを読み込む
======================

CodeIgniter はコントローラでの
``$this->load->view()`` の複数回呼び出しをうまく処理します。複数回の呼び出しがあった場合、
それらはまとめて追加されます。たとえば、
ヘッダビュー、メニュービュー、内容ビュー、およびフッタビューを使いたいとします。
次のようになります::

	<?php

	class Page extends CI_Controller {

		public function index()
		{
			$data['page_title'] = 'あなたのタイトル';
			$this->load->view('header');
			$this->load->view('menu');
			$this->load->view('content', $data);
			$this->load->view('footer');
		}

	}

上記の例では、後述の「動的データをビューに追加する」
を使用しています。

サブディレクトリにビューを保存する
==================================

お好みに応じて、ビューファイルはサブディレクトリ内に格納することができます。
その場合、ビューをロードする際にディレクトリ名を含める必要があります。
例::

	$this->load->view('directory_name/file_name');

動的データをビューに追加する
============================

データは、ビュー読み込みメソッドの第 2 引数に **配列** や
**オブジェクト** として、コントローラからビューに渡されます。
つぎのものは配列を使用した例です::

	$data = array(
		'title' => 'わたしのタイトル',
		'heading' => 'わたしのヘッダ',
		'message' => 'わたしのメッセージ'
	);

	$this->load->view('blogview', $data);

そして、つぎはオブジェクトを使用した例です::

	$data = new Someclass();
	$this->load->view('blogview', $data);

.. note:: オブジェクトを使用する場合、
	クラス変数は配列に型変換されます。

それでは、コントローラファイルで試してみましょう。
ファイルを開いてこのコードを追加します::

	<?php
	class Blog extends CI_Controller {

		public function index()
		{
			$data['title'] = "My Real Title";
			$data['heading'] = "My Real Heading";

			$this->load->view('blogview', $data);
		}
	}

さて、ビューファイルを開いて、
配列のキーに対応する変数にテキストを変更します::

	<html>
	<head>
		<title><?php echo $title;?></title>
	</head>
	<body>
		<h1><?php echo $heading;?></h1>
	</body>
	</html>

これで、いままでの URL のページを読み込めば、
置き換えた変数が表示されることでしょう。

繰り返し処理を作成する
======================

ビューファイルに渡すデータ配列は、単純な変数に限定されるものではありません。
複数行を繰り返し生成するために、多次元配列を渡すことができます。
たとえば、データベースからデータを取り出す場合、
たいてい多次元配列の形を取るでしょう。

つぎのものは簡単な例です。コントローラにこれを追加します::

	<?php
	class Blog extends CI_Controller {

		public function index()
		{
			$data['todo_list'] = array('掃除する', 'ママに電話', 'お使いに行く');

			$data['title'] = "わたしの本当のタイトル";
			$data['heading'] = "わたしの本当のヘッダ";

			$this->load->view('blogview', $data);
		}
	}

では、ビューを開き、ループを作成します::

	<html>
	<head>
		<title><?php echo $title;?></title>
	</head>
	<body>
		<h1><?php echo $heading;?></h1>
	
		<h3>わたしのTodoリスト</h3>

		<ul>
		<?php foreach ($todo_list as $item):?>
	
			<li><?php echo $item;?></li>
	
		<?php endforeach;?>
		</ul>

	</body>
	</html>

.. note:: 上記の例では、 PHP の別の構文を使用していることに気がついたでしょう。
	詳しくないのであれば、
	:doc:`これ <alternative_php>` を読むと良いです。

データとしてビューを返す
========================

**省略可能な** 第 3 引数はメソッドの動作を変更することができます、
ブラウザに送信するのではなく、文字列としてデータを返すようにです。
これは、なにがしかデータを処理したい場合に便利です。
このパラメータを TRUE (真偽値) に設定すると、データが返されます。
デフォルトの動作は false で、これはブラウザにデータを送信します。
データを返すようにしたときは変数に代入するのを忘れないでください::

	$string = $this->load->view('myfile', '', TRUE);