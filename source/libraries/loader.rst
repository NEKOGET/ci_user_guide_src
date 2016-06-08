############################
ローダ (読み込み処理) クラス
############################

ローダは、名前が示すように、要素をロードするために使用されます。
それら要素は :doc:`ビューファイル <../general/views>` 、
:doc:`ドライバー <../general/drivers>` 、
:doc:`ヘルパー <../general/helpers>` 、
:doc:`モデル <../general/models>` 、または独自のファイルをライブラリ (クラス) とすることができます。

.. note:: このクラスはシステムによって自動的に初期化されますので、
	手動で初期化する必要はありません。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

********************************
アプリケーションの「パッケージ」
********************************

アプリケーションパッケージはリソースの完全なセットを単一のディレクトリにいれて
簡単に配布することができます。それには独自のライブラリ、
モデル、ヘルパー、設定、および言語ファイルを揃えられます。これらのパッケージは
application/third_party ディレクトリに配置することをおすすめします。
後述はパッケージディレクトリのサンプルマップです。

以下は「 Foo Bar 」という名前のアプリケーションパッケージの
ディレクトリ例です。

::

	/application/third_party/foo_bar

	config/
	helpers/
	language/
	libraries/
	models/

「 FooBar 」アプリケーションパッケージの目的は何であれ、
それは独自の設定ファイル、ヘルパー、言語ファイル、ライブラリ、およびモデルを持っています。
コントローラ内でこれらのリソースを使用するには、
まずパッケージからリソースをロードするようローダに指示する必要があります。
それは ``add_package_path()`` メソッドによってパッケージパスを追加することで可能です。

パッケージのビューファイル
--------------------------

デフォルトでは ``add_package_path()`` が呼び出されたときに、パッケージのビューファイルのパスが設定されます。
ビューのパスは順番に見てまわり、一致するものが見つかればその時点で、
そのビューがロードされます。

この場合、パッケージ内で名前が衝突することがあり、
そしておそらく間違ったパッケージがロードされます。
これに対処するには ``add_package_path()`` を呼び出すとき、オプションの第 2 引数に FALSE
を設定します。

::

	$this->load->add_package_path(APPPATH.'my_app', FALSE);
	$this->load->view('my_app_index'); // 読み込まれます
	$this->load->view('welcome_message'); // デフォルトの welcome_message は読み込まれません。なぜなら add_package_path の第 2 引数が FALSE だからです

	// リセットします
	$this->load->remove_package_path(APPPATH.'my_app');

	// 第 2 引数なしでやり直します:
	$this->load->add_package_path(APPPATH.'my_app');
	$this->load->view('my_app_index'); // 読み込まれます
	$this->load->view('welcome_message'); // 読み込まれます

******************
クラスリファレンス
******************

.. php:class:: CI_Loader

	.. php:method:: library($library[, $params = NULL[, $object_name = NULL]])

		:param	mixed	$library: ライブラリ名の文字列または複数ライブラリ名の配列
		:param	array	$params: ロードされたライブラリのコンストラクタに渡す配列 (オプション)
		:param	string	$object_name: ライブラリを割り当てるオブジェクト名 (オプション)
		:returns:	CI_Loader インスタンス (メソッドチェイン)
		:rtype:	CI_Loader

		このメソッドはコアクラスをロードするために使用されます。

		.. note:: 私たちは「クラス」と「ライブラリ」という用語を互換性があるものとして使用します。

		たとえば CodeIgniter でメールを送信したい場合、
		最初のステップはコントローラ内で Email クラスをロードすることです::

			$this->load->library('email');

		いちどロードされれば、ライブラリを使用できる準備が整います。つまり ``$this->email`` を使用できます。

		ライブラリファイルは、メインの
		「 libraries 」ディレクトリ内のサブディレクトリか、またはあなたの個人的な *application/libraries*
		内に格納することができます。サブディレクトリにあるファイルをロードするには、
		単純に「 libraries 」ディレクトリからの相対パスを含めます。
		たとえば、次のファイルの場合::

			libraries/flavors/Chocolate.php

		こうやってロードできます::

			$this->load->library('flavors/chocolate');

		サブディレクトリは好きなだけネストすることができます。

		さらに、ライブラリの配列をロードメソッドに渡すことによって、
		いちどに複数のロードをすることができます。
		::

			$this->load->library(array('email', 'table'));

		**設定オプション**

		第 2 引数 (オプション) を使用すると、コンフィグ設定を任意で渡すことができます。
		通常、これらは配列として渡します::

			$config = array (
				'mailtype' => 'html',
				'charset'  => 'utf-8,
				'priority' => '1'
			);

			$this->load->library('email', $config);

		設定オプションは通常、 Config ファイルにより設定することもできます。
		各ライブラリはそれぞれのページで詳しく説明されています。
		使いたいものについてそれぞれの情報をお読みください。

		注意すべきこととして、第 1 引数に配列で複数のライブラリを渡されると、
		それぞれ同じパラメータ情報を受け取ることになります。

		**ライブラリに別のオブジェクト名を割り当てる**

		第 3 引数 (オプション) が渡されない場合、通常、
		ライブラリはそれと同じ名前のオブジェクトに割り当てられます。たとえば
		Calendar というライブラリの場合、それは
		``$this->calendar`` という名前の変数に代入されます。

		独自のクラス名を設定したい場合、
		第 3 引数にその値を渡すことができます::

			$this->load->library('calendar', NULL, 'my_calendar');

			// Calendar クラスはいま、このようにアクセスできます:
			$this->my_calendar

		注意すべきこととして、第 1 引数に配列で複数のライブラリを渡されると、
		この引数は無視されます。

	.. php:method:: driver($library[, $params = NULL[, $object_name]])

		:param	mixed	$library: ライブラリ名の文字列または複数ライブラリ名の配列
		:param	array	$params: ロードされたライブラリのコンストラクタに渡す配列 (オプション)
		:param	string	$object_name: ライブラリを割り当てるオブジェクト名 (オプション)
		:returns:	CI_Loader インスタンス (メソッドチェイン)
		:rtype:	CI_Loader

		このメソッドはドライバライブラリをロードするために使用され、
		``library()`` メソッドにとてもよく似た役割を果たします。

		例として、 CodeIgniter のセッションを使用したい場合、
		その最初の一歩はコントローラ内でセッションドライバをロードすることです::

			$this->load->driver('session');

		いちどロードされれば、ライブラリを使用できる準備が整います、つまり ``$this->session`` を使用できます。

		ドライバファイルは、メインの「 libraries 」ディレクトリ内のサブディレクトリか、
		またはあなたの個人的な *application/libraries* 内に格納することができます。
		サブディレクトリは親クラス名と一致させなければなりません。
		詳しくは :doc:`ドライバ<../general/drivers>` の説明をお読みください。

		さらに、ドライバライブラリの配列をロードメソッドに渡すことによって、
		いちどに複数のロードをすることができます。
		::

			$this->load->driver(array('session', 'cache'));

		**設定オプション**

		第 2 引数 (オプション) を使用すると、コンフィグ設定を任意で渡すことができます。
		通常、これらは配列として渡します::

			$config = array(
				'sess_driver' => 'cookie',
				'sess_encrypt_cookie'  => true,
				'encryption_key' => 'mysecretkey'
			);

			$this->load->driver('session', $config);

		設定オプションは通常、 Config ファイルにより設定することもできます。
		各ライブラリはそれぞれのページで詳しく説明されています。
		使いたいものについてそれぞれの情報をお読みください。

		**ライブラリに別のオブジェクト名を割り当てる**

		第 3 引数 (オプション) が渡されない場合、
		ライブラリは親クラスと同じ名前のオブジェクトに割り当てられます。
		たとえば Session というライブラリの場合、
		それは ``$this->session`` という名前の変数に代入されます。

		独自のクラス名を設定したい場合、
		第 3 引数にその値を渡すことができます::

			$this->load->library('session', '', 'my_session');

			// Session クラスはいまこのようにアクセスできます:
			$this->my_session

	.. php:method:: view($view[, $vars = array()[, return = FALSE]])

		:param	string	$view: ビュー名
		:param	array	$vars: 変数の連想配列
		:param	bool	$return: ロードされたビューを返すかどうか
		:returns:	$return を TRUE に設定した場合は表示内容の文字列、そうでなければ CI_Loader インスタンス (メソッドチェイン)
		:rtype:	mixed

		このメソッドはビューファイルをロードするために使用します。
		もしユーザガイドの :doc:`ビュー <../general/views>` セクションを読んでいない場合、
		まずそちらをおすすめします。
		この方法の一般的な使用方法が記されています。

		第 1 引数は必須です。
		それはロードしたいビューファイルの名前です。

		.. note:: .php の以外のものを使用しない限り、
			.php ファイル拡張子を指定する必要はありません。

		**省略可能な** 第 2 引数は連想配列またはオブジェクトを渡すことができます。
		それは実行中に PHP の
		`extract() <http://php.net/extract>`_ 関数を使ってビューファイルで使用できる変数に変換されます。
		繰り返します、 :doc:`ビューページ <../general/views>` をお読みください。
		これがどれだけ使えるかを学べます。

		**省略可能な** 第 3 引数はメソッドの動作を変更することができます、
		ブラウザに送信するのではなく、文字列としてデータを返すようにです。
		これは、なにかしらデータを処理したい場合に便利です。
		このパラメータを TRUE (真偽値) に設定すると、データが返されます。
		デフォルトの動作は FALSE で、これはブラウザにデータを送信します。
		データを返すようにしたときは変数に代入するのを忘れないでください::

			$string = $this->load->view('myfile', '', TRUE);

	.. php:method:: vars($vars[, $val = ''])

		:param	mixed	$vars: 変数の配列または単一の変数名
		:param	mixed	$val: 変数値 (オプション)
		:returns:	CI_Loader インスタンス（メソッドチェイン）
		:rtype:	CI_Loader

		このメソッドは入力として連想配列を取り、
		PHP の `extract() <http://php.net/extract>`_
		関数を使用して変数を生成します。このメソッドは上記
		``$this->load->view()`` メソッドの第 2 引数を使用した場合と同じ結果が得られます。
		独立してこのメソッドを使用するべき理由としては、
		コントローラのコンストラクタでグローバル変数を設定し、
		それらを不特定のメソッドからロードされる任意のビューファイルで利用したい場合です。
		このメソッドは複数回呼び出すことができます。
		データは蓄えられ、変数展開のために1つの配列にマージされます。

	.. php:method:: get_var($key)

		:param	string	$key: 変数名のキー
		:returns:	キーがある場合はその値、ない場合は NULL
		:rtype:	mixed

		このメソッドはビューで利用可能な変数からなる連想配列を確認します。
		ライブラリまたは別のコントローラメソッドで
		``$this->load->vars()`` を用いて変数を設定していることを何らかの理由で確認したい場合に便利です。

	.. php:method:: get_vars()

		:returns:	割り当てられたすべてのビュー変数の配列
		:rtype:	array

		このメソッドはビューで使用可能なすべての変数を取得します。

	.. php:method:: clear_vars()

		:returns:	CI_Loader インスタンス (メソッドチェイン)
		:rtype:	CI_Loader

		貯め込まれているビュー変数を消去します。

	.. php:method:: model($model[, $name = ''[, $db_conn = FALSE]])

		:param	mixed	$model: モデル名または複数のモデル名を含む配列
		:param	string	$name: モデルを割り当てるオブジェクト名 (オプション)
		:param	string	$db_conn: ロードするデータベース設定グループ (オプション)
		:returns:	CI_Loader インスタンス (メソッドチェイン)
		:rtype:	CI_Loader

		::

			$this->load->model('model_name');


		モデルがサブディレクトリに配置されている場合、
		models ディレクトリからの相対パスを含めます。たとえば
		*application/models/blog/Queries.php* にモデルがある場合、次のようにロードします::

			$this->load->model('blog/queries');

		モデルに別のオブジェクト名を割り当てたい場合は
		ロードメソッドの第 2 引数により指定することができます::

			$this->load->model('model_name', 'fubar');
			$this->fubar->method();

	.. php:method:: database([$params = ''[, $return = FALSE[, $query_builder = NULL]]])

		:param	mixed	$params: データベースグループ名または設定オプション
		:param	bool	$return: ロードされたデータベースオブジェクトを返すかどうか
		:param	bool	$query_builder: クエリビルダをロードするかどうか
		:returns:		$return が TRUE に設定されている場合は CI_DB インスタンスか失敗時に FALSE 、そうでなければ CI_Loader インスタンス (メソッドチェイン)
		:rtype:	mixed

		このメソッドではデータベースクラスをロードできます。
		2 つの引数は **省略可能** です。詳しくは :doc:`データベース <../database/index>`
		セクションをご覧ください。

	.. php:method:: dbforge([$db = NULL[, $return = FALSE]])

		:param	object	$db: データベースオブジェクト
		:param	bool	$return: データベースフォージのインスタンスを返すかどうか
		:returns:	$return が TRUE に設定されている場合は CI_DB_forge インスタンスか失敗時に FALSE 、そうでなければ CI_Loader インスタンス (メソッドチェイン)
		:rtype:	mixed

		:doc:`データベースフォージ <../database/forge>` クラスをロードします、
		より詳しくはそのマニュアルを参照してください。

	.. php:method:: dbutil([$db = NULL[, $return = FALSE]])

		:param	object	$db: データベースオブジェクト
		:param	bool	$return: データベースユーティリティのインスタンスを返すかどうか
		:returns:	$return が TRUE に設定されている場合は CI_DB_utility インスタンスか失敗時に FALSE 、そうでなければ CI_Loader インスタンス (メソッドチェイン)
		:rtype:	mixed

		:doc:`データベースユーティリティ <../database/utilities>` クラスをロードします。
		詳細はそのマニュアルを参照してください。

	.. php:method:: helper($helpers)

		:param	mixed	$helpers: ヘルパー名文字列、または複数のヘルパー名を含む配列
		:returns:	CI_Loader インスタンス (メソッドチェイン)
		:rtype:	CI_Loader

		このメソッドはヘルパーファイルをロードします。 file_name は ファイルの名前で、
		_helper.php 拡張子は不要です。

	.. php:method:: file($path[, $return = FALSE])

		:param	string	$path: ファイルパス
		:param	bool	$return: ロードされたファイルを返すかどうかを
		:returns:	$return が TRUE に設定されている場合は ファイルの内容、そうでなければ CI_Loader インスタンス (メソッドチェイン)
		:rtype:	mixed

		これは汎用的なファイルをロードするメソッドです。
		第 1 引数にファイルパスと名前を指定すれば、そのファイルが開かれ、読み込まれます。
		デフォルトではデータはちょうどビューファイルのようにお使いのブラウザに送信されますが、
		第 2 引数に TRUE を設定している場合はかわりにデータを文字列として
		返します。

	.. php:method:: language($files[, $lang = ''])

		:param	mixed	$files: 言語ファイル名または複数の言語ファイル名の配列
		:param	string	$lang: 言語名
		:returns:	CI_Loader インスタンス (メソッドチェイン)
		:rtype:	CI_Loader

		このメソッドは :doc:`言語読み込み
		メソッド <language>`: ``$this->lang->load()`` のエイリアスです。

	.. php:method:: config($file[, $use_sections = FALSE[, $fail_gracefully = FALSE]])

		:param	string	$file: 設定ファイル名
		:param	bool	$use_sections: 設定値を独自のセクションにロードするかどうか
		:param	bool	$fail_gracefully: 失敗した場合に FALSE を返すだけにするかどうか
		:returns:	成功した場合に TRUE 、失敗した場合に FALSE
		:rtype:	bool

		このメソッドは :doc:`設定読み込み
		メソッド <config>`: ``$this->config->load()`` のエイリアスです。

	.. php:method:: is_loaded($class)

		:param	string	$class: クラス名
		:returns:	見つかった場合は単一のプロパティ名、そうでない場合は FALSE
		:rtype:	mixed

		クラスがすでにロードされているかどうかを確認できます。

		.. note:: ここでの単語「クラス」は、ライブラリやドライバを指します。

		要求されたクラスがロードされている場合、このメソッドはそのクラスが割り当てられた
		CI スーパーオブジェクト内の名前を返します。そうでない場合は FALSE を返します::

			$this->load->library('form_validation');
			$this->load->is_loaded('Form_validation');	// 'form_validation' を返します

			$this->load->is_loaded('Nonexistent_library');	// FALSE を返します

		.. important:: (別のプロパティに割り当てられた) クラスのインスタンスを複数持っている場合、
			最初のものが返されます。

		::

			$this->load->library('form_validation', $config, 'fv');
			$this->load->library('form_validation');

			$this->load->is_loaded('Form_validation');	// 'fv' を返します

	.. php:method:: add_package_path($path[, $view_cascade = TRUE])

		:param	string	$path: 追加するパス
		:param	bool	$view_cascade: カスケードビューを使用するかどうか
		:returns:	CI_Loader インスタンス (メソッドチェイン)
		:rtype:	CI_Loader

		パッケージパスの追加により、後続のリソースへのリクエストで
		ローダクラスが与えられたパスを見るようになります。例として、
		上記の「Foo Bar」アプリケーションパッケージは
		Foo_bar.php という名前のライブラリを持っているとします。コントローラで、次のようにしてください::

			$this->load->add_package_path(APPPATH.'third_party/foo_bar/')
				->library('foo_bar');

	.. php:method:: remove_package_path([$path = ''])

		:param	string	$path: 削除するパス
		:returns:	CI_Loader インスタンス (メソッドチェイン)
		:rtype:	CI_Loader

		コントローラでアプリケーションパッケージのリソースの利用が完了し、
		さらに他のアプリケーションパッケージを使いたい場合、
		もう前のリソースのディレクトリ内を検索したくないので
		パッケージのパスを削除したくなる場合があることでしょう。
		最後に追加されたパスを削除するには、単にパラメータなしでメソッドを呼び出します。

		または特定のパッケージパスを削除するには、前にパッケージを
		``add_package_path()`` に指定したのとまったく同じパスで指定します::

			$this->load->remove_package_path(APPPATH.'third_party/foo_bar/');

	.. php:method:: get_package_paths([$include_base = TRUE])

		:param	bool	$include_base: BASEPATH を含めるかどうか
		:returns:	パッケージパスの配列
		:rtype:	array

		現在使用可能なすべてのパッケージのパスを返します。