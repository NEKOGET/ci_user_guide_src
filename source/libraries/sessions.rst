####################
セッションライブラリ
####################

セッションクラスは、サイトを見ている間のユーザの「状態」を維持し、
ユーザのアクティビティの追跡を可能にします。

CodeIgniter にはいくつかのセッションストレージドライバが付属しています:

  - files (デフォルト; ファイルシステムベース)
  - database
  - redis
  - memcached

また、あなた自身のカスタムセッションドライバを作ることができます。
セッションクラスの機能の良さを利用ながら、ほかの種類のストレージをベースにして、
です。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

************************
セッションクラスの使い方
************************

セッションの初期化
==================

セッションは通常、各ページをまたいでグローバルに実行されます。
そのため、セッションクラスは次のどちらかによって初期化されるべきです、
:doc:`コントローラ<../general/controllers>` のコンストラクタ、
またはシステムによる :doc:`オートロード<../general/autoloader>` によってです。
たいていの場合、セッションクラスはバックグラウンドで黙って実行されます。
そして明示的なクラスの初期化は読み込み、作成、更新が必要な時にだけ
行えばよいです。

セッションクラスの初期化をコントローラのコンストラクタで手動で
行うには、 ``$this->load->library()`` メソッドを使用します::

	$this->load->library('session');

いちどロードされれば、セッションライブラリのオブジェクトは次のように利用することができます::

	$this->session

.. important:: 変数 ``$this->load`` の :doc:`ローダクラス</libraries/loader>` は CodeIgniter の基本となる
	コントローラによってインスタンス化されるため、コントローラのコンストラクタでの
	``parent::__construct()`` の呼び出しはライブラリをロードしようとする前に
	行ってください。

セッションはどのように動作しますか？
====================================

ページが読み込まれると、セッションクラスは有効なセッションクッキーが
ユーザのブラウザから送信されたかどうかをチェックします。
セッションクッキーが存在 **しない** 場合 (またはサーバに保存されているものと一致しないか、
有効期限が切れている場合は) 、新しいセッションが作成され、保存されます。

有効なセッションが存在する場合、その情報が更新されます。
更新するごとにセッション ID を再生成されるように設定することもできます。

いちど初期化されたら、セッションクラスは自動的に実行されるということを
理解することは重要なことです。上記の動作をさせるためにあなたが何かをする
必要はありません。以降に書かれているように、セッションデータを使いながら
あなたのやりたい処理を書くことができますが、セッションの読み込み、書き込み、
更新のプロセスは自動的に行われます。

.. note:: CLIにおいては、セッションライブラリは自動的に停止します。
	セッションはもっぱら HTTP プロトコルにもとづく概念だからです。

並行性に関する注意
------------------

AJAX を多用しているウェブサイトを開発しているわけではないかぎり、
このセクションはスキップしてもよいです。しかしながらそうでない場合、さらには
パフォーマンスの問題が発生している場合、この注意はまさにあなたが探しているものでしょう。

CodeIgniter の以前のバージョンのセッションはロックを実装していませんでした。
すなわち、同一のセッションを利用するふたつの HTTP リクエストはまったく同時に
実行できていました。より適切な専門用語で言うならば、
リクエストは non-blocking でした。

しかしながら、セッションを使う状況においての non-blocking なリクエストは安全ではないと
いうことも意味しました。なぜなら片方のリクエストにおけるセッションデータの更新 (または
セッション ID の再生成) はもう片方の、同時実行しているリクエストの妨げになりえるからです。
このことはいろいろな問題の根っことなり、 CodeIgniter 3.0 でセッションライブラリを
完全に書き直すおもな理由となりました。

なぜ私たちはわざわざこんなことを言っているのですか？　それはこのあとあなたが
パフォーマンス問題の原因を見つけ出そうとして、ロックが問題であると断定して
それゆえロックをどうやって外そうかと調べ始めるだろうから……。

そ　ん　な　こ　と　は　し　な　い　で　！　ロックの削除は間違っているでしょう、
そしてより多くの問題が発生します！

ロックは問題ではなく、それは解決になりません。あなたの問題は、あなたがもうすでに
処理をし終わってもう不要になったにもかかわらず、ひらいたセッションを持ちっぱなしに
していることです。なので必要なことは、現在のリクエストのためのセッションが
もういらなくなったあと、セッションを閉じることです。

短く言えば、セッション変数を使い終えたら ``session_write_close()`` を
呼び出してください。

セッションデータとは何か？
==========================

セッションデータは、特定のセッション ID (クッキー) に結びつけられた
単なる配列です。

もし以前から PHP でセッションを利用しているなら、
PHP の `$_SESSION スーパーグローバル変数 <http://php.net/manual/ja/reserved.variables.session.php>`_ に精通している
ことでしょう (そうでない場合は、そのリンクのコンテンツをお読みください) 。

CodeIgniter はセッションデータへのアクセス方法をそれと同じ手段で提供します。
つまり、 PHP のセッションハンドラの仕組みを使っています。
セッションデータの使い方は $ _SESSION 配列を操作 (読み込み、設定および削除) するのと同じくらい
簡単です。

くわえて、CodeIgniterでは以下に説明されているさらに 2 種類の
セッションデータを提供します: flashdata と tempdata です。

.. note:: 以前のバージョンでは、CodeIgniter の標準的なセッションデータは
	「 userdata 」と呼ばれていました。その用語がマニュアルの他の場所で使用されている場合は、
	このことを気に留めておいてくださいそのほとんどがカスタム「 userdata 」メソッドが
	どのように動作するかを説明するために書かれています。

セッションデータの取得
======================

セッション配列のどんな情報でも、 ``$ _SESSION`` スーパーグローバル変数を通して
利用できます::

	$_SESSION['item']

もしくはマジックメソッドを通して::

	$this->session->item

そして後方互換性のために、 ``userdata()`` メソッドを通して::

	$this->session->userdata('item');

item は、取得したい項目に対応する配列のキーです。
たとえば ``$name`` 変数に以前に格納した「 name 」の項目を割り当てるには、
こうします::

	$name = $_SESSION['name'];

	// または:

	$name = $this->session->name

	// または:

	$name = $this->session->userdata('name');

.. note:: あなたがアクセスしようとしている項目が存在しない場合、 ``userdata()`` メソッドは NULL を
	返します。

存在するすべてのユーザーデータを取得したい場合は、
単にキーを省略してください (マジックメソッドはプロパティとしてのみ動きます) ::

	$_SESSION

	// または:

	$this->session->userdata();

セッションデータを追加する
==========================

それでは、あなたのサイトにあるユーザがログインしたとしましょう。
認証されると、セッションにユーザー名とメールアドレスを追加することができます。
あなたがそれを必要とするときに、どこでも、データベースクエリを実行することなく
取得できるようにするためです。

シンプルに ``$ _SESSION`` 配列にデータを割り当てることができます、ほかの変数と同じようにです。
もしくは ``$this->session`` プロパティとして割り当てられます。

あるいは、古い方法である「 userdata 」も使えます。
とはいえそれは ``set_userdata()`` メソッドに新しいデータを含む配列を
渡すだけです::

	$this->session->set_userdata($array);

``$array`` はあなたの新しいデータを含む連想配列です。
次に例を示します::

	$newdata = array(
		'username'  => 'johndoe',
		'email'     => 'johndoe@some-site.com',
		'logged_in' => TRUE
	);

	$this->session->set_userdata($newdata);

ユーザデータにひとつひとつ値を追加したい場合、 ``set_userdata()`` は
次の構文もサポートしています::

	$this->session->set_userdata('some_name', 'some_value');

セッション値が存在することを確認したい場合は、単に ``isset()`` で
確認してください::

	// 'some_name' の値が存在しない場合またはNULLである場合は FALSE を、
	// それ以外の場合は TRUE を返します:
	isset($_SESSION['some_name'])

もしくは ``has_userdata()`` を呼び出します::

	$this->session->has_userdata('some_name');

セッションデータを削除する
==========================

他の変数とまったく同じように、 ``$_SESSION`` の値を
削除するには ``unset()`` を使えます::

	unset($_SESSION['some_name']);

	// 複数の値を削除したい場合:

	unset(
		$_SESSION['some_name'],
		$_SESSION['another_name']
	);

また、セッションに情報を追加するために ``set_userdata()`` が
使えるように、 ``unset_userdata()`` にキーを渡すことで削除することができます。
例として、セッションデータ配列から「 some_name 」を
削除したい場合は::

	$this->session->unset_userdata('some_name');

このメソッドは削除したい項目のキーの配列を受けつけます::

	$array_items = array('username', 'email');

	$this->session->unset_userdata($array_items);

.. note:: 以前のバージョンでは ``unset_userdata()`` メソッド
	は ``key => 'dummy value'`` の連想配列を受けつけていました。
	これはもうサポートされなくなりました。

フラッシュデータ
================

CodeIgniter では「 flashdata 」をサポートします。すなわち、次のリクエストのためだけに利用でき、
その後自動的にクリアされるセッションデータです。

これは非常に便利で、特に1回だけの情報、エラーまたは
ステータスメッセージに使えます (たとえば: 「レコード2を削除しました」) 。

flashdata 変数は通常のセッション変数であり、「 __ci_vars 」キーによりとある方法で
マークされただけのものであることに注意してください
(__ci_vars には触らないでくださいね、忠告しましたよ？) 。

「 flashdata 」としてすでに作ったアイテムをマークするにはこうします::

	$this->session->mark_as_flash('item');

フラッシュデータとして複数の項目をマークしたい場合は、単に配列としてキーを
渡します::

	$this->session->mark_as_flash(array('item', 'item2'));

フラッシュデータを追加するにはこうします::

	$_SESSION['item'] = 'value';
	$this->session->mark_as_flash('item');

または別法として、 ``set_flashdata()`` メソッドを使います::

	$this->session->set_flashdata('item', 'value');

また、 ``set_userdata()`` と同じ方法で ``set_flashdata()`` に配列を渡すことが
できます。

フラッシュデータ変数の読み取りは通常のセッションデータと同じく
``$_SESSION`` を使います::

	$_SESSION['item']

.. important:: ``userdata()`` メソッドはフラッシュデータを返しません。

「 flashdata 」だけを確実に読みたい場合 (ほかの種類のセッションを
読みたくない場合) 、 ``flashdata()`` メソッドを使用することができます::

	$this->session->flashdata('item');

フラッシュデータを全部取得するには、単にキーパラメータを省略します::

	$this->session->flashdata();

.. note:: フラッシュデータがない場合、 ``flashdata()`` メソッドはNULLを
	返します。

もし次のリクエストでもフラシュデータ変数を保持する必要があるとわかった場合、
``keep_flashdata()`` メソッドを使用して保持することができます。
単一のフラッシュデータ、またはフラッシュデータの配列を渡せます。

::

	$this->session->keep_flashdata('item');
	$this->session->keep_flashdata(array('item1', 'item2', 'item3'));

テンプデータ
============

CodeIgniter は「 tempdata 」をサポートします。特定の有効期限を持つセッションデータです。
値の有効期限が切れた、またはセッションの有効期限が切れるか削除された後、
値は自動的に削除されます。

フラッシュデータと同様に、テンプデータ変数はとある方法で「 __ci_vars 」キーで
マークされた通常のセッション変数です (もう一度言います。
__ci_vars には触らないでください) 。

すでにあるアイテムを「 tempdata 」としてマークするには、シンプルにそのキーと有効期限を
(単位は秒で！) ``mark_as_temp()`` メソッドに渡してください::

	// 'item' は 300 秒後に消されます
	$this->session->mark_as_temp('item', 300);

複数のアイテムを tempdata としてマークできますが、
有効期限が同じかどうかにより方法がふたつあります::

	// 'item' と 'item2' の両方が 300 秒後に期限切れします
	$this->session->mark_as_temp(array('item', 'item2'), 300);

	// 'item'  は 300 秒後に、
	// そのあいだに 'item2' は240 秒後に消えます
	$this->session->mark_as_temp(array(
		'item'	=> 300,
		'item2'	=> 240
	));

テンプデータを追加するにはこうします::

	$_SESSION['item'] = 'value';
	$this->session->mark_as_temp('item', 300); // 5 分で期限切れ

または別法として、 set_tempdata() メソッドを使用します::

	$this->session->set_tempdata('item', 'value', 300);

また、 ``set_tempdata()`` には配列を渡すことができます::

	$tempdata = array('newuser' => TRUE, 'message' => 'Thanks for joining!');

	$this->session->set_tempdata($tempdata, NULL, $expire);

.. note:: 有効期限を省略するか 0 に設定した場合、
	デフォルトの有効期限 300 秒 (5分) が使用されます。

テンプデータ変数を読み取るには、またですが、
$_SESSION スーパーグローバル配列でアクセスすることができます::

	$_SESSION['item']

.. important:: ``userdata()`` メソッドはテンプデータを返しません。

「 tempdata 」だけを確実に読みたい場合 (ほかの種類のセッションを読みたくない場合) 、
``tempdata()`` メソッドを使用することができます::

	$this->session->tempdata('item');

そしてもちろん、すべてのテンプデータを取得する場合はこうします::

	$this->session->tempdata();

.. note:: テンプデータが見つからない場合 ``tempdata()`` メソッドは
	NULL を返します。

有効期限が切れる前にテンプデータ値を削除する必要がある場合は
``$_SESSION`` 配列を直接 unset してください::

	unset($_SESSION['item']);

しかしながら、この方法はアイテムをテンプデータとするマーカーを削除しません
(次の HTTP リクエストで不正扱いになります) 。
そのため同じリクエストで同じキーを再利用したい場合、
``unset_tempdata()`` を使用すべきところでしょう::

	$this->session->unset_tempdata('item');

セッションの破棄
================

現在のセッションをクリアするには (例えばログアウト時) 、
単に PHP の `session_destroy()  <http://php.net/session_destroy>`_ 関数を使うか、
``sess_destroy()`` メソッドを使います。
どちらもまったく同じです::

	session_destroy();

	// または

	$this->session->sess_destroy();

.. note:: これは同じリクエストで行うセッション関連の最後の操作でなければなりません。
	すべてのセッションデータ (フラッシュデータとテンプデータを含む)
	は永続的に破壊され、
	その後は同じリクエスト内ではセッション機能は使えなくなります。

Accessing session metadata
==========================

In previous CodeIgniter versions, the session data array included 4 items
by default: 'session_id', 'ip_address', 'user_agent', 'last_activity'.

This was due to the specifics of how sessions worked, but is now no longer
necessary with our new implementation. However, it may happen that your
application relied on these values, so here are alternative methods of
accessing them:

  - session_id: ``session_id()``
  - ip_address: ``$_SERVER['REMOTE_ADDR']``
  - user_agent: ``$this->input->user_agent()`` (unused by sessions)
  - last_activity: Depends on the storage, no straightforward way. Sorry!

Session Preferences
===================

CodeIgniter will usually make everything work out of the box. However,
Sessions are a very sensitive component of any application, so some
careful configuration must be done. Please take your time to consider
all of the options and their effects.

You'll find the following Session related preferences in your
**application/config/config.php** file:

============================ =============== ======================================== ============================================================================================
Preference                   Default         Options                                  Description
============================ =============== ======================================== ============================================================================================
**sess_driver**              files           files/database/redis/memcached/*custom*  The session storage driver to use.
**sess_cookie_name**         ci_session      [A-Za-z\_-] characters only              The name used for the session cookie.
**sess_expiration**          7200 (2 hours)  Time in seconds (integer)                The number of seconds you would like the session to last.
                                                                                      If you would like a non-expiring session (until browser is closed) set the value to zero: 0
**sess_save_path**           NULL            None                                     Specifies the storage location, depends on the driver being used.
**sess_match_ip**            FALSE           TRUE/FALSE (boolean)                     Whether to validate the user's IP address when reading the session cookie.
                                                                                      Note that some ISPs dynamically changes the IP, so if you want a non-expiring session you
                                                                                      will likely set this to FALSE.
**sess_time_to_update**      300             Time in seconds (integer)                This option controls how often the session class will regenerate itself and create a new
                                                                                      session ID. Setting it to 0 will disable session ID regeneration.
**sess_regenerate_destroy**  FALSE           TRUE/FALSE (boolean)                     Whether to destroy session data associated with the old session ID when auto-regenerating
                                                                                      the session ID. When set to FALSE, the data will be later deleted by the garbage collector.
============================ =============== ======================================== ============================================================================================

.. note:: As a last resort, the Session library will try to fetch PHP's
	session related INI settings, as well as legacy CI settings such as
	'sess_expire_on_close' when any of the above is not configured.
	However, you should never rely on this behavior as it can cause
	unexpected results or be changed in the future. Please configure
	everything properly.

In addition to the values above, the cookie and native drivers apply the
following configuration values shared by the :doc:`Input <input>` and
:doc:`Security <security>` classes:

================== =============== ===========================================================================
Preference         Default         Description
================== =============== ===========================================================================
**cookie_domain**  ''              The domain for which the session is applicable
**cookie_path**    /               The path to which the session is applicable
**cookie_secure**  FALSE           Whether to create the session cookie only on encrypted (HTTPS) connections
================== =============== ===========================================================================

.. note:: The 'cookie_httponly' setting doesn't have an effect on sessions.
	Instead the HttpOnly parameter is always enabled, for security
	reasons. Additionaly, the 'cookie_prefix' setting is completely
	ignored.

Session Drivers
===============

As already mentioned, the Session library comes with 4 drivers, or storage
engines, that you can use:

  - files
  - database
  - redis
  - memcached

By default, the `Files Driver`_ will be used when a session is initialized,
because it is the most safe choice and is expected to work everywhere
(virtually every environment has a file system).

However, any other driver may be selected via the ``$config['sess_driver']``
line in your **application/config/config.php** file, if you chose to do so.
Have it in mind though, every driver has different caveats, so be sure to
get yourself familiar with them (below) before you make that choice.

In addition, you may also create and use `Custom Drivers`_, if the ones
provided by default don't satisfy your use case.

.. note:: In previous CodeIgniter versions, a different, "cookie driver"
	was the only option and we have received negative feedback on not
	providing that option. While we do listen to feedback from the
	community, we want to warn you that it was dropped because it is
	**unsafe** and we advise you NOT to try to replicate it via a
	custom driver.

Files Driver
------------

The 'files' driver uses your file system for storing session data.

It can safely be said that it works exactly like PHP's own default session
implementation, but in case this is an important detail for you, have it
mind that it is in fact not the same code and it has some limitations
(and advantages).

To be more specific, it doesn't support PHP's `directory level and mode
formats used in session.save_path
<http://php.net/manual/en/session.configuration.php#ini.session.save-path>`_,
and it has most of the options hard-coded for safety. Instead, only
absolute paths are supported for ``$config['sess_save_path']``.

Another important thing that you should know, is to make sure that you
don't use a publicly-readable or shared directory for storing your session
files. Make sure that *only you* have access to see the contents of your
chosen *sess_save_path* directory. Otherwise, anybody who can do that, can
also steal any of the current sessions (also known as "session fixation"
attack).

On UNIX-like operating systems, this is usually achieved by setting the
0700 mode permissions on that directory via the `chmod` command, which
allows only the directory's owner to perform read and write operations on
it. But be careful because the system user *running* the script is usually
not your own, but something like 'www-data' instead, so only setting those
permissions will probable break your application.

Instead, you should do something like this, depending on your environment
::

	mkdir /<path to your application directory>/sessions/
	chmod 0700 /<path to your application directory>/sessions/
	chown www-data /<path to your application directory>/sessions/

Bonus Tip
^^^^^^^^^

Some of you will probably opt to choose another session driver because
file storage is usually slower. This is only half true.

A very basic test will probably trick you into believing that an SQL
database is faster, but in 99% of the cases, this is only true while you
only have a few current sessions. As the sessions count and server loads
increase - which is the time when it matters - the file system will
consistently outperform almost all relational database setups.

In addition, if performance is your only concern, you may want to look
into using `tmpfs <http://eddmann.com/posts/storing-php-sessions-file-caches-in-memory-using-tmpfs/>`_,
(warning: external resource), which can make your sessions blazing fast.

Database Driver
---------------

The 'database' driver uses a relational database such as MySQL or
PostgreSQL to store sessions. This is a popular choice among many users,
because it allows the developer easy access to the session data within
an application - it is just another table in your database.

However, there are some conditions that must be met:

  - Only your **default** database connection (or the one that you access
    as ``$this->db`` from your controllers) can be used.
  - You must have the :doc:`Query Builder </database/query_builder>`
    enabled.
  - You can NOT use a persistent connection.
  - You can NOT use a connection with the *cache_on* setting enabled.

In order to use the 'database' session driver, you must also create this
table that we already mentioned and then set it as your
``$config['sess_save_path']`` value.
For example, if you would like to use 'ci_sessions' as your table name,
you would do this::

	$config['sess_driver'] = 'database';
	$config['sess_save_path'] = 'ci_sessions';

.. note:: If you've upgraded from a previous version of CodeIgniter and
	you don't have 'sess_save_path' configured, then the Session
	library will look for the old 'sess_table_name' setting and use
	it instead. Please don't rely on this behavior as it will get
	removed in the future.

And then of course, create the database table ...

For MySQL::

	CREATE TABLE IF NOT EXISTS `ci_sessions` (
		`id` varchar(40) NOT NULL,
		`ip_address` varchar(45) NOT NULL,
		`timestamp` int(10) unsigned DEFAULT 0 NOT NULL,
		`data` blob NOT NULL,
		KEY `ci_sessions_timestamp` (`timestamp`)
	);

For PostgreSQL::

	CREATE TABLE "ci_sessions" (
		"id" varchar(40) NOT NULL,
		"ip_address" varchar(45) NOT NULL,
		"timestamp" bigint DEFAULT 0 NOT NULL,
		"data" text DEFAULT '' NOT NULL
	);

	CREATE INDEX "ci_sessions_timestamp" ON "ci_sessions" ("timestamp");

You will also need to add a PRIMARY KEY **depending on your 'sess_match_ip'
setting**. The examples below work both on MySQL and PostgreSQL::

	// When sess_match_ip = TRUE
	ALTER TABLE ci_sessions ADD PRIMARY KEY (id, ip_address);

	// When sess_match_ip = FALSE
	ALTER TABLE ci_sessions ADD PRIMARY KEY (id);

	// To drop a previously created primary key (use when changing the setting)
	ALTER TABLE ci_sessions DROP PRIMARY KEY;


.. important:: Only MySQL and PostgreSQL databases are officially
	supported, due to lack of advisory locking mechanisms on other
	platforms. Using sessions without locks can cause all sorts of
	problems, especially with heavy usage of AJAX, and we will not
	support such cases. Use ``session_write_close()`` after you've
	done processing session data if you're having performance
	issues.

Redis Driver
------------

.. note:: Since Redis doesn't have a locking mechanism exposed, locks for
	this driver are emulated by a separate value that is kept for up
	to 300 seconds.

Redis is a storage engine typically used for caching and popular because
of its high performance, which is also probably your reason to use the
'redis' session driver.

The downside is that it is not as ubiquitous as relational databases and
requires the `phpredis <https://github.com/phpredis/phpredis>`_ PHP
extension to be installed on your system, and that one doesn't come
bundled with PHP.
Chances are, you're only be using the 'redis' driver only if you're already
both familiar with Redis and using it for other purposes.

Just as with the 'files' and 'database' drivers, you must also configure
the storage location for your sessions via the
``$config['sess_save_path']`` setting.
The format here is a bit different and complicated at the same time. It is
best explained by the *phpredis* extension's README file, so we'll simply
link you to it:

	https://github.com/phpredis/phpredis#php-session-handler

.. warning:: CodeIgniter's Session library does NOT use the actual 'redis'
	``session.save_handler``. Take note **only** of the path format in
	the link above.

For the most common case however, a simple ``host:port`` pair should be
sufficient::

	$config['sess_driver'] = 'redis';
	$config['sess_save_path'] = 'tcp://localhost:6379';

Memcached Driver
----------------

.. note:: Since Memcache doesn't have a locking mechanism exposed, locks
	for this driver are emulated by a separate value that is kept for
	up to 300 seconds.

The 'memcached' driver is very similar to the 'redis' one in all of its
properties, except perhaps for availability, because PHP's `Memcached
<http://php.net/memcached>`_ extension is distributed via PECL and some
Linux distrubutions make it available as an easy to install package.

Other than that, and without any intentional bias towards Redis, there's
not much different to be said about Memcached - it is also a popular
product that is usually used for caching and famed for its speed.

However, it is worth noting that the only guarantee given by Memcached
is that setting value X to expire after Y seconds will result in it being
deleted after Y seconds have passed (but not necessarily that it won't
expire earlier than that time). This happens very rarely, but should be
considered as it may result in loss of sessions.

The ``$config['sess_save_path']`` format is fairly straightforward here,
being just a ``host:port`` pair::

	$config['sess_driver'] = 'memcached';
	$config['sess_save_path'] = 'localhost:11211';

Bonus Tip
^^^^^^^^^

Multi-server configuration with an optional *weight* parameter as the
third colon-separated (``:weight``) value is also supported, but we have
to note that we haven't tested if that is reliable.

If you want to experiment with this feature (on your own risk), simply
separate the multiple server paths with commas::

	// localhost will be given higher priority (5) here,
	// compared to 192.0.2.1 with a weight of 1.
	$config['sess_save_path'] = 'localhost:11211:5,192.0.2.1:11211:1';

Custom Drivers
--------------

You may also create your own, custom session drivers. However, have it in
mind that this is typically not an easy task, as it takes a lot of
knowledge to do it properly.

You need to know not only how sessions work in general, but also how they
work specifically in PHP, how the underlying storage mechanism works, how
to handle concurrency, avoid deadlocks (but NOT through lack of locks) and
last but not least - how to handle the potential security issues, which
is far from trivial.

Long story short - if you don't know how to do that already in raw PHP,
you shouldn't be trying to do it within CodeIgniter either. You've been
warned.

If you only want to add some extra functionality to your sessions, just
extend the base Session class, which is a lot more easier. Read the
:doc:`Creating Libraries <../general/creating_libraries>` article to
learn how to do that.

Now, to the point - there are three general rules that you must follow
when creating a session driver for CodeIgniter:

  - Put your driver's file under **application/libraries/Session/drivers/**
    and follow the naming conventions used by the Session class.

    For example, if you were to create a 'dummy' driver, you would have
    a ``Session_dummy_driver`` class name, that is declared in
    *application/libraries/Session/drivers/Session_dummy_driver.php*.

  - Extend the ``CI_Session_driver`` class.

    This is just a basic class with a few internal helper methods. It is
    also extendable like any other library, if you really need to do that,
    but we are not going to explain how ... if you're familiar with how
    class extensions/overrides work in CI, then you already know how to do
    it. If not, well, you shouldn't be doing it in the first place.


  - Implement the `SessionHandlerInterface
    <http://php.net/sessionhandlerinterface>`_ interface.

    .. note:: You may notice that ``SessionHandlerInterface`` is provided
    	by PHP since version 5.4.0. CodeIgniter will automatically declare
    	the same interface if you're running an older PHP version.

    The link will explain why and how.

So, based on our 'dummy' driver example above, you'd end up with something
like this::

	// application/libraries/Session/drivers/Session_dummy_driver.php:

	class CI_Session_dummy_driver extends CI_Session_driver implements SessionHandlerInterface
	{

		public function __construct(&$params)
		{
			// DO NOT forget this
			parent::__construct($params);

			// Configuration & other initializations
		}

		public function open($save_path, $name)
		{
			// Initialize storage mechanism (connection)
		}

		public function read($session_id)
		{
			// Read session data (if exists), acquire locks
		}

		public function write($session_id, $session_data)
		{
			// Create / update session data (it might not exist!)
		}

		public function close()
		{
			// Free locks, close connections / streams / etc.
		}

		public function destroy($session_id)
		{
			// Call close() method & destroy data for current session (order may differ)
		}

		public function gc($maxlifetime)
		{
			// Erase data for expired sessions
		}

	}

If you've done everything properly, you can now set your *sess_driver*
configuration value to 'dummy' and use your own driver. Congratulations!

***************
Class Reference
***************

.. php:class:: CI_Session

	.. php:method:: userdata([$key = NULL])

		:param	mixed	$key: Session item key or NULL
		:returns:	Value of the specified item key, or an array of all userdata
		:rtype:	mixed

		Gets the value for a specific ``$_SESSION`` item, or an
		array of all "userdata" items if not key was specified.
	
		.. note:: This is a legacy method kept only for backwards
			compatibility with older applications. You should
			directly access ``$_SESSION`` instead.

	.. php:method:: all_userdata()

		:returns:	An array of all userdata
		:rtype:	array

		Returns an array containing all "userdata" items.

		.. note:: This method is DEPRECATED. Use ``userdata()``
			with no parameters instead.

	.. php:method:: &get_userdata()

		:returns:	A reference to ``$_SESSION``
		:rtype:	array

		Returns a reference to the ``$_SESSION`` array.

		.. note:: This is a legacy method kept only for backwards
			compatibility with older applications.

	.. php:method:: has_userdata($key)

		:param	string	$key: Session item key
		:returns:	TRUE if the specified key exists, FALSE if not
		:rtype:	bool

		Checks if an item exists in ``$_SESSION``.

		.. note:: This is a legacy method kept only for backwards
			compatibility with older applications. It is just
			an alias for ``isset($_SESSION[$key])`` - please
			use that instead.

	.. php:method:: set_userdata($data[, $value = NULL])

		:param	mixed	$data: An array of key/value pairs to set as session data, or the key for a single item
		:param	mixed	$value:	The value to set for a specific session item, if $data is a key
		:rtype:	void

		Assigns data to the ``$_SESSION`` superglobal.

		.. note:: This is a legacy method kept only for backwards
			compatibility with older applications.

	.. php:method:: unset_userdata($key)

		:param	mixed	$key: Key for the session data item to unset, or an array of multiple keys
		:rtype:	void

		Unsets the specified key(s) from the ``$_SESSION``
		superglobal.

		.. note:: This is a legacy method kept only for backwards
			compatibility with older applications. It is just
			an alias for ``unset($_SESSION[$key])`` - please
			use that instead.

	.. php:method:: mark_as_flash($key)

		:param	mixed	$key: Key to mark as flashdata, or an array of multiple keys
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		Marks a ``$_SESSION`` item key (or multiple ones) as
		"flashdata".

	.. php:method:: get_flash_keys()

		:returns:	Array containing the keys of all "flashdata" items.
		:rtype:	array

		Gets a list of all ``$_SESSION`` that have been marked as
		"flashdata".

	.. php:method:: umark_flash($key)

		:param	mixed	$key: Key to be un-marked as flashdata, or an array of multiple keys
		:rtype:	void

		Unmarks a ``$_SESSION`` item key (or multiple ones) as
		"flashdata".

	.. php:method:: flashdata([$key = NULL])

		:param	mixed	$key: Flashdata item key or NULL
		:returns:	Value of the specified item key, or an array of all flashdata
		:rtype:	mixed

		Gets the value for a specific ``$_SESSION`` item that has
		been marked as "flashdata", or an array of all "flashdata"
		items if no key was specified.
	
		.. note:: This is a legacy method kept only for backwards
			compatibility with older applications. You should
			directly access ``$_SESSION`` instead.

	.. php:method:: keep_flashdata($key)

		:param	mixed	$key: Flashdata key to keep, or an array of multiple keys
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		Retains the specified session data key(s) as "flashdata"
		through the next request.

		.. note:: This is a legacy method kept only for backwards
			compatibility with older applications. It is just
			an alias for the ``mark_as_flash()`` method.

	.. php:method:: set_flashdata($data[, $value = NULL])

		:param	mixed	$data: An array of key/value pairs to set as flashdata, or the key for a single item
		:param	mixed	$value:	The value to set for a specific session item, if $data is a key
		:rtype:	void

		Assigns data to the ``$_SESSION`` superglobal and marks it
		as "flashdata".

		.. note:: This is a legacy method kept only for backwards
			compatibility with older applications.

	.. php:method:: mark_as_temp($key[, $ttl = 300])

		:param	mixed	$key: Key to mark as tempdata, or an array of multiple keys
		:param	int	$ttl: Time-to-live value for the tempdata, in seconds
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		Marks a ``$_SESSION`` item key (or multiple ones) as
		"tempdata".

	.. php:method:: get_temp_keys()

		:returns:	Array containing the keys of all "tempdata" items.
		:rtype:	array

		Gets a list of all ``$_SESSION`` that have been marked as
		"tempdata".

	.. php:method:: umark_temp($key)

		:param	mixed	$key: Key to be un-marked as tempdata, or an array of multiple keys
		:rtype:	void

		Unmarks a ``$_SESSION`` item key (or multiple ones) as
		"tempdata".

	.. php:method:: tempdata([$key = NULL])

		:param	mixed	$key: Tempdata item key or NULL
		:returns:	Value of the specified item key, or an array of all tempdata
		:rtype:	mixed

		Gets the value for a specific ``$_SESSION`` item that has
		been marked as "tempdata", or an array of all "tempdata"
		items if no key was specified.
	
		.. note:: This is a legacy method kept only for backwards
			compatibility with older applications. You should
			directly access ``$_SESSION`` instead.

	.. php:method:: set_tempdata($data[, $value = NULL])

		:param	mixed	$data: An array of key/value pairs to set as tempdata, or the key for a single item
		:param	mixed	$value:	The value to set for a specific session item, if $data is a key
		:param	int	$ttl: Time-to-live value for the tempdata item(s), in seconds
		:rtype:	void

		Assigns data to the ``$_SESSION`` superglobal and marks it
		as "tempdata".

		.. note:: This is a legacy method kept only for backwards
			compatibility with older applications.

	.. php:method:: sess_regenerate([$destroy = FALSE])

		:param	bool	$destroy: Whether to destroy session data
		:rtype:	void

		Regenerate session ID, optionally destroying the current
		session's data.

		.. note:: This method is just an alias for PHP's native
			`session_regenerate_id()
			<http://php.net/session_regenerate_id>`_ function.

	.. php:method:: sess_destroy()

		:rtype:	void

		Destroys the current session.

		.. note:: This must be the *last* session-related function
			that you call. All session data will be lost after
			you do that.

		.. note:: This method is just an alias for PHP's native
			`session_destroy()
			<http://php.net/session_destroy>`_ function.

	.. php:method:: __get($key)

		:param	string	$key: Session item key
		:returns:	The requested session data item, or NULL if it doesn't exist
		:rtype:	mixed

		A magic method that allows you to use
		``$this->session->item`` instead of ``$_SESSION['item']``,
		if that's what you prefer.

		It will also return the session ID by calling
		``session_id()`` if you try to access
		``$this->session->session_id``.

	.. php:method:: __set($key, $value)

		:param	string	$key: Session item key
		:param	mixed	$value: Value to assign to the session item key
		:returns:	void

		A magic method that allows you to assign items to
		``$_SESSION`` by accessing them as ``$this->session``
		properties::

			$this->session->foo = 'bar';

			// Results in:
			// $_SESSION['foo'] = 'bar';
