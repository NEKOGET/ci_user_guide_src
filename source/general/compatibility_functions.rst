########
互換関数
########

CodeIgniter では互換関数のセットを提供します。
PHP でネイティブに提供しているものの、より新しいバージョンでのみで対応であったり、
特定のエクステンションに依存するような関数を使えるようにするものです。

独自実装しているため、
これらの関数自身もほかのものに依存する場合もありますが、
それでもまだ PHP 環境がそれらを提供していないままであるよりは便利でしょう。

.. note:: まるで `共通機能 <common_functions>` のように、
	それらの依存関係が満たされているかぎり
	互換関数は常に利用可能です。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

******************
パスワードハッシュ
******************

この互換関数のセットは PHP 標準の
`パスワードハッシュ拡張 <http://php.net/password>`_ の「バックポート」を提供しています。
本来は PHP 5.5 以降で利用可能なものです。

依存関係
========

- PHP 5.3.7
- ``crypt()`` の ``CRYPT_BLOWFISH`` サポート

定数
====

- ``PASSWORD_BCRYPT``
- ``PASSWORD_DEFAULT``

関数リファレンス
================

.. php:function:: password_get_info($hash)

	:param	string	$hash: パスワードのハッシュ
	:returns:	ハッシュされたパスワードの情報
	:rtype:	array

	詳しくは
	`password_get_info() の PHP マニュアル <http://php.net/password_get_info>`_ を参照してください。

.. php:function:: password_hash($password, $algo[, $options = array()])

	:param	string	$password: 平文のパスワード
	:param	int	$algo: ハッシュアルゴリズム
	:param	array	$options: ハッシュオプション
	:returns:	ハッシュされたパスワード、失敗した場合には FALSE
	:rtype:	string

	詳しくは
	`password_hash() の PHP マニュアル <http://php.net/password_hash>`_ を参照してください。

	.. note:: 指定の (有効な) saltを提供しない限り、
		この機能はさらに CSPRNG ソースにも依存します。
		下記のいずれかがそれを満たします:
		- ``mcrypt_create_iv()`` と ``MCRYPT_DEV_URANDOM``
		- ``openssl_random_pseudo_bytes()``
		- /dev/arandom
		- /dev/urandom

.. php:function:: password_needs_rehash()

	:param	string	$hash: パスワードのハッシュ
	:param	int	$algo: ハッシュアルゴリズム
	:param	array	$options: ハッシュオプション
	:returns:	ハッシュが与えられたアルゴリズムとオプションにマッチするよう再ハッシュする必要がある場合は TRUE 、それ以外の場合はFALSE
	:rtype:	bool

	詳しくは
	`password_needs_rehash() の PHP マニュアル <http://php.net/password_needs_rehash>`_ を参照してください。

.. php:function:: password_verify($password, $hash)

	:param	string	$password: 平文のパスワード
	:param	string	$hash: パスワードのハッシュ
	:returns:	パスワードがハッシュと一致した場合は TRUE 、そうでない場合は FALSE
	:rtype:	bool

	詳しくは
	`password_verify() の PHP マニュアル <http://php.net/password_verify>`_ を参照してください。

*********************************
ハッシュ (メッセージダイジェスト)
*********************************

この互換レイヤには ``hash_equals()`` 関数と
``hash_pbkdf2()`` 関数のバックポートが含まれています。本来おのおの PHP 5.6 およびまたは
PHP 5.5 を必要とするものです。

依存関係
========

- なし

関数リファレンス
================

.. php:function:: hash_equals($known_string, $user_string)

	:param	string	$known_string: 既知の文字列
	:param	string	$user_string: ユーザ指定の文字列
	:returns:	文字列が一致する場合 TRUE 、それ以外の場合は FALSE
	:rtype:	string

	詳しくは
	`hash_equals() の PHP マニュアル <http://php.net/hash_equals>`_ を参照してください。

.. php:function:: hash_pbkdf2($algo, $password, $salt, $iterations[, $length = 0[, $raw_output = FALSE]])

	:param	string	$algo: ハッシュアルゴリズム
	:param	string	$password: パスワード
	:param	string	$salt: ハッシュの salt
	:param	int	$iterations: 導出の際に実行する反復回数
	:param	int	$length: 出力文字列の長さ
	:param	bool	$raw_output: -生のバイナリデータを返すかどうか
	:returns:	パスワード派生キー、または失敗した場合に FALSE
	:rtype:	string

	詳しくは
	`hash_pbkdf2() の PHP マニュアル <http://php.net/hash_pbkdf2>`_ を参照してください。

******************
マルチバイト文字列
******************

この互換関数のセットは PHP
の `マルチバイト文字列拡張 <http://php.net/mbstring>`_ を限定的にサポートします。
代替手段が限られているため、わずかな関数だけが用意されています。

.. note:: 文字セットパラメータが省略されている場合、
	``$config['charset']`` が使用されます。

依存関係
========

- `iconv <http://php.net/iconv>`_ 拡張

.. important:: この依存関係は省略可能ですが、これらの関数は常に宣言されます。
	iconv が利用できない場合、対応する非 mbstring
	バージョンにフォールバック　し　ま　す　。

.. important:: 文字セットが供給されている場合、
	それは iconv によりサポートされ、 iconv の認識できる形式でなければなりません。

.. note:: 本来の mbstring 拡張モジュールへの依存がありそれをチェックしたい場合、
	``MB_ENABLED`` 定数を使用します。

関数リファレンス
================

.. php:function:: mb_strlen($str[, $encoding = NULL])

	:param	string	$str: 入力文字列
	:param	string	$encoding: 文字セット
	:returns:	入力文字列の文字数、失敗した場合には FALSE
	:rtype:	string

	詳しくは
	`mb_strlen() の PHP マニュアル <http://php.net/mb_strlen>`_ を参照してください。

.. php:function:: mb_strpos($haystack, $needle[, $offset = 0[, $encoding = NULL]])

	:param	string	$haystack: 調べたい文字列
	:param	string	$needle: haystack の中から探す文字列
	:param	int	$offset: 検索オフセット
	:param	string	$encoding: 文字セット
	:returns:	$needle の見つかった位置、見つからない場合は FALSE
	:rtype:	mixed

	詳しくは
	mb_strpos() の PHP マニュアル <http://php.net/mb_strpos>`_ を参照してください。

.. php:function:: mb_substr($str, $start[, $length = NULL[, $encoding = NULL]])

	:param	string	$str: 入力文字列
	:param	int	$start: 最初の文字の位置
	:param	int	$length: 最大文字数
	:param	string	$encoding: 文字セット
	:returns:	$start と $length で指定された $str の部分文字列、失敗した場合は FALSE
	:rtype:	string

	詳しくは
	`mb_substr() の PHP マニュアル <http://php.net/mb_substr>`_ を参照してください。

********
標準関数
********

この互換関数のセットは、本来新しい
PHP バージョンを必要とする PHP 標準関数のいくらかをサポートします。

依存関係
========

- なし

関数リファレンス
================

.. php:function:: array_column(array $array, $column_key[, $index_key = NULL])

	:param	array	$array: 値を取り出したい配列
	:param	mixed	$column_key: 返したいカラムのキー
	:param	mixed	$index_key: 返す配列のキーに使うカラム
	:returns:	入力配列から抽出した単独のカラムの配列
	:rtype:	array

	詳しくは
	`array_column() の PHP マニュアル <http://php.net/array_column>`_ を参照してください。

.. php:function:: array_replace(array $array1[, ...])

	:param	array	$array1: 要素を置き換えたい配列
	:param	array	...: 置き換える配列 (または複数のそれ)
	:returns:	編集された配列
	:rtype:	array

	詳しくは
	`array_replace() の PHP マニュアル <http://php.net/array_replace>`_ を参照してください。

.. php:function:: array_replace_recursive(array $array1[, ...])

	:param	array	$array1: 要素を置き換えたい配列
	:param	array	...: 引き抜きたい要素を持つ配列 (または複数のそれ)
	:returns:	編集された配列
	:rtype:	array

	詳しくは
	`array_replace_recursive() の PHP マニュアル <http://php.net/array_replace_recursive>`_ を参照してください。

	.. important:: PHP のネイティブ関数だけが無限再帰を検出することができます。
		PHP 5.3 以上でないならば、参照に注意してください！

.. php:function:: hex2bin($data)

	:param	array	$data: 十六進表現のデータ
	:returns:	バイナリ表現にしたデータ
	:rtype:	string

	詳しくは `hex2bin() の PHP マニュアル
	<http://php.net/hex2bin>`_ を参照してください。

.. php:function:: quoted_printable_encode($str)

	:param	string	$str: 入力文字列
	:returns:	8 ビットエンコードされた文字列
	:rtype:	string

	詳しくは
	`quoted_printable_encode() の PHP マニュアル <http://php.net/quoted_printable_encode>`_ を参照してください。