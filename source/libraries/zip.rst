##################
Zip圧縮クラス
##################

CodeIgniter's Zip Encoding Class permits you to create Zip archives.
Archives can be downloaded to your desktop or saved to a directory.

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

****************************
Zip圧縮クラスを使用する
****************************

クラスの初期化
======================

CodeIgniterの大部分のクラスと同様に、Zip クラスはコントローラ
の中で $this->load->library メソッドを使って初期化できます:

	$this->load->library('zip');

いったん読み込まれれば、Zipライブラリのオブジェクトは次のようにして利用できます:

	$this->zip

使用例
=============

以下は、ファイルを圧縮し、サーバ上のフォルダに保存し、デスクトップに
ダウンロードさせる方法を示した例です。

::

	$name = 'mydata1.txt';
	$data = 'A Data String!';

	$this->zip->add_data($name, $data);

	// Zipファイルをサーバのフォルダに書き込みます。"my_backup.zip" という名前にします。
	$this->zip->archive('/path/to/directory/my_backup.zip');

	// "my_backup.zip" という名前で、デスクトップにファイルをダウンロードします。
	$this->zip->download('my_backup.zip');

***************
クラスリファレンス
***************

.. php:class:: CI_Zip

	.. attribute:: $compression_level = 2

		圧縮レベルを使用します。

		効果的に圧縮を抑制している0から最も高圧縮な9までの範囲あります。::

			$this->zip->compression_level = 0;

	.. php:method:: add_data($filepath[, $data = NULL])

		:パラメータ	mixed	$filepath: 一つのファイル経路または多数のファイル => 配列
		:パラメータ	array	$data: ファイルのデータ ($filepath が配列であれば無視されます)
		:返り値型:	void

		Zipアーカイブにデータを追加できます。単独と複数のファイルモードで動作します。

		単独のファイルを追加するとき、第1引数はファイルに付けたい名前を指定し、
		第2引数にはファイルのファイル内容を指定します。::

			$name = 'mydata1.txt';
			$data = 'A Data String!';
			$this->zip->add_data($name, $data);

			$name = 'mydata2.txt';
			$data = 'Another Data String!';
			$this->zip->add_data($name, $data);

		複数のファイルを加えるとき、最初のパラメータは　ファイル名=>内容　の配列で渡します。
		第2のパラメータは無視されます::

			$data = array(
				'mydata1.txt' => 'A Data String!',
				'mydata2.txt' => 'Another Data String!'
			);

			$this->zip->add_data($data);

		圧縮データをサブフォルダの中に配置したい場合は、下記のようにファイル名の一部に
		パスを含めてください::

			$name = 'personal/my_bio.txt';
			$data = 'I was born in an elevator...';

			$this->zip->add_data($name, $data);

		上の例では、 personal というフォルダの中に my_bio.txt が配置されます。 

	.. php:method:: add_dir($directory)

		:パラメータ	mixed	$directory: ディレクトリ名　文字列　または　配列
		:返り値型:	void

		ディレクトリを追加できます。$this->zip->add_data() を使った時にデータをフォルダに追加
		できるので、通常はこのメソッドは必要ないですが、空のフォルダを作成したい場合は、
		そうすることもできます。:

			$this->zip->add_dir('myfolder'); // "myfolder"というフォルダを作成します

	.. php:method:: read_file($path[, $archive_filepath = FALSE])

		:パラメータ	string	$path: ファイルのパス
		:パラメータ	mixed	$archive_filepath: 新ファイル名/パス (string) または (boolean) オリジナルのファイルパス
		:返り値:	維持したい場合　TRUE , 維持しない場合　FALSE
		:返り値型:	bool

		サーバ上に既に存在しているフォルダ (およびその中身) を圧縮できます。ディレクトまでのパスを指定すると、Zipクラスは、再帰的にその
		フォルダを読み込み、Zipファイルとして再作成します。指定されたパスに含まれるサブフォルダの配下にあるものも含めてすべてのファイルが圧縮されます。

			$path = '/path/to/photo.jpg';

			$this->zip->read_file($path);

			// "my_backup.zip"という名前でデスクトップにダウンロードさせます。
			$this->zip->download('my_backup.zip');

		Zip アーカイブで、そのファイルが含まれるディレクトリ構造を維持したい場合は、
		第2引数に TRUE (ブール値) を指定します。例:

			$path = '/path/to/photo.jpg';

			$this->zip->read_file($path, TRUE);

			// "my_backup.zip"という名前でデスクトップにダウンロードさせます。
			$this->zip->download('my_backup.zip');

		上の例では、photo.jpg が次の2つのフォルダの配下に置かれます。: path/to/

		更なるファイルの新しい名前（含まれるパス）を自動的にその場で指定することもできます:

			$path = '/path/to/photo.jpg';
			$new_path = '/new/path/some_photo.jpg';

			$this->zip->read_file($path, $new_path);

			// 新しいパスを含んだZIPアーカイブをダウンロードさせます。: /new/path/some_photo.jpg
			$this->zip->download('my_archive.zip');

	.. php:method:: read_dir($path[, $preserve_filepath = TRUE[, $root_path = NULL]])

		:パラメータ	string	$path: ファイルのパス
		:パラメータ	bool	$preserve_filepath: オリジナルのファイルパス
		:パラメータ	string	$root_path: アーカイブディレクトリから除外するパスの一部
		:返り値:	維持したい場合　TRUE, 維持しない場合　FALSE
		:返り値型:	bool

		サーバ上に既に存在しているフォルダ (およびその中身) を圧縮できます。ディレクトまでのパスを指定すると、
		Zipクラスは、再帰的にそのフォルダを読み込み、Zipファイルとして再作成します。指定されたパスに含まれる
		サブフォルダの配下にあるものも含めてすべてのファイルが圧縮されます。例:

			$path = '/path/to/your/directory/';

			$this->zip->read_dir($path);

			// "my_backup.zip"という名前でデスクトップにダウンロードさせます。
			$this->zip->download('my_backup.zip');

		デフォルトでは Zip アーカイブには第1引数に指定されたすべてのディレクトリが含まれます。
		もし、指定フォルダまでのパスを無視したい場合は、 第2引数に FALSE (ブール値)
		を指定してください。例::

			$path = '/path/to/your/directory/';

			$this->zip->read_dir($path, FALSE);

		これは、"directory" フォルダの中身を含んだ ZIP を作成します。すべてのサブフォルダは
		正しく保存されますが、/path/to/your は含まれません。

	.. php:method:: archive($filepath)

		:パラメータ	string	$filepath: パスはZIPアーカイブを対象とします。
		:返り値:	成功するとTRUE, 失敗するとFALSE を返します。
		:返り値型:	bool

		Zip 圧縮ファイルをサーバ上のディレクトリに書き込みます。ファイル名で終わる正しいサーバのパスを
		渡します。 ディレクトリが書き込み可能(755であれば通常は大丈夫です)かどうかを確かめてください。
		例:

			$this->zip->archive('/path/to/folder/myarchive.zip'); // myarchive.zip という名前のファイルを作成します。

	.. php:method:: download($filename = 'backup.zip')

		:パラメータ	string	$filename: アーカイブ　ファイル名
		:返り値型:	void

		サーバから Zipファイルをダウンロードさせます。このメソッドは、
		Zipファイルにつけたい名前を指定する必要があります。 例:

			$this->zip->download('latest_stuff.zip'); //"latest_stuff.zip" という名前になります。

		.. note::  このメソッドを使用するときは、ダウンロードさせ、バイナリとして
			データを取り扱わせるための様々なヘッダを送信する必要があるので、 
			コントローラでどんなデータも表示させないで下さい。

	.. php:method:: get_zip()

		:パラメータ:	ZIPファイル実体
		:返り値型:	string

		Zip 圧縮データを返します。データを使って何か特別な事をしない限りは、通常はこのメソッドは
		必要ではありません。 例:

			$name = 'my_bio.txt';
			$data = 'I was born in an elevator...';

			$this->zip->add_data($name, $data);

			$zip_file = $this->zip->get_zip();

	.. php:method:: clear_data()

		:返り値型:	void

		Zip クラスは、上のメソッドを使うたびにZipアーカイブを再圧縮しなくて済むように、Zipデータを
		キャッシュします。 しかし、それぞれ異なるデータの複数のZipを作成する場合、それらのメソッド
		を呼び出す間で、キャッシュをクリアすることができます。例:

			$name = 'my_bio.txt';
			$data = 'I was born in an elevator...';

			$this->zip->add_data($name, $data);
			$zip_file = $this->zip->get_zip();

			$this->zip->clear_data();

			$name = 'photo.jpg';
			$this->zip->read_file("/path/to/photo.jpg"); // ファイルを読み込みます

			$this->zip->download('myphotos.zip');
