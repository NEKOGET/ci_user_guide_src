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

		:param	string	$key: Variable name key
		:returns:	Value if key is found, NULL if not
		:rtype:	mixed

		This method checks the associative array of variables available to
		your views. This is useful if for any reason a var is set in a library
		or another controller method using ``$this->load->vars()``.

	.. php:method:: get_vars()

		:returns:	An array of all assigned view variables
		:rtype:	array

		This method retrieves all variables available to your views.

	.. php:method:: clear_vars()

		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		Clears cached view variables.

	.. php:method:: model($model[, $name = ''[, $db_conn = FALSE]])

		:param	mixed	$model: Model name or an array containing multiple models
		:param	string	$name: Optional object name to assign the model to
		:param	string	$db_conn: Optional database configuration group to load
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		::

			$this->load->model('model_name');


		If your model is located in a subdirectory, include the relative path
		from your models directory. For example, if you have a model located at
		*application/models/blog/Queries.php* you'll load it using::

			$this->load->model('blog/queries');

		If you would like your model assigned to a different object name you can
		specify it via the second parameter of the loading method::

			$this->load->model('model_name', 'fubar');
			$this->fubar->method();

	.. php:method:: database([$params = ''[, $return = FALSE[, $query_builder = NULL]]])

		:param	mixed	$params: Database group name or configuration options
		:param	bool	$return: Whether to return the loaded database object
		:param	bool	$query_builder: Whether to load the Query Builder
		:returns:	Loaded CI_DB instance or FALSE on failure if $return is set to TRUE, otherwise CI_Loader instance (method chaining)
		:rtype:	mixed

		This method lets you load the database class. The two parameters are
		**optional**. Please see the :doc:`database <../database/index>`
		section for more info.

	.. php:method:: dbforge([$db = NULL[, $return = FALSE]])

		:param	object	$db: Database object
		:param	bool	$return: Whether to return the Database Forge instance
		:returns:	Loaded CI_DB_forge instance if $return is set to TRUE, otherwise CI_Loader instance (method chaining)
		:rtype:	mixed

		Loads the :doc:`Database Forge <../database/forge>` class, please refer
		to that manual for more info.

	.. php:method:: dbutil([$db = NULL[, $return = FALSE]])

		:param	object	$db: Database object
		:param	bool	$return: Whether to return the Database Utilities instance
		:returns:	Loaded CI_DB_utility instance if $return is set to TRUE, otherwise CI_Loader instance (method chaining)
		:rtype:	mixed

		Loads the :doc:`Database Utilities <../database/utilities>` class, please
		refer to that manual for more info.

	.. php:method:: helper($helpers)

		:param	mixed	$helpers: Helper name as a string or an array containing multiple helpers
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		This method loads helper files, where file_name is the name of the
		file, without the _helper.php extension.

	.. php:method:: file($path[, $return = FALSE])

		:param	string	$path: File path
		:param	bool	$return: Whether to return the loaded file
		:returns:	File contents if $return is set to TRUE, otherwise CI_Loader instance (method chaining)
		:rtype:	mixed

		This is a generic file loading method. Supply the filepath and name in
		the first parameter and it will open and read the file. By default the
		data is sent to your browser, just like a View file, but if you set the
		second parameter to boolean TRUE it will instead return the data as a
		string.

	.. php:method:: language($files[, $lang = ''])

		:param	mixed	$files: Language file name or an array of multiple language files
		:param	string	$lang: Language name
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		This method is an alias of the :doc:`language loading
		method <language>`: ``$this->lang->load()``.

	.. php:method:: config($file[, $use_sections = FALSE[, $fail_gracefully = FALSE]])

		:param	string	$file: Configuration file name
		:param	bool	$use_sections: Whether configuration values should be loaded into their own section
		:param	bool	$fail_gracefully: Whether to just return FALSE in case of failure
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		This method is an alias of the :doc:`config file loading
		method <config>`: ``$this->config->load()``

	.. php:method:: is_loaded($class)

		:param	string	$class: Class name
		:returns:	Singleton property name if found, FALSE if not
		:rtype:	mixed

		Allows you to check if a class has already been loaded or not.

		.. note:: The word "class" here refers to libraries and drivers.

		If the requested class has been loaded, the method returns its assigned
		name in the CI Super-object and FALSE if it's not::

			$this->load->library('form_validation');
			$this->load->is_loaded('Form_validation');	// returns 'form_validation'

			$this->load->is_loaded('Nonexistent_library');	// returns FALSE

		.. important:: If you have more than one instance of a class (assigned to
			different properties), then the first one will be returned.

		::

			$this->load->library('form_validation', $config, 'fv');
			$this->load->library('form_validation');

			$this->load->is_loaded('Form_validation');	// returns 'fv'

	.. php:method:: add_package_path($path[, $view_cascade = TRUE])

		:param	string	$path: Path to add
		:param	bool	$view_cascade: Whether to use cascading views
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		Adding a package path instructs the Loader class to prepend a given path
		for subsequent requests for resources. As an example, the "Foo Bar"
		application package above has a library named Foo_bar.php. In our
		controller, we'd do the following::

			$this->load->add_package_path(APPPATH.'third_party/foo_bar/')
				->library('foo_bar');

	.. php:method:: remove_package_path([$path = ''])

		:param	string	$path: Path to remove
		:returns:	CI_Loader instance (method chaining)
		:rtype:	CI_Loader

		When your controller is finished using resources from an application
		package, and particularly if you have other application packages you
		want to work with, you may wish to remove the package path so the Loader
		no longer looks in that directory for resources. To remove the last path
		added, simply call the method with no parameters.

		Or to remove a specific package path, specify the same path previously
		given to ``add_package_path()`` for a package.::

			$this->load->remove_package_path(APPPATH.'third_party/foo_bar/');

	.. php:method:: get_package_paths([$include_base = TRUE])

		:param	bool	$include_base: Whether to include BASEPATH
		:returns:	An array of package paths
		:rtype:	array

		Returns all currently available package paths.