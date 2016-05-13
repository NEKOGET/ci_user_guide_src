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

いくつかの PHP 関数は失敗した場合に FALSE を返しますが、正当な戻り値として
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
<http://php.net/manual/en/language.types.type-juggling.php#language.types.typecasting>`_ に関する情報も参照してください。
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
もしくはユーザーのアプリケーションに影響を与えることなく静かに消え去るオプション機能を持つかです。

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

Code Indenting
==============

Use Allman style indenting. With the exception of Class declarations,
braces are always placed on a line by themselves, and indented at the
same level as the control statement that "owns" them.

**INCORRECT**::

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

**CORRECT**::

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

Bracket and Parenthetic Spacing
===============================

In general, parenthesis and brackets should not use any additional
spaces. The exception is that a space should always follow PHP control
structures that accept arguments with parenthesis (declare, do-while,
elseif, for, foreach, if, switch, while), to help distinguish them from
functions and increase readability.

**INCORRECT**::

	$arr[ $foo ] = 'foo';

**CORRECT**::

	$arr[$foo] = 'foo'; // no spaces around array keys

**INCORRECT**::

	function foo ( $bar )
	{

	}

**CORRECT**::

	function foo($bar) // no spaces around parenthesis in function declarations
	{

	}

**INCORRECT**::

	foreach( $query->result() as $row )

**CORRECT**::

	foreach ($query->result() as $row) // single space following PHP control structures, but not in interior parenthesis

Localized Text
==============

CodeIgniter libraries should take advantage of corresponding language files
whenever possible.

**INCORRECT**::

	return "Invalid Selection";

**CORRECT**::

	return $this->lang->line('invalid_selection');

Private Methods and Variables
=============================

Methods and variables that are only accessed internally,
such as utility and helper functions that your public methods use for
code abstraction, should be prefixed with an underscore.

::

	public function convert_text()
	private function _convert_text()

PHP Errors
==========

Code must run error free and not rely on warnings and notices to be
hidden to meet this requirement. For instance, never access a variable
that you did not set yourself (such as ``$_POST`` array keys) without first
checking to see that it ``isset()``.

Make sure that your dev environment has error reporting enabled
for ALL users, and that display_errors is enabled in the PHP
environment. You can check this setting with::

	if (ini_get('display_errors') == 1)
	{
		exit "Enabled";
	}

On some servers where *display_errors* is disabled, and you do not have
the ability to change this in the php.ini, you can often enable it with::

	ini_set('display_errors', 1);

.. note:: Setting the `display_errors
	<http://php.net/manual/en/errorfunc.configuration.php#ini.display-errors>`_
	setting with ``ini_set()`` at runtime is not identical to having
	it enabled in the PHP environment. Namely, it will not have any
	effect if the script has fatal errors.

Short Open Tags
===============

Always use full PHP opening tags, in case a server does not have
*short_open_tag* enabled.

**INCORRECT**::

	<? echo $foo; ?>

	<?=$foo?>

**CORRECT**::

	<?php echo $foo; ?>

.. note:: PHP 5.4 will always have the **<?=** tag available.

One Statement Per Line
======================

Never combine statements on one line.

**INCORRECT**::

	$foo = 'this'; $bar = 'that'; $bat = str_replace($foo, $bar, $bag);

**CORRECT**::

	$foo = 'this';
	$bar = 'that';
	$bat = str_replace($foo, $bar, $bag);

Strings
=======

Always use single quoted strings unless you need variables parsed, and
in cases where you do need variables parsed, use braces to prevent
greedy token parsing. You may also use double-quoted strings if the
string contains single quotes, so you do not have to use escape
characters.

**INCORRECT**::

	"My String"					// no variable parsing, so no use for double quotes
	"My string $foo"				// needs braces
	'SELECT foo FROM bar WHERE baz = \'bag\''	// ugly

**CORRECT**::

	'My String'
	"My string {$foo}"
	"SELECT foo FROM bar WHERE baz = 'bag'"

SQL Queries
===========

SQL keywords are always capitalized: SELECT, INSERT, UPDATE, WHERE,
AS, JOIN, ON, IN, etc.

Break up long queries into multiple lines for legibility, preferably
breaking for each clause.

**INCORRECT**::

	// keywords are lowercase and query is too long for
	// a single line (... indicates continuation of line)
	$query = $this->db->query("select foo, bar, baz, foofoo, foobar as raboof, foobaz from exp_pre_email_addresses
	...where foo != 'oof' and baz != 'zab' order by foobaz limit 5, 100");

**CORRECT**::

	$query = $this->db->query("SELECT foo, bar, baz, foofoo, foobar AS raboof, foobaz
					FROM exp_pre_email_addresses
					WHERE foo != 'oof'
					AND baz != 'zab'
					ORDER BY foobaz
					LIMIT 5, 100");

Default Function Arguments
==========================

Whenever appropriate, provide function argument defaults, which helps
prevent PHP errors with mistaken calls and provides common fallback
values which can save a few lines of code. Example::

	function foo($bar = '', $baz = FALSE)