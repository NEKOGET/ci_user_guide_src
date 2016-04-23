##########
エラー処理
##########

CodeIgniter では以下に説明する機能を利用して、
アプリケーションにエラーレポーティングを組み込むことができます。
また、そのなかにはエラーロギングクラスがあり、
エラーおよびデバッグメッセージをテキストファイルとして保存できます。

.. note:: デフォルトでは、 CodeIgniter はすべての PHP エラーを表示します。
	開発が完了すれば、この動作を変更したくなることでしょう。
	error_reporting() 関数を
	メインの index.php ファイルの上のほうに見つけ出すことでしょうが、
	エラーレポーティングを無効にしても、エラーがあればログファイルへの書き出しを防ぐことは　で　き　ま　せ　ん　。

CodeIgniter の中のほとんどの仕組みとは異なり、
エラー関数は単純な手続き型インターフェイスで、
アプリケーション全体でグローバルに使用することができます。
このアプローチは、エラーメッセージがクラス/関数のスコープを気にすることなくトリガーすることを可能にします。

CodeIgniter はまた、コアのどこかが
``exit()`` をいつ呼び出そうともステータスコードを返します。この終了ステータスコードは HTTP
ステータスコードから分離されており、他のプロセスへの通知として機能します。
そのプロセスはスクリプトが正常に完了したかどうか、正常終了しなかった場合、
どのような問題により異常終了したかを監視していることでしょう。
これらの値は *application/config/constants.php* で定義されています。
終了ステータスコードは CLI 設定において最も有用であるとともに、
適切なコードを返すことはサーバーソフトウェアがスクリプトとアプリケーションの状態を追跡しつづけることに役立つでしょう。

以下の機能によりエラーを生成できます:

.. php:function:: show_error($message, $status_code, $heading = 'An Error Was Encountered')

	:param	mixed	$message: エラーメッセージ
	:param	int	$status_code: HTTP 応答ステータスコード
	:param	string	$heading: エラーページの見出し
	:rtype:	void

	この関数は渡されたエラーメッセージを表示しますが、
	実行状態に合わせたエラーテンプレートを使用します::

		application/views/errors/html/error_general.php

	または:

		application/views/errors/cli/error_general.php

	引数 ``$status_code`` は HTTP
	ステータスコードがエラーとして送信されるべきかを決定します。 ``$status_code`` が
	100 未満である場合、 HTTP ステータスコードは 500 に設定され、
	終了ステータスコードは ``$status_code + EXIT__AUTO_MIN`` に設定されます。
	その値が ``EXIT__AUTO_MAX`` より大きい場合、
	または ``$status_code`` が 100 以上である場合、終了状態コードは
	``EXIT_ERROR`` に設定されます。
	詳細については *application/config/constants.php* で確認することができます。

.. php:function:: show_404($page = '', $log_error = TRUE)

	:param	string	$page: URI 文字列
	:param	bool	$log_error: エラーをログに記録するかどうか
	:rtype:	void

	この関数は 404 エラーメッセージを表示しますが、
	実行状態に合わせたエラーテンプレートを使用します::

		application/views/errors/html/error_404.php

	または:

		application/views/errors/cli/error_404.php

	この関数は、ファイルパスのページが見つからなかったことを示す文字列が渡されることを期待しています。
	終了ステータスコードは
	``EXIT_UNKNOWN_FILE`` に設定されます。コントローラが見つからない場合、
	CodeIgniter は自動的に 404
	メッセージを表示することに注意してください。

	CodeIgniterは自動的にあらゆる ``show_404()`` 呼び出しをログに記録します。
	オプションの第 2 パラメータを FALSE に設定すると、ログをスキップします。

.. php:function:: log_message($level, $message, $php_error = FALSE)

	:param	string	$level: ログレベル: 「 error 」「 debug 」または「 info 」
	:param	string	$message: ログのメッセージ
	:param	bool	$php_error: ネイティブの PHP エラーメッセージをログに記録するかどうか
	:rtype:	void

	この関数によりログファイルにメッセージを書き込むことができます。
	第 1 引数で 3 つの「レベル」のいずれかを指定する必要があります、
	どの種類のメッセージか (デバッグ、エラー、情報) を示すためです。
	第 2 引数はメッセージそのものです。

	例::

		if ($some_var == '')
		{
			log_message('error', '何々の変数に値がありません。');
		}
		else
		{
			log_message('debug', '何々の変数は正しく設定されました。');
		}

		log_message('info', '何々の変数の目的は何々の値を提供することです。');

	メッセージタイプは 3 つあります:

	#. エラーメッセージ。これらは PHP のエラーやユーザのエラーなど、
	   実際のエラーです。
	#. デバッグメッセージを表示します。これらはデバッグに役立たせるメッセージです。
	   たとえばクラスが初期化された場合など、
	   デバッグ情報としてログに記録することができます。
	#. 情報メッセージ。最も低い優先度のメッセージで、
	   単にいくつかのプロセスに関する情報を提供します。

	.. note:: ログファイルが実際に書き込まれるには、
		*logs/* ディレクトリが書き込み可能でなければなりません。
		また、ロギングの「しきい値」を
		*application/config/config.php* に設定する必要があります。
		たとえば、エラーメッセージだけログに記録され、
		他の 2 つのタイプはいらないかもしれません。0 に設定するとロギングは無効になります。
