##########
出力クラス
##########

出力クラスはコアクラスであり、主な機能はひとつだけです: 要求元のブラウザに完成した
Web ページを送信します。また、あなたが Web ページの
:doc:`キャッシュ機能 <../general/caching>` を使用する場合は、
それを処理する責任も持ちます。

.. note:: このクラスはシステムによって自動的に初期化されますので、
	手動で行う必要はありません。

通常の状況ではあなたの操作なしに透過的に動作しますので、
出力クラスを意識することはありません。たとえば、
ビューファイルをロードするために :doc:`Loader <../libraries/loader>` クラスを使うとき、
自動的に出力クラスに渡されますが、 CodeIgniter
によりシステム実行の終了時に自動的に呼び出されます。
しかしながら、必要がある場合、
手動で出力を操作することは可能です。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

******************
クラスリファレンス
******************

.. php:class:: CI_Output

	.. attribute:: $parse_exec_vars = TRUE;

		{elapsed_time} と {memory_usage} 擬似変数の構文解析を有効/無効にします。

		CodeIgniterはデフォルトで出力中のこれらのトークンを解析します。
		これを無効にするには、コントローラでこのプロパティを FALSE に設定します。
		::

			$this->output->parse_exec_vars = FALSE;

	.. php:method:: set_output($output)

		:param	string	$output: 出力へ設定する文字列
		:returns:	CI_Output インスタンス (メソッドチェイン)
		:rtype:	CI_Output

		手動で最終的な出力文字列を設定することを可能にします。使用例::

			$this->output->set_output($data);

		.. important:: 手動で出力を設定する場合、それは関数呼び出し元で行う最後のものでなければなりません。
			たとえば、あなたのコントローラメソッドのいずれかでページを作成した場合、
			最後まで出力を設定しないでください。

	.. php:method:: set_content_type($mime_type[, $charset = NULL])

		:param	string	$mime_type: MIME タイプ文字列
		:param	string	$charset: 文字セット
		:returns:	CI_Output インスタンス (メソッドチェイン)
		:rtype:	CI_Output

		ページの MIME タイプを設定することを可能にします。これにより JSON データ、 JPEG 、 XML などの提供が簡単になります。
		::

			$this->output
				->set_content_type('application/json')
				->set_output(json_encode(array('foo' => 'bar')));

			$this->output
				->set_content_type('jpeg') // ".jpeg" も使えます。ピリオドは config/mimes.php を探す前に削除されます
				->set_output(file_get_contents('files/something.jpg'));

		.. important:: 非MIME文字列をこのメソッドに渡す場合は
			*application/config/mimes.php* に存在することを確認してください。ない場合は効果はありません。

		また、第 2 引数を渡すことでドキュメントの文字セットを設定することができます::

			$this->output->set_content_type('css', 'utf-8');

	.. php:method:: get_content_type()

		:returns:	Content-Type の文字列
		:rtype:	string

		現在使用中の Content-Type HTTP ヘッダを返します。ただし文字セット値は除きます。
		::

			$mime = $this->output->get_content_type();

		.. note:: 設定されていない場合、デフォルトの戻り値は 'text/html' です。

	.. php:method:: get_header($header)

		:param	string	$header: HTTP ヘッダ名
		:returns:	HTTP レスポンスヘッダ、見つからない場合は NULL
		:rtype:	mixed

		指定の HTTP ヘッダ値を返します。指定のヘッダが設定されていない場合は NULL を返します。
		例::

			$this->output->set_content_type('text/plain', 'UTF-8');
			echo $this->output->get_header('content-type');
			// 出力: text/plain; charset=utf-8

		.. note:: ヘッダ名は大文字小文字を区別せずに比較されます。

		.. note:: PHP 標準の ``header()`` 関数を使用して送信される生のヘッダも検出対象です。

	.. php:method:: get_output()

		:returns:	出力文字列
		:rtype:	string

		出力クラスに格納された出力を手動で取得することができます。
		使用例::

			$string = $this->output->get_output();

		``$this->load->view()`` などの
		CodeIgniter の機能により出力クラスに送られたデータのみが
		この関数から取得できることに注意してください。

	.. php:method:: append_output($output)

		:param	string	$output: 追加の出力データ
		:returns:	CI_Output インスタンス (メソッドチェイン)
		:rtype:	CI_Output

		出力文字列にデータを追加します。
		::

			$this->output->append_output($data);

	.. php:method:: set_header($header[, $replace = TRUE])

		:param	string	$header: HTTP レスポンスヘッダ
		:param	bool	$replace: すでに設定済みの場合、古いヘッダの値を置換するかどうか
		:returns:	CI_Output インスタンス (メソッドチェイン)
		:rtype:	CI_Output

		手動でサーバのヘッダを設定することができます。
		ヘッダは最終的にレンダリング表示を出力する際、出力クラスが送信します。例::

			$this->output->set_header('HTTP/1.0 200 OK');
			$this->output->set_header('HTTP/1.1 200 OK');
			$this->output->set_header('Last-Modified: '.gmdate('D, d M Y H:i:s', $last_update).' GMT');
			$this->output->set_header('Cache-Control: no-store, no-cache, must-revalidate');
			$this->output->set_header('Cache-Control: post-check=0, pre-check=0');
			$this->output->set_header('Pragma: no-cache');

	.. php:method:: set_status_header([$code = 200[, $text = '']])

		:param	int	$code: HTTP ステータスコード
		:param	string	$text: オプションのメッセージ
		:returns:	CI_Output インスタンス (メソッドチェイン)
		:rtype:	CI_Output

		サーバステータスヘッダを手動で設定することができます。例::

			$this->output->set_status_header(401);
			// ヘッダはこれに設定されます:  Unauthorized

		ヘッダの完全なリストについては `ここを参照してください <http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html>`_ 。

		.. note:: このメソッドは :doc:`共通関数 <../general/common_functions>` の
			:func:`set_status_header()` のエイリアスです。

	.. php:method:: enable_profiler([$val = TRUE])

		:param	bool	$val: プロファイラを有効または無効にするかどうか
		:returns:	CI_Output インスタンス (メソッドチェイン)
		:rtype:	CI_Output

		:doc:`プロファイラ <../general/profiling>` を有効/無効にすることができます。
		プロファイラはデバッグと最適化を目的として、ベンチマークとその他の値をページの下部に表示します。

		プロファイラを有効にするには
		:doc:`コントローラ <../general/controllers>` メソッド内の任意の場所につぎの行を追加します::

			$this->output->enable_profiler(TRUE);

		有効にするとレポートが生成され、ページの下部に挿入されます。

		プロファイラを無効にするには次のようにします::

			$this->output->enable_profiler(FALSE);

	.. php:method:: set_profiler_sections($sections)

		:param	array	$sections: プロファイラセクション
		:returns:	CI_Output インスタンス (メソッドチェイン)
		:rtype:	CI_Output

		プロファイラが有効になっているとき、特定のセクションを有効/無効にすることができます。
		詳細は :doc:`プロファイラ<../general/profiling>` ドキュメントを参照してください。

	.. php:method:: cache($time)

		:param	int	$time: 秒単位でキャッシュの有効期限
		:returns:	CI_Output インスタンス (メソッドチェイン)
		:rtype:	CI_Output

		指定された秒数で現在のページをキャッシュします。

		詳細は :doc:`キャッシングのドキュメント <../general/caching>` を参照してください。

	.. php:method:: _display([$output = ''])

		:param	string	$output: 上書きする出力データ
		:returns:	void
		:rtype:	void

		いくつかのサーバヘッダとともにブラウザに最終出力データを送信します。
		また、ベンチマークタイマを停止します。

		.. note:: このメソッドはスクリプト実行の終了時に自動的に呼び出されるので、
			``exit()`` または ``die()`` を使用してスクリプト実行を中止しない限り、手動で呼び出す必要はありません。
		
		例::

			$response = array('status' => 'OK');

			$this->output
				->set_status_header(200)
				->set_content_type('application/json', 'utf-8')
				->set_output(json_encode($response, JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE | JSON_UNESCAPED_SLASHES))
				->_display();
			exit;

		.. note:: スクリプト実行を中断することなく手動でこのメソッドを呼び出すと、出力が重複します。
