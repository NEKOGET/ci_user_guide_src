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

セッションメタデータへのアクセス
================================

CodeIgniter の以前のバージョンでは、セッションデータ配列はデフォルトで4つの項目を含んでいました:
「session_id」「ip_address」「user_agent」「last_activity」です。

これはセッションを動かすために必要だった変数ですが、今の新しい実装には不要になりました。
しかしながら、あなたのアプリケーションがこれらの値に依存しているということもあるでしょう。
そこで、
これらにアクセスするための代替手段を記します:

  - session_id: ``session_id()``
  - ip_address: ``$_SERVER['REMOTE_ADDR']``
  - user_agent: ``$this->input->user_agent()`` (セッションでは使われません)
  - last_activity: ストレージによります。ストレートな方法はありません。ごめんなさい！

セッションの設定
================

CodeIgniter は通常、セットアップしてすぐ動きます。
しかしながらセッションはいろいろなアプリケーションで非常に敏感な部品であるため、
いくらか慎重に設定を行われなければなりません。
オプションとその影響のすべてを考慮するため、どうか時間を取って検討してください。

次のセッション関連の設定は 
**application/config/config.php** ファイルにあります:

============================ =============== ======================================== ============================================================================================
設定                         デフォルト      オプション                               説明
============================ =============== ======================================== ============================================================================================
**sess_driver**              files           files/database/redis/memcached/*custom*  使用するセッションストレージドライバ。
**sess_cookie_name**         ci_session      [A-Za-z\_-] のみ                         セッションクッキーの名前。
**sess_expiration**          7200 (2 時間)   秒数 (整数)                              セッションを保持したい秒数。
                                                                                      有効期限のないセッション (ブラウザを閉じるまで) にしたい場合は値を 0 に設定します。
**sess_save_path**           NULL            なし                                     ストレージの保存場所を指定しますが、使用するドライバに依存します。
**sess_match_ip**            FALSE           TRUE/FALSE (真偽値)                      セッションクッキーを読み取る際に、ユーザーの IP アドレスを検証するかどうか。
                                                                                      一部のインターネットサービスプロバイダが動的に IP アドレスを変更することに注意してください。
                                                                                      そのため、有効期限のないセッションをしたい場合は FALSE に設定するのが適当でしょう。
**sess_time_to_update**      300             秒数 (整数)                              このオプションは、セッションクラスが自分自身を再生成し、新しいセッション ID を作成する頻度を
                                                                                      制御します。 0 に設定すると、セッション ID の再生成を無効にします。
**sess_regenerate_destroy**  FALSE           TRUE/FALSE (真偽値)                      セッション ID の自動再生成をするときに古いセッション ID に関連付けられたセッションデータを
                                                                                      破棄するかどうか。 FALSE に設定すると、データはガベージコレクタによってあとで削除されます。
============================ =============== ======================================== ============================================================================================

.. note:: 上記のいずれも設定されていない場合、セッションライブラリは最後の手段として PHP の
	INI 設定を取得しようとします。古い CodeIgniter の
	「 sess_expire_on_close 」と同様にです。
	しかしながら、その振る舞いに依存してはなりません。
	予期しない結果を引き起こすか、将来変更される可能性があります。
	どうかすべてきちんと設定してください。

上記の値に加えて、クッキーとネイティブドライバは
次の設定値が適用されます。この値は :doc:`入力 <input>` および
:doc:`セキュリティ <security>` クラスと共有しています:

================== =============== ===========================================================================
設定               デフォルト      説明
================== =============== ===========================================================================
**cookie_domain**  ''              セッションが適用されるドメイン
**cookie_path**    /               セッションが適用されるパス
**cookie_secure**  FALSE           暗号化接続 (HTTPS) でのみセッションクッキーを作成するかどうか
================== =============== ===========================================================================

.. note:: 「 cookie_httponly 」の設定はセッションには影響しません。
	HttpOnly のパラメータはセキュリティ上の理由から常に有効になっています。
	加えて、「 cookie_prefix 」の設定は完全に
	無視されます。

セッションドライバ
==================

既に述べたように、セッションライブラリには 4 つのドライバ、
つまりストレージエンジンが付属しています:

  - files
  - database
  - redis
  - memcached

デフォルトでは `ファイルドライバ`_ がセッションが初期化されるときに使用されます。
それは最も安全な選択であり、どこでも動くと期待されるからです
(実質的にあらゆる実行環境はファイルシステムを持っています) 。

一方、他のドライバは **application/config/config.php** ファイルの ``$config['sess_driver']``
により選択することができます、あなたがそれを選ぶなら。
しかし、それぞれのドライバには異なる注意点があり、それらを使う前にそれら
(後述します) に熟知しておくべきということを頭にとどめておいてください。

また、デフォルトがあなたのユースケースを満たしていない場合、 `カスタムドライバ`_
を作成して使うことができます。

.. note:: CodeIgniter の以前のバージョンでは状況が異なり、「クッキードライバ」が唯一の選択肢であり、
	私たちは上記のオプションを提供していないことに対して
	遺憾の意を受けていました。私たちはコミュニティからのフィードバックに耳を傾けていますが、
	一方で私たちはクッキードライバが **安全ではない** ので機能落ちされたことを警告し、
	カスタムドライバでそれを複製　し　な　い　よ　う　
	おすすめします。

ファイルドライバ
----------------

「 files 」ドライバはセッションデータを格納するためにファイルシステムを使用しています。

それは正確に PHP そのもののデフォルトセッション実装のように動作するといって差し支えありませんが、
しかし場合により重要な細部となりえることに、
実際には同じコードではありません。それはいくつかの制限
(と利点) を持っているということを気にかけておいてください。

具体的には、
PHP の session.save_path
<http://php.net/manual/ja/session.configuration.php#ini.session.save-path>`_
で使用されるディレクトリレベルとモードの形式を サポートしていません、そして、そのオプションのほとんどは、
安全のためにハードコーディングされています。そのかわり、ただひとつ絶対パスだけは ``$config['sess_save_path']`` でサポートされています。

知っておくべきもう一つの重要なことは、
セッションファイルを格納するティレクトリとして広範囲に読み込み可能なディレクトリ、
または共有ディレクトリを使用していないことを確認することです。 *ただ一人あなただけが*
アクセス可能であるディレクトリを *sess_save_path* ディレクトリに選んだことを確実にしてください。
さもなくば、それを行うことができる誰もが、現在のセッションのどれでも盗むことができます
(「セッション固定」攻撃として知られています) 。

UNIX ライクなオペレーティングシステムでは、
これは通常 `chmod` コマンドによって 0700 モードで設定して保存されます。
そのモードはディレクトリの所有者だけが読み書き操作を実行することを可能にするものです。
しかし気をつけるべきは、スクリプトを *実行* しているシステムのユーザは通常、
あなた自身ではなく、かわりに「 www-data 」のようなものが使われるため、
パーミッションの設定だけではたぶんアプリケーションは動かなくなります。

かわりに、ご使用の環境に応じて、次のようなものを行う必要があります
::

	mkdir /<path to your application directory>/sessions/
	chmod 0700 /<path to your application directory>/sessions/
	chown www-data /<path to your application directory>/sessions/

ボーナスヒント
^^^^^^^^^^^^^^

あなたがたの何人かは、ファイルストレージは通常遅いので、
おそらく別のセッションのドライバを選択するでしょう。これは半分だけ真実です。

非常に簡単なテストはおそらく、 SQL データベースがより高速であると信じ込ませるようにあなたをだましますが、
しかし 99% のケースで、わずか数セッションだけ持っているあいだだけの真実です。
セッションが積み重ねられサーバ負荷が増えるにしたがい
――それが問題になったとき―― ほぼ一貫してファイルシステムのほうが
リレーショナルデータベースで組み上げるよりパフォーマンスに優れるでしょう。

つけくわえると、パフォーマンスだけが関心事であるなら、 `tmpfs <http://eddmann.com/posts/storing-php-sessions-file-caches-in-memory-using-tmpfs/>`_ (警告: 外部リソースです)
の使い方を調べるといいかもしれません 、
それは燃えるがごとくセッションを速くします。

データベースドライバ
--------------------

「 database 」ドライバは MySQL や PostgreSQL などのリレーショナルデータベースに
セッションを保存するのに使います。これは多くのユーザーに人気のある選択肢です。
開発者にとって容易にアプリケーションからアクセス可能になるからです――
単にデータベースにテーブルを新しく作るだけです。

しかしながら、満たされなければならないいくつかの条件があります:

  - **デフォルト** のデータベース接続 (つまりコントローラから
    ``$this->db`` としてアクセスできるもの) だけを使えます。
  - :doc:`クエリビルダ </database/query_builder>` を有効にしていることが
    必要です。
  - 持続的接続を使用することは　で　き　ま　せ　ん　。
  - *cache_on* 設定が有効な接続では使用することは　で　き　ま　せ　ん　。

「 database 」セッションドライバを使用するためにはまた、すでに言及したように、
テーブルをデータベース上に作成する必要があり、そして
``$config['sess_save_path']`` にテーブル名を設定します。
たとえばテーブル名として「 ci_sessions 」を使用する場合、
こうします:

	$config['sess_driver'] = 'database';
	$config['sess_save_path'] = 'ci_sessions';

.. note:: CodeIgniter の古いバージョンからアップグレードした際に
	「 sess_save_path 」を設定していない場合は、
	セッションライブラリは古い設定である「 sess_table_name 」をかわりに使います。
	将来的に削除されますので、
	この動作に頼らないようにしてください。

そしてもちろん、データベースにテーブルを作成します……

MySQL の場合::

	CREATE TABLE IF NOT EXISTS `ci_sessions` (
		`id` varchar(40) NOT NULL,
		`ip_address` varchar(45) NOT NULL,
		`timestamp` int(10) unsigned DEFAULT 0 NOT NULL,
		`data` blob NOT NULL,
		KEY `ci_sessions_timestamp` (`timestamp`)
	);

PostgreSQL の場合::

	CREATE TABLE "ci_sessions" (
		"id" varchar(40) NOT NULL,
		"ip_address" varchar(45) NOT NULL,
		"timestamp" bigint DEFAULT 0 NOT NULL,
		"data" text DEFAULT '' NOT NULL
	);

	CREATE INDEX "ci_sessions_timestamp" ON "ci_sessions" ("timestamp");

また、**「 sess_match_ip 」の設定に応じて** PRIMARY KEY を追加する必要があります。
次の例は MySQL と PostgreSQL の両方で動きます::

	// sess_match_ip = TRUE のとき
	ALTER TABLE ci_sessions ADD PRIMARY KEY (id, ip_address);

	// sess_match_ip = FALSE のとき
	ALTER TABLE ci_sessions ADD PRIMARY KEY (id);

	// 以前のプライマリキーを削除するとき (設定を変更するときに使います)
	ALTER TABLE ci_sessions DROP PRIMARY KEY;


.. important:: MySQL と PostgreSQL のデータベースのみが
	公式サポート対象です。ほかのデータベースでアドバイザリロック機構が
	提供されていないためです。ロックなしにセッションを使うと、
	特にAJAXを多用する場合において、あらゆる種類の問題を引き起こします。
	私たちはそのようなものはサポートしません。パフォーマンス問題を抱えている場合は、
	セッションデータを処理したあとに ``session_write_close()`` を
	使用してください。

Redis ドライバ
--------------

.. note:: Redis はロック機構を提供していないので、
	このドライバのロックは最大 300 秒間保持される別の値によって
	エミュレートされています。

Redis はそのハイパフォーマンスさからキャッシュにとてもよく使われるストレージエンジンで、
あなたが「 Redis 」セッションドライバを使用するのもまた
その性能の高さからでしょう。

欠点としては、リレーショナルデータベースほどにはどこでも使えるわけではなく、
`phpredis <https://github.com/phpredis/phpredis>`_ PHP拡張モジュールを
インストールしなければなりません、しかしそれは PHP
にはバンドルされていません。
Redisとその使われ方に詳しくなければ「 Redis 」ドライバは
使えないでしょう。

「 file 」と「 databse 」のドライバと同じように、
セッションを保存するストレージの場所を
``$config['sess_save_path']`` に設定する必要があります。
そのフォーマットはすこしだけそれらと異なり、また複雑です。
最高の説明が *phpredis* 拡張の README ファイルでなされているので、
私たちはそれへのリンクを渡すにとどめましょう。

	https://github.com/phpredis/phpredis#php-session-handler

.. warning:: CodeIgniter のセッションライブラリは実際の「 Redis 」の
	``session.save_handler`` を使用して　い　ま　せ　ん　。上記リンクのパス形式に **だけ**
	注意してください。

しかしながら、最も一般的なケースではシンプルな ``host:port`` のペアで
十分でしょう::

	$config['sess_driver'] = 'redis';
	$config['sess_save_path'] = 'tcp://localhost:6379';

Memcached ドライバ
------------------

.. note:: Memcache はロック機構を提供していないので、
	このドライバのロックは最大 300 秒間保持される別の値によって
	エミュレートされています。

「 memcached 」ドライバはほぼすべての項目で「 redis 」と非常によく似ています、
おそらくは用いやすさを除いては。 PHP の `Memcached
<http://php.net/memcached>`_ 拡張は PECL により提供され、また、いくつかの
Linux ディストリビューションはパッケージインストールで利用しやすくしています。

それ以外には、とくに偏見もひいきもなく、 Memcached は Redis
と変わるところはないと言えましょう――これもまたキャッシュによく使われ、
その速度で名高い製品です。

しかしながら重要な注意点として、 Memcached により保証されることは唯一、
Y 秒の期限を定められた値 X が Y 秒経過後にそれが削除されていることだけです
(その時間より前には削除されないということでは必ずしもありません) 。
極めてまれではありますが、
それによりセッションが失われる可能性があることを考慮するべきです。

``$config['sess_save_path']`` の形式は非常に簡便で、
``host:port`` のペアだけです::

	$config['sess_driver'] = 'memcached';
	$config['sess_save_path'] = 'localhost:11211';

ボーナスヒント
^^^^^^^^^^^^^^

複数サーバを使ったコロン区切りの値 (``:weight``) の *重みづけ* パラメータも
サポートされています。しかし、
私たちはそれを信頼できるほどのテストはしていないことにご注意ください。

あなたが (あなた自身のリスクで) この機能を試してみたい場合、
単にカンマ区切りで複数のサーバパスを並べます::

	// localhost はより高い優先度 (5) を与えられます、
	// 192.0.2.1 の重みづけ 1 と比べ。
	$config['sess_save_path'] = 'localhost:11211:5,192.0.2.1:11211:1';

カスタムドライバ
----------------

独自のカスタムセッションドライバを作成することもできます。
しかしながら、それはたいていの場合かんたんな仕事ではありません、
正しく動作させるにはたくさんの知識を必要とします。

次のことを知っておく必要があります。セッションが一般的にどのように機能するかだけでなく、
特に PHP でどのように機能するか、基礎となるストレージ機構がどのように機能するか、
デッドロックを避け (しかしロックの　抜　け　を避けながら) 同時実行をどのように制御するか、
そして最後に大事なこととして――潜在的セキュリティ問題をどのように制御するか、
これは軽んずべきことではありません。

かいつまんで言うと――素の PHP でどう実装すべきかを知らないなら、
CodeIgniter においても実装しようとすべきではありません。
忠告しましたよ。

セッションにいくつか機能を追加したい場合は、ベースとなる Session
クラスを継承してください、そのほうがはるかに簡単です。
その方法を学ぶには :doc:`ライブラリの作成 <../general/creating_libraries>`
の記事を読んでください。

さて、話を戻しましょう―― CodeIgniter のセッションドライバを作成するとき、
従わなければならない 3 つの原則があります:

  - ドライバのファイルは **application/libraries/Session/drivers/**
    の下に置いて、セッションクラスで使用される命名規則に従ってください。

    たとえば「 dummy 」ドライバを作成するとすれば、
    ``Session_dummy_driver`` のクラス名で、
    *application/libraries/Session/drivers/Session_dummy_driver.php* で定義する必要があります。

  - ``CI_Session_driver`` クラスを継承します。

    これはいくつかの内部向けヘルパーメソッドを持つ基礎的なクラスです。
    それはほかのクラスと同様に拡張できます、あなたが本当にそれが必要なら。
    しかし私たちはその方法を説明しようとは思いません……あなたがCIでの継承とオーバーライドに詳しいなら、
    もうすでにやり方がわかるはずです。
    そうでないなら、ええ、そもそもやるべきではありません。


  - `SessionHandlerInterface
    <http://php.net/sessionhandlerinterface>`_ インターフェースを実装します。

    .. note:: ``SessionHandlerInterface`` はPHP 5.4.0 以降からの提供であることに
    	気づかれたかもしれません。古い PHP で実行している場合、 CodeIgniter は
    	自動的に同じ内容のインターフェースを作ります。

    リンク先はどうして、どのように実装するかを説明しています。

そして、上記「 dummy 」ドライバの例をもとにすると、
次のようなものでおしまいになります::

	// application/libraries/Session/drivers/Session_dummy_driver.php:

	class CI_Session_dummy_driver extends CI_Session_driver implements SessionHandlerInterface
	{

		public function __construct(&$params)
		{
			// これを忘れては　な　り　ま　せ　ん
			parent::__construct($params);

			// 設定その他の初期化をします
		}

		public function open($save_path, $name)
		{
			// ストレージ機構 (接続) の初期化
		}

		public function read($session_id)
		{
			// (もしあれば) セッションデータの読み込み、ロックの獲得
		}

		public function write($session_id, $session_data)
		{
			// セッションデータの作成、更新 (データは存在しないかもしれません！)
		}

		public function close()
		{
			// ロックの開放、接続の切断など
		}

		public function destroy($session_id)
		{
			// close() メソッドの呼び出しと、現在のセッションデータの破壊 (順序は逆かもしれません)
		}

		public function gc($maxlifetime)
		{
			// 期限切れセッションの削除
		}

	}

すべてを適切にできたなら、ただいまから *sess_driver* 設定値に
「 dummy 」を設定し、独自ドライバを使用することができます。おめでとうございます！

******************
クラスリファレンス
******************

.. php:class:: CI_Session

	.. php:method:: userdata([$key = NULL])

		:param	mixed	$key: セッション値のキーまたは NULL
		:returns:	指定のキーに対応する値、またはすべての userdata の配列
		:rtype:	mixed

		Gets 特定の ``$_SESSION`` アイテムの値または、
		キーを指定しない場合はすべての「 userdata 」アイテムの値を取得します。
	
		.. note:: これは古いアプリケーションの
			後方互換性のためだけに残されたメソッドです。
			かわりに直接 ``$_SESSION`` を使うべきです。

	.. php:method:: all_userdata()

		:returns:	すべての userdata の配列
		:rtype:	array

		すべての「 userdata 」を含む配列を返します。

		.. note:: このメソッドは廃止予定です。かわりに ``userdata()``
			を引数なしで使ってください。

	.. php:method:: &get_userdata()

		:returns:	``$_SESSION`` への参照
		:rtype:	array

		``$_SESSION`` 配列への参照を返します。

		.. note:: これは古いアプリケーションの
			後方互換性のためだけに残されたメソッドです。

	.. php:method:: has_userdata($key)

		:param	string	$key: セッションアイテムのキー
		:returns:	キーが存在すれば TRUE 、そうでなければ FALSE
		:rtype:	bool

		アイテムが ``$_SESSION`` に存在するかを確認します。

		.. note:: これは古いアプリケーションの
			後方互換性のためだけに残されたメソッドです。
			これは ``isset($_SESSION[$key])`` のエイリアスにすぎません
			――どうぞかわりに isset() を使ってください。

	.. php:method:: set_userdata($data[, $value = NULL])

		:param	mixed	$data: セッションに設定するキーと値のペアの配列、または単体のキー。
		:param	mixed	$value:	引数 $data がキーなら、設定されたセッション値
		:rtype:	void

		スーパーグローバル変数 ``$_SESSION`` にデータを設定します。

		.. note:: これは古いアプリケーションの
			後方互換性のためだけに残されたメソッドです。

	.. php:method:: unset_userdata($key)

		:param	mixed	$key: 削除するセッションデータのキー、またはキーの配列
		:rtype:	void

		指定のキーをスーパーグローバル変数 ``$_SESSION``
		から削除します。

		.. note:: これは古いアプリケーションの
			後方互換性のためだけに残されたメソッドです。
			これは ``unset($_SESSION[$key])`` のエイリアスにすぎません
			――どうぞかわりに unset() を使ってください。

	.. php:method:: mark_as_flash($key)

		:param	mixed	$key: flashdata としてマークするキー、またはキーの配列
		:returns:	成功すれば TRUE 、失敗したら FALSE
		:rtype:	bool

		``$_SESSION`` アイテムのキー (または複数のキー) を
		「 flashdata 」としてマークします。

	.. php:method:: get_flash_keys()

		:returns:	すべての「 flashdata 」を含む配列.
		:rtype:	array

		``$_SESSION`` のうち
		「 flashdata 」としてマークされたすべての値の配列を取得します。

	.. php:method:: umark_flash($key)

		:param	mixed	$key: 「 flashdata 」のマークを外すキー、または複数のキーの配列
		:rtype:	void

		``$_SESSION`` アイテムのキー (または複数のキー) の
		「 flashdata 」としてのマークを外します。

	.. php:method:: flashdata([$key = NULL])

		:param	mixed	$key: Flashdata のキーまたは NULL
		:returns:	指定したキーに対応する値、またはすべての flashdata の配列
		:rtype:	mixed

		``$_SESSION`` アイテムのうち
		「 flashdata 」としてマークされた値、または引数が指定されない場合はすべての「 flashdata 」の配列
		を取得します。
	
		.. note:: これは古いアプリケーションの
			後方互換性のためだけに残されたメソッドです。
			かわりに直接 ``$_SESSION`` を使うべきです。

	.. php:method:: keep_flashdata($key)

		:param	mixed	$key: 保持したい Flashdata のキー、またはキーの配列
		:returns:	成功すれば TRUE 、失敗したら FALSE
		:rtype:	bool

		指定の「 flashdata 」のキーを
		次のリクエストでも保持します。

		.. note:: これは古いアプリケーションの
			後方互換性のためだけに残されたメソッドです。
			これは ``mark_as_flash()`` のエイリアスにすぎません。

	.. php:method:: set_flashdata($data[, $value = NULL])

		:param	mixed	$data: flashdata のキーと値のセットの配列、または単体のキー
		:param	mixed	$value:	引数 $data がキーなら、設定されたセッション値
		:rtype:	void

		スーパーグローバル変数 ``$_SESSION`` にデータを設定し、
		「 flashdata 」としてマークします。

		.. note:: これは古いアプリケーションの
			後方互換性のためだけに残されたメソッドです。

	.. php:method:: mark_as_temp($key[, $ttl = 300])

		:param	mixed	$key: tempdata としてマークするキー、またはキーの配列
		:param	int	$ttl: tempdata の生存秒数
		:returns:	成功すれば TRUE 、失敗したら FALSE
		:rtype:	bool

		``$_SESSION`` アイテムのキー (または複数のキー) を
		「 tempdata 」としてマークします。

	.. php:method:: get_temp_keys()

		:returns:	すべての「 tempdata 」を含む配列.
		:rtype:	array

		``$_SESSION`` のうち
		「 tempdata 」としてマークされたすべての値の配列を取得します。

	.. php:method:: umark_temp($key)

		:param	mixed	$key: 「 tempdata 」のマークを外すキー、または複数のキーの配列
		:rtype:	void

		``$_SESSION`` アイテムのキー (または複数のキー) の
		「 tempdata 」としてのマークを外します。

	.. php:method:: tempdata([$key = NULL])

		:param	mixed	$key: Tempdata のキーまたは NULL
		:returns:	指定したキーに対応する値、またはすべての tempdata の配列
		:rtype:	mixed

		``$_SESSION`` アイテムのうち
		「 tempdata 」としてマークされた値、または引数が指定されない場合はすべての「 flashdata 」の配列
		を取得します。
	
		.. note:: これは古いアプリケーションの
			後方互換性のためだけに残されたメソッドです。
			かわりに直接 ``$_SESSION`` を使うべきです。

	.. php:method:: set_tempdata($data[, $value = NULL])

		:param	mixed	$data: tempdata のキーと値のセットの配列、または単体のキー
		:param	mixed	$value:	引数 $data がキーなら、設定されたセッション値
		:param	int	$ttl: tempdata の生存秒数
		:rtype:	void

		スーパーグローバル変数 ``$_SESSION`` にデータを設定し、
		「 tempdata 」としてマークします。

		.. note:: これは古いアプリケーションの
			後方互換性のためだけに残されたメソッドです。

	.. php:method:: sess_regenerate([$destroy = FALSE])

		:param	bool	$destroy: セッションを破壊するかどうか
		:rtype:	void

		セッション ID を再生成します。
		オプションで現在のセッションを破壊します。

		.. note:: このメソッドは素の PHP の
			`session_regenerate_id()
			<http://php.net/session_regenerate_id>`_ 関数のエイリアスにすぎません。

	.. php:method:: sess_destroy()

		:rtype:	void

		現在のセッションを破壊します。

		.. note:: これはセッション関連機能の *最後に* 呼ばれる必要があります。
			すべてのセッションデータはこれのあとには
			失われます。

		.. note:: このメソッドは素の PHP の
			`session_destroy()
			<http://php.net/session_destroy>`_ 関数のエイリアスにすぎません。

	.. php:method:: __get($key)

		:param	string	$key: セッションアイテムのキー
		:returns:	要求されたセッションアイテム、または存在しない場合は NULL
		:rtype:	mixed

		これはマジックメソッドで、
		``$_SESSION['item']`` のかわりに ``$this->session->item`` で使えるようにするものです、
		もしあなたがお好みなら。

		``$this->session->session_id``にアクセスした場合、
		これは ``session_id()`` によって取得されるセッション ID を
		返します。

	.. php:method:: __set($key, $value)

		:param	string	$key: セッションアイテムのキー
		:param	mixed	$value: セッションアイテムのキーに設定する値
		:returns:	void

		これはマジックメソッドで、
		``$this->session`` プロパティで ``$_SESSION`` に 
		アクセスできるようにするものです::

			$this->session->foo = 'bar';

			// 結果として:
			// $_SESSION['foo'] = 'bar';
