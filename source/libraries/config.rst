############
設定クラス
############

設定クラスは、設定情報を取得する手段を提供します。設定情報は、 
デフォルトの設定ファイル (application/config/config.php) 
または、ユーザ定義の設定ファイルから取得できます。

.. note:: このクラスは、システムで自動的に初期化されるので、
手動で初期化する必要はありません。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

*****************************
設定クラスの機能
*****************************

設定ファイルの詳細
========================

初期状態では、CodeIgniter には、application/config/config.php 
にある一個のメイン設定ファイルがあります。 テキストエディタを
使ってファイルを開くと、$config という名前の配列に、設定項目が
セットされているのがわかると思います。

このファイルにユーザ定義の設定項目を追加することもできますし、設定項目を分けて
おきたい場合は (ユーザ定義の設定項目が必要だと仮定した場合の話です)、 単に、ユ
ーザ用のファイルを作成し、config フォルダに保存するだけです。

.. note:: ユーザ用の設定ファイルを作成する場合は、メインのものと同じ
フォーマットを使って、 $config という名前の配列に設定項目をセットし
てください。 CodeIgniter では(配列の添字が他と同じ名前でないとすれば)
配列の名前が同じであったとしても衝突がないように、設定ファイルが賢く
管理されます。

設定ファイルの読み込み
=====================

.. note::
 CodeIgniter では、メインの設定ファイル (application/config/config.php) 
 は自動で 読み込まれますので、 ユーザ定義の設定ファイルだけをロードする
 必要があります。

設定ファイルをロードするには2つの方法があります:

手動での読み込み
**************

ユーザ定義の設定ファイルをロードするには、その設定ファイルが必要に
なる:doc:`コントローラ </general/controllers>`の中で次のメソッド
を使います::

	$this->config->load('filename');

ここでの filename は、ファイル拡張子の .php を除いたユーザ定義
設定ファイルの名前になります。

複数の設定ファイルを読み込む必要がある場合、通常は一つのマスタ
設定配列に設定項目が展開されます。しかし、 別々の設定ファイルで
、同じ名前の添字を使っていた場合は、名前の衝突が起こる可能性が
あります。衝突を避けるには、第2引数を TRUE に設定し、格納先の配
列の添字でをそれぞれの設定ファイルを設定ファイルの名前にして、そ
の中に設定を格納させます。例:

	// 次のような形で配列に保管されます: $this->config['blog_settings'] = $config
	$this->config->load('blog_settings', TRUE);

上記の方法でセットした設定項目を読み取る方法については、下の 設定項目の取り出し
というタイトルのセクションを見てください。

第3引数で、設定ファイルが存在しなかった時に発生するエラーを出さないようにすることが
できます[ 訳注: TRUE に設定するとエラーがでなくなります ]。:

	$this->config->load('blog_settings', FALSE, TRUE);

自動読み込み
************

特定の設定ファイルをグローバルに利用すべきことが分かった場合、システム
でその設定を自動読み込みすることができます。これを実施するには、 
application/config/autoload.phpにある **autoload.php**ファイルを開き、
そこに書いてある方法に従って、 設定ファイルを追加してください。


設定項目の取り出し
=====================

設定ファイルから設定項目を読み取るには、次のメソッドを使います::

	$this->config->item('item name');

ここでの item name は、読み取りたい $config 配列の添字です。たとえば、
選択した言語を取得するには次のようにします::

	$lang = $this->config->item('language');

このメソッドは、読み取ろうとする項目が存在しない場合、
NULLを返します。

指定の添字に設定項目を代入するために、$this->config->load 
メソッドの第2引数を使った場合、 $this->config->item() メソッド
の第2引数でも指定した添字の名前を設定することで、それを読み取
ることができます。例:

	// blog_settings.php というファイル名の設定ファイルをロードし、"blog_settings" というインデックスに代入します
	$this->config->load('blog_settings', TRUE);

	// blog_settings 配列にある site_name という設定項目を取得します
	$site_name = $this->config->item('site_name', 'blog_settings');

	// 同じ項目を指定する別の方法です:
	$blog_config = $this->config->item('blog_settings');
	$site_name = $blog_config['site_name'];

設定項目をセットする
=====================

動的に設定項目をセットしたり既存の設定を変更したりするには、
下記のようなコードを使います::

	$this->config->set_item('item_name', 'item_value');

ここでの item_name は、変更したい項目の $config 配列
の添字で、 item_value はその値になります。

.. _config-environments:

複数の環境
============

現状の環境により異なった設定ファイルをロードすることができます。 
定数 ENVIRONMENT が index.php で定義されており、 :doc:`複数の
環境の取扱い </general/environments>`のセクションに詳細が記述
されています。 

環境固有の設定ファイルを作成するには、 application/config/{ENVIRONMENT}/{FILENAME}.php 
に設定ファイルを作成またはコピーします。 

たとえば、本番環境での config.php を作成するには、以下のようにします:

#. ディレクトリ application/config/production/ を作成します
#. 既存の config.php を上記のディレクトリにコピーします
#. application/config/production/config.php を編集し本番
環境の設定を記述します

定数 ENVIRONMENT を 'production' に設定すると、
新しく作成した本番環境用の config.php がロードされます。 

環境固有のフォルダに以下の設定ファイルを置くこと
ができます:

-  デフォルトの CodeIgniter の設定ファイル群
-  あなた自身のカスタム設定ファイル群

.. note::
 CodeIgniter は、現在の環境の設定ファイルを最初にロード
 しよう と試みます。もし、ファイルが存在しなければ、グ
 ローバルの設定 ファイル(すなわち、application/config/ 
 にあるファイル)をロー ドします。これは、あなたは **すべ
 て**の 設定ファイルを環境 固有のフォルダに置く必要はな
 い こと、環境により変更するファ イルだけを置けばよいこ
 とを意味します


***************
クラスリファレンス
***************

.. class:: CI_Config

	.. 属性:: $config

		ロードされたすべての設定値の配列

	.. 属性:: $is_loaded

		すべてのロードされた設定ファイルの配列


	.. メソッド:: item($item[, $index=''])

		:パラメータ	string	$item: Configの項目名
		:パラメータ	string	$index: インデックス名
		:返り値:	Configの項目値、見つからない場合はNULL
		:返り値型:	mixed

		設定ファイルの項目を取得します。

	.. メソッド:: set_item($item, $value)

		:パラメータ	string	$item: Configの項目名
		:パラメータ	string	$value: Configの項目値
		:返り値型:	void

		指定された値に設定ファイルの項目を設定します。

	.. メソッド:: slash_item($item)

		:パラメータ	string	$item: Configの項目名
		:返り値:	Configの項目フォワード末尾の値スラッシュ見つからない場合はnull
		:返り値型:	mixed

		この方法は、``item()``と同じです,  設定項目の末尾に
		スラッシュを加えます。

	.. メソッド:: load([$file = ''[, $use_sections = FALSE[, $fail_gracefully = FALSE]]])

		:パラメータ	string	$file: 構成ファイル名
		:パラメータ	bool	$use_sections: 設定値　独自のセクションにロードする必要があるかどうか(主な構成配列のインデックス)
		:パラメータ	bool	$fail_gracefully: falseを返す、またはエラーメッセージを表示するかどうか
		:返り値:	成功時　TRUE 失敗時　FALSE
		:返り値型:	bool

		設定ファイルをロードします。

	.. メソッド:: site_url()

		:返り値:	サイトURL
		:返り値型:	string

		このメソッドは、設定ファイルで、"index" の値に指定した、
		サイトへの URL を取得します。

		このメソッドは、通常:doc:`URLヘルパー </helpers/url_helper>`
		で対応する関数を経由してアクセスされます。

	.. メソッド:: base_url()

		:返り値:	ベース URL
		:返り値型:	string

		このメソッドは、サイトの URL、プラス、オプションの
		スタイルシートや画像などへのパスを取得します。

		このメソッドは、通常:doc:`URLヘルパー </helpers/url_helper>`
		で対応する関数を経由してアクセスされます。

	.. メソッド:: system_url()

		:返り値:	CI system/ フォルダの指しているURL
		:返り値型:	string

		このメソッドを使うと system フォルダ の URL を取得できます。

		.. note:: このメソッドは推奨されていません。理由は安全でない
		コーディングの使用を奨励しています。お使いの*system/* ディレ
		クトリは、公的にアクセス可能にすべきではありません。
