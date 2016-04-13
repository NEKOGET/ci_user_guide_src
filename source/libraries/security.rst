##################
セキュリティクラス
##################

セキュリティクラスには、セキュリティのために入力データを処理し、
安全なアプリケーションを作成するのに役立つメソッドが含まれています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

******************
XSS フィルタリング
******************

CodeIgniter にはクロスサイトスクリプティング防止フィルタが付属しています。
これはクッキーをハイジャックしようするなど悪意のあることを行う
JavaScript やその他のコードのうち、一般的に使われる技術のものを探し出します。
許可されていないものが検出された場合、
データを文字エンティティに変換することによって安全にレンダリングされます。

XSSフィルタを通してデータをフィルタリングするには、 ``xss_clean()`` メソッドを使用します::

	$data = $this->security->xss_clean($data);

オプションの第 2 引数、 *is_image* は、このメソッドを画像ファイルを
XSS 攻撃に使っている可能性をテストするのに使えます。
これはファイルアップロードに便利です。この第 2 引数が TRUE
に設定されている場合、返り値として変換した文字列を返すかわりに、画像が安全な場合に TRUE
を、ブラウザが実行するかもしれない潜在的に悪意のある情報を含む場合には FALSE
を返します。

::

	if ($this->security->xss_clean($file, TRUE) === FALSE)
	{
		// ファイルは XSS テストに不合格でした
	}

*****************************************
クロスサイトリクエストフォージェリ (CSRF)
*****************************************

次のように **application/config/config.php**
ファイルを変更することにより、 CSRF 保護を有効にすることができます::

	$config['csrf_protection'] = TRUE;

:doc:`フォームヘルパー <../helpers/form_helper>` を使用している場合、
:func:`form_open()` は自動的にフォームに CSRF 隠れフィールドを挿入します。
そうでない場合、 ``get_csrf_token_name()`` と
``get_csrf_hash()`` を使用することができます。
::

	$csrf = array(
		'name' => $this->security->get_csrf_token_name(),
		'hash' => $this->security->get_csrf_hash()
	);

	...

	<input type="hidden" name="<?=$csrf['name'];?>" value="<?=$csrf['hash'];?>" />

トークンは、すべてのサブミット時に再生成する (デフォルト) か、
CSRF クッキーの生存期間は同一の値で維持するかのどちらかになります。
デフォルトであるトークン再生成はより厳格なセキュリティを提供しますが、
他のトークンが無効になることでユーザビリティの問題をもたらす可能性があります
(戻る/進むナビゲーション、複数のタブ/ウィンドウ、非同期アクションなど) 。
次の config パラメータを編集することによって、この動作を変更することができます。

::

	$config['csrf_regenerate'] = TRUE;

指定の URI を CSRF 保護からホワイトリストに登録することができます
(例えば外部からコンテンツを POST されることを期待する API エンドポイント) 。
「 csrf_exclude_uris 」configパラメータを編集することで、これらの URI を追加することができます::

	$config['csrf_exclude_uris'] = array('api/person/add');

正規表現もサポートされています (大文字小文字を区別しません)::

	$config['csrf_exclude_uris'] = array(
		'api/record/[0-9]+',
		'api/title/[a-z]+'
	);

******************
クラスリファレンス
******************

.. php:class:: CI_Security

	.. php:method:: xss_clean($str[, $is_image = FALSE])

		:param	mixed	$str: 入力文字列または文字列の配列
		:returns:	XSS クリーンなデータ
		:rtype:	mixed

		入力データから XSS 攻撃コードの削除を実施し、クリーンにした文字列を返します。
		オプションの第 2 引数を true に設定すると、画像を使用しても安全な場合は TRUE 、悪意のあるデータが検出された場合は FALSE となる真偽値を返します。

	.. php:method:: sanitize_filename($str[, $relative_path = FALSE])

		:param	string	$str: ファイル名/パス
		:param	bool	$relative_path: ファイルパスのどのディレクトリも保護するかどうか
		:returns:	サニタイズされたファイル名/パス
		:rtype:	string

		ディレクトリトラバーサルやその他セキュリティの脅威を防止するためにファイル名のサニタイズを実施します。
		ユーザ入力により与えられたファイル名には特に有用です。
		::

			$filename = $this->security->sanitize_filename($this->input->post('filename'));

		相対パスを含めてユーザ入力を許可したい場合、たとえば
		*file/in/some/approved/folder.txt* 、第 2 引数の ``$relative_path``  を TRUE に設定することでできます
		::

			$filename = $this->security->sanitize_filename($this->input->post('filename'), TRUE);

	.. php:method:: get_csrf_token_name()

		:returns:	CSRF トークン名
		:rtype:	string

		Returns the CSRF トークン名 (``$config['csrf_token_name']`` の値) 。

	.. php:method:: get_csrf_hash()

		:returns:	CSRF ハッシュ
		:rtype:	string

		CSRFのハッシュ値を返します。手動でフォームを作成するか、正当な AJAX POST リクエストを送信するために ``get_csrf_token_name()``
		と組み合わせで使うと便利です。

	.. php:method:: entity_decode($str[, $charset = NULL])

		:param	string	$str: 入力文字列
		:param	string	$charset: 入力文字列の文字セット
		:returns:	エンティティデコードされた文字列
		:rtype:	string

		このメソッドは PHP が標準で持っている ``html_entity_decode()`` 関数を ENT_COMPAT モードで動かした場合の挙動に非常によく似ています、
		セミコロンで終っていない HTML エンティティを検出しようとする以外は。いくつかのブラウザがそれを許可するからです。

		``$charset`` パラメータを空のままである場合は、 ``$config['charset']`` に設定した値が使用されます。

	.. php:method:: get_random_bytes($length)

		:param	int	$length: 出力の長さ
		:returns:	ランダムバイトのバイナリストリーム。失敗した場合には FALSE
		:rtype:	string

		``mcrypt_create_iv()`` により適切なランダムバイトを取得するための便利なメソッドです、
		``/dev/urandom`` か ``openssl_random_pseudo_bytes()`` のどちらかが有効な場合に (この順序で)
		取得します。

		CSRF と XSS のトークンを生成するために使用されます。

		.. note:: 出力は暗号化手法として安全であるという保証は　あ　り　ま　せ　ん　、
			その時においてベストな試行というだけです。