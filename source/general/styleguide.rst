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
は使用しては *なりません* 。UTF-16 や UTF-32 とは違い、
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

ファイル命名
============

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

クラスとメソッドのネーミング
============================

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

Constants
=========

Constants follow the same guidelines as do variables, except constants
should always be fully uppercase. *Always use CodeIgniter constants when
appropriate, i.e. SLASH, LD, RD, PATH_CACHE, etc.*

**INCORRECT**::

	myConstant	// missing underscore separator and not fully uppercase
	N		// no single-letter constants
	S_C_VER		// not descriptive
	$str = str_replace('{foo}', 'bar', $str);	// should use LD and RD constants

**CORRECT**::

	MY_CONSTANT
	NEWLINE
	SUPER_CLASS_VERSION
	$str = str_replace(LD.'foo'.RD, 'bar', $str);

TRUE, FALSE, and NULL
=====================

**TRUE**, **FALSE**, and **NULL** keywords should always be fully
uppercase.

**INCORRECT**::

	if ($foo == true)
	$bar = false;
	function foo($bar = null)

**CORRECT**::

	if ($foo == TRUE)
	$bar = FALSE;
	function foo($bar = NULL)

Logical Operators
=================

Use of the ``||`` "or" comparison operator is discouraged, as its clarity
on some output devices is low (looking like the number 11, for instance).
``&&`` is preferred over ``AND`` but either are acceptable, and a space should
always precede and follow ``!``.

**INCORRECT**::

	if ($foo || $bar)
	if ($foo AND $bar)  // okay but not recommended for common syntax highlighting applications
	if (!$foo)
	if (! is_array($foo))

**CORRECT**::

	if ($foo OR $bar)
	if ($foo && $bar) // recommended
	if ( ! $foo)
	if ( ! is_array($foo))
	

Comparing Return Values and Typecasting
=======================================

Some PHP functions return FALSE on failure, but may also have a valid
return value of "" or 0, which would evaluate to FALSE in loose
comparisons. Be explicit by comparing the variable type when using these
return values in conditionals to ensure the return value is indeed what
you expect, and not a value that has an equivalent loose-type
evaluation.

Use the same stringency in returning and checking your own variables.
Use **===** and **!==** as necessary.

**INCORRECT**::

	// If 'foo' is at the beginning of the string, strpos will return a 0,
	// resulting in this conditional evaluating as TRUE
	if (strpos($str, 'foo') == FALSE)

**CORRECT**::

	if (strpos($str, 'foo') === FALSE)

**INCORRECT**::

	function build_string($str = "")
	{
		if ($str == "")	// uh-oh!  What if FALSE or the integer 0 is passed as an argument?
		{

		}
	}

**CORRECT**::

	function build_string($str = "")
	{
		if ($str === "")
		{

		}
	}


See also information regarding `typecasting
<http://php.net/manual/en/language.types.type-juggling.php#language.types.typecasting>`_,
which can be quite useful. Typecasting has a slightly different effect
which may be desirable. When casting a variable as a string, for
instance, NULL and boolean FALSE variables become empty strings, 0 (and
other numbers) become strings of digits, and boolean TRUE becomes "1"::

	$str = (string) $str; // cast $str as a string

Debugging Code
==============

Do not leave debugging code in your submissions, even when commented out.
Things such as ``var_dump()``, ``print_r()``, ``die()``/``exit()`` should not be included
in your code unless it serves a specific purpose other than debugging.

Whitespace in Files
===================

No whitespace can precede the opening PHP tag or follow the closing PHP
tag. Output is buffered, so whitespace in your files can cause output to
begin before CodeIgniter outputs its content, leading to errors and an
inability for CodeIgniter to send proper headers.

Compatibility
=============

CodeIgniter recommends PHP 5.5 or newer to be used, but it should be
compatible with PHP 5.2.4. Your code must either be compatible with this
requirement, provide a suitable fallback, or be an optional feature that
dies quietly without affecting a user's application.

Additionally, do not use PHP functions that require non-default libraries
to be installed unless your code contains an alternative method when the
function is not available.

One File per Class
==================

Use separate files for each class, unless the classes are *closely related*.
An example of a CodeIgniter file that contains multiple classes is the 
Xmlrpc library file.

Whitespace
==========

Use tabs for whitespace in your code, not spaces. This may seem like a
small thing, but using tabs instead of whitespace allows the developer
looking at your code to have indentation at levels that they prefer and
customize in whatever application they use. And as a side benefit, it
results in (slightly) more compact files, storing one tab character
versus, say, four space characters.

Line Breaks
===========

Files must be saved with Unix line breaks. This is more of an issue for
developers who work in Windows, but in any case ensure that your text
editor is setup to save files with Unix line breaks.

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