###########
Email クラス
###########

CodeIgniter の堅牢な Email クラスは、次のような機能に対応しています:

-  複数プロトコル: メール、Sendmail、および SMTP
-  SMTPのための暗号化　TLS および SSL 
-  複数の受取人
-  CC と BCC
-  HTML または プレーンテキスト email
-  添付
-  ワードラップ
-  優先度
-  BCC バッチモード。これは、大きなメールリストを小さな 
   BCC バッチに分割します。
-  Email デバッグツール

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***********************
Email クラスを使う
***********************

Email の送信
=============

メールの送信は単純なだけでなく、送信する直前にも設定ファイル
でも、メール送信の設定ができます。

以下は、どうやってメールを送信できるかを示した基本的な例です。 Note: 
この例では、:doc:`コントローラ  <../general/controllers>`
でメールを送信すると仮定しています。

::

	$this->load->library('email');

	$this->email->from('your@example.com', 'Your Name');
	$this->email->to('someone@example.com');
	$this->email->cc('another@another-example.com');
	$this->email->bcc('them@their-example.com');

	$this->email->subject('Email Test');
	$this->email->message('Testing the email class.');

	$this->email->send();

Email のオプションを設定する
=========================

メールの送信方法をカスタマイズできる21の設定項目が
利用可能です。以下で述べるように手動で設定することも
できますし、後述するように、設定ファイルに保管された
項目から自動設定することもできます:

設定項目は、email クラスの initialize メソッドに設定項目の配列を渡す
ことでセットすることができます。以下は、設定項目をどのようにセット
できるかの例です:

	$config['protocol'] = 'sendmail';
	$config['mailpath'] = '/usr/sbin/sendmail';
	$config['charset'] = 'iso-8859-1';
	$config['wordwrap'] = TRUE;

	$this->email->initialize($config);

.. note:: ほとんどの設定項目には、設定しなかった場合に使われる
初期値があります。

設定ファイルでEmailの設定を行う
------------------------------------------

設定をするのに、上で述べた方法を使いたくない場合は、代わりに設定
ファイルにその設定を書いておくことができます。 email.php という
名前で新しいファイルをつくり、$config という配列をそのファイル
に書くだけです。 そして、config/email.php にそのファイルを保存
すると、自動的にそれが使われます。 設定ファイルで設定した場合は、
$this->email->initialize() メソッドを使う必要は「ありません」。

Email クラスの設定項目
=================

次のリストは、メールを送信する際にセットできる
設定項目の全リストです。

=================== ====================== ============================ =======================================================================
設定項目            初期値                 選択肢                       説明
=================== ====================== ============================ =======================================================================
**useragent**       CodeIgniter            なし                         ユーザエージェント
**protocol**        mail                   mail, sendmail, または smtp  メールを送信するプロトコル
**mailpath**        /usr/sbin/sendmail     なし                         Sendmail へのパス
**smtp_host**       初期値なし             なし                         SMTP サーバのアドレス
**smtp_user**       初期値なし             なし                         SMTP のユーザ名
**smtp_pass**       初期値なし             なし                         SMTP のパスワード
**smtp_port**       25                     なし                         SMTP のポート番号
**smtp_timeout**    5                      なし                         SMTP のタイムアウト (秒単位)
**smtp_keepalive**  FALSE                  TRUE または FALSE(boolean)   持続的SMTP接続の有効化
**smtp_crypto**     初期値なし             tls または ssl               SMTP暗号化
**wordwrap**        TRUE                   TRUE または FALSE (boolean)  ワードラップの有効化設定
**wrapchars**       76                                                  何番目の文字で折り返すか
**mailtype**        text                   text または html             メールのタイプ。HTML メールを送信すると、メールは完全な Web 
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　ページとして送信されます。このとき、相対リンクや画像への相対
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　パスがないか確かめてください。それらは動作しません。
**charset**         ``$config['charset']``                              文字セット (utf-8、iso-8859-1、など)
**validate**        FALSE                  TRUE または FALSE(boolean)   メールアドレスを検証するかどうか
**priority**        3                      1, 2, 3, 4, 5                メールの優先度。 1 = 最高 5 = 最低 3 = 通常
**crlf**            \\n                    "\\r\\n" or "\\n" or "\\r"   CRLF ("\\r\\n" RFC 822に応じて使用). 
**newline**         \\n                    "\\r\\n" or "\\n" or "\\r"   改行文字 ("\\r\\n" RFC 822に応じて使用). 
**bcc_batch_mode**  FALSE                  TRUE またはFALSE(boolean)    BCC バッチモードを有効にするかどうか
**bcc_batch_size**  200                    なし                         各 BCC バッチで送るメール件数。
**dsn**             FALSE                  TRUE または FALSE (boolean)  サーバーからのメッセージ通知を有効にする
=================== ====================== ============================ =======================================================================

ワードラップ設定の上書き
========================

ワードラップが有効になっている (RFC 822 に従うことを推奨します)
場合 、email に非常に長いリンクがあると折り返されてしまい、受信
した人がクリックできないようになります。 CodeIgniter では、次の
ようにして、メッセージの一部で手動でワードラップ設定を上書きする
ことができます:

	通常通り折り返された
	メールのテキスト。

	{unwrap}http://example.com/a_long_link_that_should_not_be_wrapped.html{/unwrap}

	さらに通常通り折り返
	されたテキスト.


折り返したくない項目を {unwrap} {/unwrap}で挟んでください。

***************
クラスリファレンス
***************

.. class:: CI_Email

	.. メソッド:: from($from[, $name = ''[, $return_path = NULL]])

		:パラメータ	string	$from: "From" メールアドレス
		:パラメータ	string	$name: "From" 表示名
		:パラメータ	string	$return_path: 未配達の電子メールをリダイレクトするオプションのメールアドレス
		:返り値:	CI_Email インスタンス (メソッドチエーン)
		:返り値型:	CI_Email

		電子メール送信者の電子メールアドレスと氏名をセットします::

			$this->email->from('you@example.com', 'あなたの名前');

		送信したメールのリターンパスを決めることもできます。未配達のメールを転送するのを支援します。::

			$this->email->from('you@example.com', 'あなたの名前', 'returned_emails@example.com');

		.. note:: プロトコルとして「SMTP」を設定した場合、
		リターンパスは使用できません。

	.. メソッド:: reply_to($replyto[, $name = ''])

		:パラメータ	string	$replyto: 返信の電子メール・アドレス
		:パラメータ	string	$name: 返信の電子メールアドレス名を示します
		:返り値:	CI_Email インスタンス (メソッドチェイン)
		:返り値型:	CI_Email

		返信先アドレスをセットします。指定しない場合は、"from" メソッド
		で指定されたものが使われます。例:

			$this->email->reply_to('you@example.com', 'あなたの名前');

	.. メソッド:: to($to)

		:パラメータ	mixed	$to: メールアドレス　カンマで区切られた列または配列
		:返り値:	CI_Email インスタンス (メソッドチェイン)
		:返り値型:	CI_Email

		受取人のメールアドレスをセットします(複数可)。次のように、単一のメールアドレス、
		カンマ区切りのリスト、あるいは配列で指定可能です:

			$this->email->to('someone@example.com');

		::

			$this->email->to('one@example.com, two@example.com, three@example.com');

		::

			$this->email->to(
				array('one@example.com', 'two@example.com', 'three@example.com')
			);

	.. メソッド:: cc($cc)

		:パラメータ	mixed	$cc: メールアドレス　カンマで区切られた列または配列
		:返り値:	CI_Email インスタンス (メソッドチェイン)
		:返り値型:	CI_Email

		CC のメールアドレスをセットします(複数可)。 "to" メソッドのように、単一のメールアドレス、
		カンマ区切りのリスト、あるいは配列で指定可能です。

	.. メソッド:: bcc($bcc[, $limit = ''])

		:パラメータ	mixed	$bcc: メールアドレス　カンマで区切られた列または配列
		:パラメータ	int	$limit: バッチ送信する電子メールの最大数
		:返り値:	CI_Email インスタンス (メソッドチェイン)
		:返り値型:	CI_Email

		BCC のメールアドレスをセットします(複数可)。"to" メソッドのように、単一のメールアドレス、
		ンマ区切りのリスト、あるいは配列で指定可能です。

		「$LIMIT」が設定されている場合は、「バッチモード」は、各バッチ
		が指定された「$LIMIT」を超えないと、バッチに電子メールを送信します、
		これが有効になります。

	.. メソッド:: subject($subject)

		:パラメータ	string	$subject: 電子メールの件名
		:返り値:	CI_Email インスタンス (メソッドチェイン)
		:返り値型:	CI_Email

		電子メールの件名をセットします::

			$this->email->subject('This is my subject');

	.. メソッド:: message($body)

		:パラメータ	string	$body: 電子メール本文
		:返り値:	CI_Email インスタンス (メソッドチェイン)
		:返り値型:	CI_Email

		電子メールの本文をセットします::

			$this->email->message('This is my message');

	.. メソッド:: set_alt_message($str)

		:パラメータ	string	$str: 代替のメール本文:
		:返り値:	CI_Email インスタンス (メソッドチェイン)
		:返り値型:	CI_Email

		代替のメール本文をセットします::

			$this->email->set_alt_message('This is the alternative message');

		これは、HTML形式にフォーマットされた電子メールを送信する場合に使用
		できるオプションのメッセージ文字列です。あなたがHTML形式のメールを
		対応していない人々の為、ヘッダ文字列に追加されていないHTMLフォーマ
		ットで代替メッセージを指定することができます。あなた自身のメッセー
		ジを設定しないとCodeIgniterはHTMLメールからメッセージを抽出しタグを
		削除します。

	.. メソッド:: set_header($header, $value)

		:パラメータ	string	$header: ヘッダ名
		:パラメータ	string	$value: ヘッダ内容
		:返り値:	CI_Email インスタンス (メソッドチェイン)
		:返り値型: CI_Email

		電子メールの追加のヘッダーを付加::

			$this->email->set_header('Header1', 'Value1');
			$this->email->set_header('Header2', 'Value2');

	.. メソッド:: clear([$clear_attachments = FALSE])

		:パラメータ	bool	$clear_attachments: 添付ファイルをクリアするかどうか
		:返り値:	CI_Email インスタンス (メソッドチェイン)
		:返り値型: CI_Email

		メールの設定を空状態にします。 このメソッドは、ループの
		各サイクルでデータをリセットしながらメール送信機能を使う
		場合を意図しています。

		::

			foreach ($list as $name => $address)
			{
				$this->email->clear();

				$this->email->to($address);
				$this->email->from('your@example.com');
				$this->email->subject('あなたの情報 '.$name);
				$this->email->message('こんにちは  '.$name.'さん ご要望の情報です。');
				$this->email->send();
			}

		次のように引数に TRUE をセットした場合は、すべての添付も
		解除されます::

			$this->email->clear(TRUE);

	.. メソッド:: send([$auto_clear = TRUE])

		:パラメータ	bool	$auto_clear: 自動的にメッセージデータをクリアするかどうか
		:返り値:	成功時TRUE、失敗した場合FALSE
		:返り値型:	bool

		メール送信メソッド。 条件判断が利用できるよう、送信が成功したか失敗したかに
		基づいてブール値の TRUE か FALSE が返ります::

			if ( ! $this->email->send())
			{
				// エラーを生成します
			}

		要求が成功した場合、このメソッドは自動的にすべてのパラメータをクリアします。
		この動作を停止するにはFALSEを渡します。

		 	if ($this->email->send(FALSE))
		 	{
		 		// パラメータはクリアされません
		 	}

		.. note:: 「print_debugger」を使用するためには、電子メールのパラメータ
		をクリアしないようにする必要があります。

	.. メソッド:: attach($filename[, $disposition = ''[, $newname = NULL[, $mime = '']]])

		:パラメータ	string	$filename: ファイル名
		:パラメータ	string	$disposition: 添付ファイルを「配置」します。ほとんどの電子メールクライアント
		にかかわらず、ここで使用されるMIME仕様の独自の判断を下します。
		https://www.iana.org/assignments/cont-disp/cont-disp.xhtml
		:パラメータ	string	$newname: 電子メールで使用するカスタムファイル名
		:パラメータ	string	$mime: MIMEタイプを使用する (バッファリングされたデータに利用)
		:返り値:	CI_Email インスタンス (メソッドチェイン)
		:返り値型:	CI_Email

		添付ファイルを送信できます。第1引数にファイルのパスとファイル名を指定してください。
		複数ファイルを添付する場合は、複数回メソッドを呼んでください。例えば以下のように
		します::

			$this->email->attach('/path/to/photo1.jpg');
			$this->email->attach('/path/to/photo2.jpg');
			$this->email->attach('/path/to/photo3.jpg');

		デフォルトの設定（添付ファイル）を使用します。それ以外の場合はカスタム配置を使用し、
		二番目のパラメータを空白のままにします::

			$this->email->attach('image.jpg', 'inline');

		また、URLを使用することができます::

			$this->email->attach('http://example.com/filename.pdf');

		カスタムファイル名を使用したい場合は、第三のパラメータを使用することができます::

			$this->email->attach('filename.pdf', 'attachment', 'report.pdf');

		本当の物理的なファイルの代わりに、バッファ文字列を使用する必要がある場合、
		バッファとしての最初のパラメータ、ファイル名としての第3のパラメータとMIME
		タイプとしての第4のパラメータを使うことができます::

			$this->email->attach($buffer, 'attachment', 'report.pdf', 'application/pdf');

	.. メソッド:: attachment_cid($filename)

		:パラメータ	string	$filename: 既存の添付ファイル名
		:返り値:	添付ファイルのContent-ID、見つからない場合はFALSE
		:返り値型:	string
 
		添付ファイルのセットとContent-IDを返し、添付ファイルをHTMLにインライン（写真）埋め込むため有効にします。
		最初のパラメータは、すでに添付されたファイル名でなければなりません。
		::
 
			$filename = '/img/photo1.jpg';
			$this->email->attach($filename);
			foreach ($list as $address)
			{
				$this->email->to($address);
				$cid = $this->email->attach_cid($filename);
				$this->email->message('<img src='cid:". $cid ."' alt="photo1" />');
				$this->email->send();
			}

		.. note:: 一意にするため、それぞれ電子メール用のContent-IDは、再作成する必要があります。

	.. メソッド:: print_debugger([$include = array('headers', 'subject', 'body')])

		:パラメータ	array	$include: メッセージのどの部分を印刷するか
		:返り値:	フォーマットされたデバッグデータ
		:返り値型:	string

		すべてのサーバメッセージ、メールヘッダ、メールメッセージを文字列として返します。
		デバッグに役立ちます。
		
		オプションで、メッセージの一部を印刷するかを指定することができます。
		有効なオプションは以下のとおりです。「ヘッダ」「件名」「本文」

		例::

			// クリアされないように電子メールデータのための順序で送信しているときには、
			// FALSEを渡す必要があります。その場合、print_debugger（）の出力には何も
			// ないでしょう。
			$this->email->send(FALSE);

			// 唯一のメッセージの件名と本文を除く、電子メールのヘッダを出力します
			$this->email->print_debugger(array('headers'));

		.. note:: デフォルトでは、すべての生データが印刷されます。
