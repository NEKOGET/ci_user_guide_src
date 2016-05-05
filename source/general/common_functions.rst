########
共通関数
########

CodeIgniter はその動作のために少しの関数を使用しており、
それはグローバルに定義され、どこでも使えます。
ライブラリやヘルパーをロードする必要はありません。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

.. php:function:: is_php($version)

	:param	string	$version: バージョン番号
	:returns:	実行している PHP のバージョンが指定のもの以上なら TRUE 、そうでないなら FALSE
	:rtype:	bool

	使用している PHP のバージョンが与えられたバージョン番号よりも
	大きいかどうかを判定します。

	Example::

		if (is_php('5.3'))
		{
			$str = quoted_printable_encode($str);
		}

	インストールされている PHP のバージョンが与えられたバージョン番号と
	等しいかそれ以上なら真偽値の TRUE を返します。インストールされている PHP
	のバージョンが与えられたバージョン番号よりも低い場合には FALSE を返します。

.. php:function:: is_really_writable($file)

	:param	string	$file: ファイルパス
	:returns:	パスが書き込み可能な場合は TRUE 、そうでない場合は FALSE
	:rtype:	bool

	``is_writable()`` は Windows サーバー上で TRUE を返し、
	OS のレポートとして PHP に読み取り専用属性がマークされていて
	本当に書き込めない場合にのみ FALSE を返します。

	この関数は最初に実際にファイルに書き込もうとすることによって、
	書き込み可能であるかどうかを判定します。一般的に、
	情報を信頼できないプラットフォーム上でのみおすすめします。

	例::

		if (is_really_writable('file.txt'))
		{
			echo "書き込もうと思えば書き込めます";
		}
		else
		{
			echo "ファイルは書き込めません";
		}

	.. note:: より詳しくは `PHP のバグ #54709 <https://bugs.php.net/bug.php?id=54709>`_ も参照してください。

.. php:function:: config_item($key)

	:param	string	$key: コンフィグ項目キー
	:returns:	設定キーの値。見つからない場合はNULL
	:rtype:	mixed

	:doc:`コンフィグライブラリ <../libraries/config>` はコンフィグ情報にアクセスするために推奨される方法ですが、
	単一のキーを取得するためには ``config_item()``
	を使用することもできます。詳細については :doc:`コンフィグライブラリ <../libraries/config>`
	マニュアルを参照してください。

.. :noindex: function:: show_error($message, $status_code[, $heading = 'An Error Was Encountered'])

	:param	mixed	$message: エラーメッセージ
	:param	int	$status_code: HTTP Reponseステータスコード
	:param	string	$heading: エラーページの見出し
	:rtype:	void

	これは関数 ``CI_Exception::show_error()`` を呼び出します。より詳しくは、
	どうぞ :doc:`エラー処理 <errors>` ドキュメントをご覧ください。

.. :noindex: function:: show_404([$page = ''[, $log_error = TRUE]])

	:param	string	$page: URI 文字列
	:param	bool	$log_error: ログに保存するかどうか
	:rtype:	void

	これは関数 ``CI_Exception::show_404()`` を呼び出します。より詳しくは、
	どうぞ :doc:`エラー処理 <errors>` ドキュメントをご覧ください。

.. :noindex: function:: log_message($level, $message)

	:param	string	$level: ログレベル: 「 error 」、「 debug 」または「 info 」
	:param	string	$message: ログに記録するメッセージ
	:rtype:	void

	この関数は ``CI_Log::write_log()`` のエイリアスです。より詳しくは、
	どうぞ :doc:`エラー処理 <errors>` ドキュメントをご覧ください。

.. php:function:: set_status_header($code[, $text = ''])

	:param	int	$code: HTTP Reponse ステータスコード
	:param	string	$text: ステータスコードを設定する際のカスタムメッセージ
	:rtype:	void

	サーバステータスヘッダを手動で設定することを可能にします。例::

		set_status_header(401);
		// ヘッダを設定します:  Unauthorized

	ヘッダの完全なリストについては
	`ここを参照してください <http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html>`_ 。

.. php:function:: remove_invisible_characters($str[, $url_encoded = TRUE])

	:param	string	$str: 入力文字列
	:param	bool	$url_encoded: URL エンコードされた文字を削除するかどうか
	:returns:	サニタイズされた文字列
	:rtype:	string

	この機能は、Java\\0scriptのように、 ASCII 文字の間に NULL
	文字を挿入しないようにします。

	例::

		remove_invisible_characters('Java\\0script');
		// 返り値: 'Javascript'

.. php:function:: html_escape($var)

	:param	mixed	$var: エスケープする変数 (文字列または配列)
	:returns:	HTML エスケープされた文字列 (または文字列の配列)
	:rtype:	mixed

	この関数は PHP ネイティブの ``htmlspecialchars()``
	関数の別名として機能するほか、文字列の配列を処理できるという利点があります。

	これはクロスサイトスクリプティング (XSS) の予防に有用です。

.. php:function:: get_mimes()

	:returns:	ファイルタイプの連想配列
	:rtype:	array

	この関数は *application/config/mimes.php* から MIME の配列への *参照*
	を返します。

.. php:function:: is_https()

	:returns:	現在 HTTP over SSL を使用している場合は TRUE 、そうでないなら FALSE
	:rtype:	bool

	セキュア接続 (HTTPS) を使用していれば TRUE を返し、他の場合には FALSE
	を返します (非 HTTP 要求を含みます) 。

.. php:function:: is_cli()

	:returns:	現在 CLI で実行されている場合は TRUE 、そうでない場合は FALSE
	:rtype:	bool

	アプリケーションがコマンドラインから実行している場合は TRUE を返し、そうでないなら
	FALSE を返します。

	.. note:: この関数は、 ``PHP_SAPI`` 値が「 cli 」であるかどうかと、
		``STDIN`` 定数が定義されているかどうかの両方をチェックします。

.. php:function:: function_usable($function_name)

	:param	string	$function_name: 関数名
	:returns:	関数を使用することができる場合は TRUE 、そうでない場合は FALSE
	:rtype:	bool

	関数が存在し使用可能であれば TRUE を、そうでなければ FALSE を返します。

	この関数は ``function_exists()`` を確認し、また、
	`Suhosin extension <http://www.hardened-php.net/suhosin/>` がロードされていれば
	関数が無効化されていないかどうかをチェックします。

	これは
	``eval()`` や ``exec()`` など、危険であり、制限の厳しいセキュリティポリシーを持つサーバ上で
	無効にされうる関数の可用性をチェックする場合に便利です。

	.. note:: Suhosinはスクリプトの実行を中断させますが、
		これはバグであることが判明したため、この機能が導入されました。
		修正プログラムはすでに用意されましたが (バージョン0.9.34) 、
		残念ながらまだリリースされていません。