##################
PHP スタイルガイド
##################


このページでは CodeIgniter の開発に貢献する際に守っている
コーディングスタイルを記述します。あなた自身の CodeIgniter
アプリケーションではこのスタイルを使用する必要はありませんが、
推奨はいたします。

.. contents:: Table of Contents

ファイル形式
============

ファイルは Unicode (UTF-8) エンコーディングで保存すべきです。BOM
は使用 *しないべきです* 。UTF-16 や UTF-32 とは違い、
UTF-8 でエンコードされたファイルには示すべきバイトオーダーはなく、 BOM は
PHP の出力に悪影響があり、アプリケーションから独自のヘッダーを設定できないようにしてしまいます。
Unix の行末は (LF) を使用すべきです。

ここではよく使われるテキストエディタのいくつかで、
これらの設定を適用する方法を示します。テキストエディタの手順は変更される場合があります。
テキストエディタのマニュアルを確認してください。

TextMate
''''''''

#. アプリケーションの Preferences を開きます
#. Advanced をクリックし、「 Saving 」タブを選んでください
#. 「 File Encoding 」で「 UTF-8 (recommended) 」を選択してください
#. 「 Line Endings 」で「 LF (recommended) 」を選択してください
#. *オプション:* 新しい設定で開いたファイルを変更したい場合、「 Use for existing files as well 」に
   チェックしてください。

BBEdit
''''''

#. アプリケーションの Preferences を開きます
#. 左側の「 Text Encodings 」を選択します。
#. 「 Default text encoding for new documents 」で「 Unicode (UTF-8,
   no BOM) 」を選択してください
#. *オプション:* 「 If file’s encoding can’t be guessed, use 」の
   「 Unicode (UTF-8, no BOM) 」を選んでください
#. 左側の「 Text Files 」を選択します。
#. 「 Default line breaks 」で「 Mac OS X and Unix (LF) 」を選択してください

PHP の閉じタグ
==============

PHP 文書の PHP 閉じタグ **?>** は PHP パーサにとってオプションです。
しかしながら、使用すると、閉じタグのうしろにあるいくつかの空白文字が、
開発者が入れたものであれ、ユーザによるものであれ、または FTP アプリケーションによるものであれ、
不要な出力、 PHP エラー、または後者が抑制されている場合、
空白ページを引き起こす可能性があります。このため、すべての PHP ファイルは PHP 閉じタグを　省　略　し　な　け　れ　ば　な　ら　ず　、
代わりに単一の空行で終了します。

ファイルの命名
==============

クラスファイルは Ucfirst のように命名しなければならず、その他のファイル名
(設定、ビュー、一般的なスクリプトなど) はすべて小文字にするべきです。

**誤**::

	somelibrary.php
	someLibrary.php
	SOMELIBRARY.php
	Some_Library.php

	Application_config.php
	Application_Config.php
	applicationConfig.php

**正**::

	Somelibrary.php
	Some_library.php

	applicationconfig.php
	application_config.php

また、クラスファイル名はクラス自体の名前と一致させるべきです。
たとえば、 `Myclass` という名前のクラスがあるなら、そのファイル名は
**Myclass.php** でなければなりません。

クラスとメソッドの命名
======================

クラス名は常に大文字で開始するべきです。複数単語は
アンダースコアで区切られるべきで、キャメル記法ではありません。

**誤**::

	class superclass
	class SuperClass

**正**::

	class Super_class

::

	class Super_class {

		public function __construct()
		{

		}
	}

クラスメソッドは完全に小文字であるべきで、
その機能を明確に示すように名前を付けるべきで、なるべく動詞を含めます。
過度に長く冗長な名前は避けるようにしてください。
複数単語はアンダースコアで区切るべきです。

**誤**::

	function fileproperties()		// 言い表せておらず、アンダースコアで区切ることが求められます
	function fileProperties()		// 言い表せておらず、 キャメルケースを使用しています
	function getfileproperties()		// 良くなりました！　でもまだアンダースコア区切りが抜けています
	function getFileProperties()		// キャメルケースを使っています
	function get_the_file_properties_from_the_file()	// くどいです

**正**::

	function get_file_properties()	// よく言い表せており、アンダースコア区切りで、すべて小文字です

変数名
======

変数命名のガイドラインは、クラスメソッドに使用されるものと非常に類似しています。
変数は小文字のみで、アンダースコア区切り文字を使用し、
適切にその目的や内容を示すよう名前をつけるべきです。
非常に短い、単語になっていない変数は
for() ループの中でイテレータとしてのみを使用されるべきです。

**誤**::

	$j = 'foo';		// 1 文字変数は for() ループの中でだけ使用されるべきです
	$Str			// 大文字を含んでいます
	$bufferedText		// キャメルケースを使っています。また、意味の欠落なく短くできます。
	$groupid		// 複数単語です、アンダースコア区切りが求められます
	$name_of_last_city_used	// 長いです

**正**::

	for ($j = 0; $j < 10; $j++)
	$str
	$buffer
	$group_id
	$last_city

コメント
========

一般的に、コードには豊富にコメントするべきです。
経験の浅いプログラマにコードの流れや意図を説明するのに役立つだけでなく、
数か月先の将来においてあなた自身のコードに立ち戻ったときに
非常に貴重な指針となりえます。コメントに要求されるフォーマットはありませんが、
以下のものが推奨されます。

`DocBlock <http://manual.phpdoc.org/HTMLSmartyConverter/HandS/phpDocumentor/tutorial_phpDocumentor.howto.pkg.html#basics.docblock>`_
形式はクラス、メソッド、およびプロパティの宣言の前にコメントします。それにより
IDE によってピックアップできるようなります::

	/**
	 * スーパークラス
	 *
	 * @package	パッケージ名
	 * @subpackage	サブパッケージ
	 * @category	カテゴリ
	 * @author	作者
	 * @link	http://example.com
	 */
	class Super_class {

::

	/**
	 * XML 向けに文字列をエンコードする
	 *
	 * @param	string	$str	入力文字列
	 * @return	string
	 */
	function xml_encode($str)

::

	/**
	 * クラス操作のデータ
	 *
	 * @var	array
	 */
	public $data = array();

コード内では一行コメントを使用し、
大きなコメントブロックとコードの間には空白行を残してください。

::

	// 改行ごとに分割する
	$parts = explode("\n", $str);

	// 何が起きるか、なぜなのかについてとても詳細に説明する必要のある
	// 長いコメントは複数の一行コメントを使用できます。
	// 幅は意味のあるものとし、一番読みやすい 70 文字前後になるよう
	// 努めてください。永続的な外部リソースへリンクするのを躊躇しないでください。
	// それはより優れた説明をしてくれるでしょう。
	//
	// http://example.com/information_about_something/in_particular/

	$parts = $this->foo($parts);

定数
====

定数は変数と同じガイドラインに従います、
ただし定数は常にすべて大文字にすべきです。 *CodeIgniter の定数、すなわち
SLASH 、 LD 、 RD 、 PATH_CACHE などが割り当てられている場合は常に使用してください。*

**誤**::

	myConstant	// アンダースコア区切りがなく、すべてを大文字にはしていません
	N		// 1 文字定数はだめです
	S_C_VER		// 意味が伝わりません
	$str = str_replace('{foo}', 'bar', $str);	// LD 定数と RD 定数を使うべきです

**正**::

	MY_CONSTANT
	NEWLINE
	SUPER_CLASS_VERSION
	$str = str_replace(LD.'foo'.RD, 'bar', $str);

TRUE 、 FALSE および NULL
=========================

**TRUE** 、 **FALSE** および **NULL** キーワードは常にすべて
大文字にすべきです。

**誤**::

	if ($foo == true)
	$bar = false;
	function foo($bar = null)

**正**::

	if ($foo == TRUE)
	$bar = FALSE;
	function foo($bar = NULL)

論理演算子
==========

``||`` 「論理和」比較演算子の使用はやめましょう。いくつかの出力デバイス上で不明確だからです
(例として、数字の 11 のように見えます) 。
``&&`` は ``AND`` よりも好ましいですが、どちらとも許容されます。
``!`` の前後には空白文字をいれるべきです。

**誤**::

	if ($foo || $bar)
	if ($foo AND $bar)  // 問題ありませんが推奨されません、一般的なシンタックスハイライトアプリのためです
	if (!$foo)
	if (! is_array($foo))

**正**::

	if ($foo OR $bar)
	if ($foo && $bar) // 推奨です
	if ( ! $foo)
	if ( ! is_array($foo))
	

返り値の比較と型キャスト
========================

いくつかの PHP 関数は失敗した場合に FALSE を返しますが、正当な返り値として
"" または 0 を返します。これらは緩やかな比較で FALSE
と評価されます。条件式においてはそれらの返り値を使用する際、変数型の比較によって明確にします。
返り値が確かに期待するものであることを確認するためです。
そして緩やかな型比較評価において等しくなる値によっては
比較しません。

あなた独自の変数を返す際と確認する際には、同じ厳格さを用いてください。
必要に応じて **===** と **!==** を使用してください。

**誤**::

	// もし 'foo' が文字列の先頭にあれば、 strpos は 0 を返します。
	// この条件式の評価結果は TRUE となります。
	if (strpos($str, 'foo') == FALSE)

**正**::

	if (strpos($str, 'foo') === FALSE)

**誤**::

	function build_string($str = "")
	{
		if ($str == "")	// おおっと！　引数に FALSE や 整数値 0 が渡されたらどうなりますか？
		{

		}
	}

**正**::

	function build_string($str = "")
	{
		if ($str === "")
		{

		}
	}


`型キャスト
<http://php.net/manual/ja/language.types.type-juggling.php#language.types.typecasting>`_ に関する情報も参照してください。
非常に便利です。型キャストには微妙に異なる効果があり、
それは望ましいものでしょう。文字列として変数をキャストすると、例として、
NULL と真偽値 FALSE は空文字列となり、 0 (および
他の数値) は 10 進数の文字列となり、真偽値 TRUE は「 1 」になります::

	$str = (string) $str; // $str を文字列としてキャストします

デバッグコード
==============

あなたの成果物にデバッグコードを放置しないでください、たとえコメントアウトしてあってもです。
``var_dump()`` 、 ``print_r()`` 、 ``die()``/``exit()`` のようなものはコードに含めないべきです、
デバッグ以外の特定の目的を果たすわけでない限りは。

ファイル内の空白
================

PHP 開始タグの前、もしくは PHP 閉じタグのうしろの空白文字は禁止です。
出力はバッファリングされているので、ファイル内の空白文字は
CodeIgniter がその内容を出力する前に出力を開始する原因となりえるため、エラーにつながり、
CodeIgniter が適切なヘッダを送信することができなくなることにもなりえます。

互換性
======

CodeIgniter では PHP 5.5 以降の使用を推奨していますが、
PHP 5.2.4 と互換性を保つべきです。あなたのコードは次のいずれかでなければなりません。
この要件と互換性を保つか、適切なフォールバックを提供するか、
もしくはユーザのアプリケーションに影響を与えることなく静かに消え去るオプション機能を持つかです。

加えて、デフォルト以外のライブラリをインストールする必要がある PHP
関数を使用してはいけません。
もしくはその関数が使えない場合の代替手段をそのコードに持たせてください。

クラスごとにひとつのファイル
============================

クラスが *密接に関連している* 場合を除き、各クラスには別々のファイルを使用してください。
複数のクラスが含まれている CodeIgniter ファイルの例は、
Xmlrpc ライブラリファイルです。

空白文字
========

コード内では、空白文字にはタブを使用してください。スペースではありません。
これは小さなことのように思えますが、しかしスペースのかわりにタブを使用することで、
あなたのコードを読む開発者は彼らの好みのレベルでインデントすることができ、
彼らがアプリケーションに何を使っていようともカスタマイズできます。
そして副次的な利点として、結果的に (少しだけですが) よりコンパクトなファイルになります。
たとえばスペース文字 4 つの保存に対し、タブなら 1 文字です。

改行
====

ファイルは Unix の改行で保存しなければなりません。これは
Windows で作業する開発者にとって余計な問題ですが、それでも何であれテキストエディタが
Unix の改行でファイルを保存するように設定されていることを確認してください。

コードのインデント
==================

オールマンスタイルのインデントを使用してください。
クラス宣言を除き中括弧は常にそれ用の行に配置され、
それらを「所有」する制御文と同じレベルにインデントされます。

**誤**::

	function foo($bar) {
		// ...
	}

	foreach ($arr as $key => $val) {
		// ...
	}

	if ($foo == $bar) {
		// ...
	} else {
		// ...
	}

	for ($i = 0; $i < 10; $i++)
		{
		for ($j = 0; $j < 10; $j++)
			{
			// ...
			}
		}
		
	try {
		// ...
	}
	catch() {
		// ...
	}

**正**::

	function foo($bar)
	{
		// ...
	}

	foreach ($arr as $key => $val)
	{
		// ...
	}

	if ($foo == $bar)
	{
		// ...
	}
	else
	{
		// ...
	}

	for ($i = 0; $i < 10; $i++)
	{
		for ($j = 0; $j < 10; $j++)
		{
			// ...
		}
	}
	
	try 
	{
		// ...
	}
	catch()
	{
		// ...
	}

角括弧と括弧内の空白文字
========================

原則として、丸括弧と角括弧にはスペースを使用しないべきです。
例外は、丸括弧による引数を受けつける PHP の制御構造
(宣言、 do-while 、
elseif 、 for 、 foreach 、 if 、 switch 、 while) のうしろにはスペースを入れるべきです。
それらと関数を区別する補助とし、読みやすさを向上させるためです。

**誤**::

	$arr[ $foo ] = 'foo';

**正**::

	$arr[$foo] = 'foo'; // 配列のキーの周囲にはスペースを入れません

**誤**::

	function foo ( $bar )
	{

	}

**正**::

	function foo($bar) // 関数宣言の丸括弧の周囲にはスペースを入れません
	{

	}

**誤**::

	foreach( $query->result() as $row )

**正**::

	foreach ($query->result() as $row) // PHP 制御構造のうしろにはスペース 1 つを入れます、しかし丸括弧の内側には入れません

ローカライズされたテキスト
==========================

CodeIgniter のライブラリは可能な限り、
対応する言語ファイルを利用するべきです。

**誤**::

	return "Invalid Selection";

**正**::

	return $this->lang->line('invalid_selection');

プライベートメソッドと変数
==========================

内部からのみアクセスできるメソッドと変数、
コードの抽象化のためにパブリックメソッドが使用するユーティリティやヘルパー関数などは、
アンダースコアをプレフィックスにつけるべきです。

::

	public function convert_text()
	private function _convert_text()

PHPのエラー
===========

コードはエラーなく実行されなければならず、この要件を満たすために warning や notice
を非表示にしてはなりません。例として、自分でセットしていない変数にはまず ``isset()``
で確認するまでは決してアクセスしません (``$_POST``
配列のキーの類です) 。

あなたの dev 環境で　す　べ　て　のユーザーに対してエラー報告が有効になっていることを確認してください。
また PHP 環境で display_errors が有効になっていることを確認してください。
この設定は次のもので確認することができます::

	if (ini_get('display_errors') == 1)
	{
		exit "Enabled";
	}

いくつかのサーバでは *display_errors* は無効になっており、あなたが
php.ini でこれを変更することができない場合は、だいたいの場合次の方法で有効にすることができます::

	ini_set('display_errors', 1);

.. note:: `display_errors
	<http://php.net/manual/en/errorfunc.configuration.php#ini.display-errors>`_
	の ``ini_set()`` による実行時設定は
	PHP 環境で有効にした場合と同一ではありません。すなわち、スクリプトに
	fatal error がある場合には何の意味もありません。

短い形式の開始タグ
==================

PHP の開始タグには常に完全なものを使用します。
*short_open_tag* が有効になっていない場合に備えるためです。

**誤**::

	<? echo $foo; ?>

	<?=$foo?>

**正**::

	<?php echo $foo; ?>

.. note:: PHP 5.4 では常に **<?=** は有効です。

1 行につき 1 つのステートメント
===============================

1 行の中に複数のステートメントは入れません。

**誤**::

	$foo = 'this'; $bar = 'that'; $bat = str_replace($foo, $bar, $bag);

**正**::

	$foo = 'this';
	$bar = 'that';
	$bat = str_replace($foo, $bar, $bag);

文字列
======

変数を展開する必要がない限り、常にシングルクォートを使用してください。
もし変数を展開する必要があっても、トークンを過剰に解釈しすぎないように
中括弧を使用してください。
また、文字列にシングルクォートが含まれている場合はダブルクォート文字列を使ってもよく、
エスケープ文字を使用する必要はありません。

**誤**::

	"My String"					// 変数展開はありませんので、ダブルクォートは使わないでください
	"My string $foo"				// 中括弧が必要です
	'SELECT foo FROM bar WHERE baz = \'bag\''	// 読みにくいです

**正**::

	'My String'
	"My string {$foo}"
	"SELECT foo FROM bar WHERE baz = 'bag'"

SQL クエリ
==========

SQL キーワード常に大文字です: SELECT 、 INSERT 、 UPDATE 、 WHERE 、
AS 、 JOIN 、 ON 、 IN 、 など。

長いクエリは複数行に分割してください、読みやすくするためです。
文節ごとに分割するのが好ましいです。

**誤**::

	// キーワードが小文字になっており、 1 行に納めるにはクエリが長すぎます
	// (... は行が続くことを表しています)
	$query = $this->db->query("select foo, bar, baz, foofoo, foobar as raboof, foobaz from exp_pre_email_addresses
	...where foo != 'oof' and baz != 'zab' order by foobaz limit 5, 100");

**正**::

	$query = $this->db->query("SELECT foo, bar, baz, foofoo, foobar AS raboof, foobaz
					FROM exp_pre_email_addresses
					WHERE foo != 'oof'
					AND baz != 'zab'
					ORDER BY foobaz
					LIMIT 5, 100");

関数の引数のデフォルト値
========================

適切な場合はいつでも、関数の引数にはデフォルト値を提供してください。
呼び出し間違えでの PHP エラーを防ぐことができますし、
数行のコードを省力化するための共通のフォールバック値を提供できます。例::

	function foo($bar = '', $baz = FALSE)