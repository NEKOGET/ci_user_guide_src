######
モデル
######

モデルはより伝統的な MVC アプローチを使用したい人のために、 **必要に応じて**
ご利用いただけます。

.. contents:: ページの目次

モデルとは何ですか？
====================

モデルとは PHP クラスであり、データベースの情報とともに動作するように設計されるものです。
たとえば、 CodeIgniter でブログを管理するとしましょう。
モデルクラスはブログのデータを挿入、更新、
および取得するための関数を含むモデルクラスが必要でしょう。
これはこのようなモデルクラスの例です::

	class Blog_model extends CI_Model {

		public $title;
		public $content;
		public $date;

		public function __construct()
		{
			// CI_Model constructor の呼び出し
			parent::__construct();
		}

		public function get_last_ten_entries()
		{
			$query = $this->db->get('entries', 10);
			return $query->result();
		}

		public function insert_entry()
		{
			$this->title	= $_POST['title']; // 後述の注意書き参照
			$this->content	= $_POST['content'];
			$this->date	= time();

			$this->db->insert('entries', $this);
		}

		public function update_entry()
		{
			$this->title	= $_POST['title'];
			$this->content	= $_POST['content'];
			$this->date	= time();

			$this->db->update('entries', $this, array('id' => $_POST['id']));
		}

	}

.. note:: 上記の例では :doc:`クエリビルダ
	<../database/query_builder>` データベースメソッドを使っています。

.. note:: この例では簡潔に書くため、直接 ``$_POST`` を使用しています。
	これは一般には悪いプラクティスであり、より一般的なアプローチはとしては
	:doc:`入力ライブラリ <../libraries/input>` の
	``$this->input->post('title')`` を使用してください。

モデルの作りを調べる
====================

モデルクラスは **application/models/** ディレクトリに格納されています。
分類分けをしたいなら、サブディレクトリ内にネストすることが
できます。

モデルクラスの基本的なプロトタイプはこのようになります::

	class Model_name extends CI_Model {

		public function __construct()
		{
			parent::__construct();
		}

	}

**Model_name** の箇所はクラス名です。クラス名は頭文字は大文字で、残りは小文字である **必要があります**。
クラスは、
基本モデルクラスを継承してください。

ファイル名はクラス名と一致する必要があります。たとえば、このクラスの場合::

	class User_model extends CI_Model {

		public function __construct()
		{
			parent::__construct();
		}

	}

ファイルはこのようになります::

	application/models/User_model.php

モデルのロード
==============

モデルは一般的に
:doc:`コントローラ <controllers>` メソッドでロードされ、呼び出されます。
モデルをロードするにはつぎのメソッドを使います::

	$this->load->model('model_name');

モデルがサブディレクトリに配置されている場合、
models ディレクトリからの相対パスを含めます。たとえば、
*application/models/blog/Queries.php* にモデルがある場合、次のようにロードします::

	$this->load->model('blog/queries');

いちどロードされたら、クラスと同じ名前を持つオブジェクトを使用して、
モデルのメソッドにアクセスします::

	$this->load->model('model_name');

	$this->model_name->method();

モデルに別のオブジェクト名を割り当てたい場合は、
ロードメソッドの 第 2 引数により指定することができます::

	$this->load->model('model_name', 'foobar');

	$this->foobar->method();

つぎのコントローラの一例は、モデルをロードし、
ビューを提供しています::

	class Blog_controller extends CI_Controller {

		public function blog()
		{
			$this->load->model('blog');

			$data['query'] = $this->blog->get_last_ten_entries();

			$this->load->view('blog', $data);
		}
	}
	

モデルのオートローディング
==========================

アプリケーション全体でグローバルに特定のモデルが必要となった場合、
システムの初期化時に自動的にロードするように CodeIgniter
に設定することができます。
**application/config/autoload.php** ファイルを開き、
オートロード配列にモデルを追加するだけでおわります。

データベースへの接続
====================

モデルがロードされても、自動的にはデータベースに接続 **しません** 。
接続するための以下のオプションが利用できます:

-  標準的なデータベース方法を用いて接続することができます、 :doc:`ここで説明しているものです
   <../database/connecting>`、コントローラクラスとモデルクラスの
   どちらからでもできます。
-  第 3 引数に
   TRUE (真偽値) を渡すことでロード時に自動接続させることができます。
   接続設定はデータベースの設定ファイルで定義されているものを使用します::

	$this->load->model('model_name', '', TRUE);

-  第 3 引数に手動でデータベース接続設定を渡すことが
   できます::

	$config['hostname'] = 'localhost';
	$config['username'] = 'myusername';
	$config['password'] = 'mypassword';
	$config['database'] = 'mydatabase';
	$config['dbdriver'] = 'mysqli';
	$config['dbprefix'] = '';
	$config['pconnect'] = FALSE;
	$config['db_debug'] = TRUE;

	$this->load->model('model_name', '', $config);