##################
Zip圧縮クラス
##################

CodeIgniter のZip圧縮クラスを使うと、Zipアーカイブが作成できます。
アーカイブは、 デスクトップにダウンロードさせたり、ディレクトリ
に保存したりできます。

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

.. class:: CI_Zip

	.. attribute:: $compression_level = 2

		圧縮レベルを使用します。

		効果的に圧縮を抑制している0から最も高圧縮な9までの範囲あります。::

			$this->zip->compression_level = 0;

	.. method:: add_data($filepath[, $data = NULL])

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

	.. method:: add_dir($directory)

		:パラメータ	mixed	$directory: ディレクトリ名　文字列　または　配列
		:返り値型:	void

		ディレクトリを追加できます。$this->zip->add_data() を使った時にデータをフォルダに追加
		できるので、通常はこのメソッドは必要ないですが、空のフォルダを作成したい場合は、
		そうすることもできます。:

			$this->zip->add_dir('myfolder'); // "myfolder"というフォルダを作成します

	.. method:: read_file($path[, $archive_filepath = FALSE])

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

	.. method:: read_dir($path[, $preserve_filepath = TRUE[, $root_path = NULL]])

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

		By default the Zip archive will place all directories listed in the first parameter
		inside the zip. If you want the tree preceding the target directory to be ignored,
		you can pass FALSE (boolean) in the second parameter. Example::

			$path = '/path/to/your/directory/';

			$this->zip->read_dir($path, FALSE);

		This will create a ZIP with a directory named "directory" inside, then all sub-directories
		stored correctly inside that, but will not include the */path/to/your* part of the path.

	.. method:: archive($filepath)

		:param	string	$filepath: Path to target zip archive
		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		Writes the Zip-encoded file to a directory on your server. Submit a valid server path
		ending in the file name. Make sure the directory is writable (755 is usually OK).
		Example::

			$this->zip->archive('/path/to/folder/myarchive.zip'); // Creates a file named myarchive.zip

	.. method:: download($filename = 'backup.zip')

		:param	string	$filename: Archive file name
		:rtype:	void

		Causes the Zip file to be downloaded from your server.
		You must pass the name you would like the zip file called. Example::

			$this->zip->download('latest_stuff.zip'); // File will be named "latest_stuff.zip"

		.. note:: Do not display any data in the controller in which you call
			this method since it sends various server headers that cause the
			download to happen and the file to be treated as binary.

	.. method:: get_zip()

		:returns:	Zip file content
		:rtype:	string

		Returns the Zip-compressed file data. Generally you will not need this method unless you
		want to do something unique with the data. Example::

			$name = 'my_bio.txt';
			$data = 'I was born in an elevator...';

			$this->zip->add_data($name, $data);

			$zip_file = $this->zip->get_zip();

	.. method:: clear_data()

		:rtype:	void

		The Zip class caches your zip data so that it doesn't need to recompile the Zip archive
		for each method you use above. If, however, you need to create multiple Zip archives,
		each with different data, you can clear the cache between calls. Example::

			$name = 'my_bio.txt';
			$data = 'I was born in an elevator...';

			$this->zip->add_data($name, $data);
			$zip_file = $this->zip->get_zip();

			$this->zip->clear_data();

			$name = 'photo.jpg';
			$this->zip->read_file("/path/to/photo.jpg"); // Read the file's contents

			$this->zip->download('myphotos.zip');
