#########
FTP クラス
#########

CodeIgniter's FTP Class permits files to be transfered to a remote
server. Remote files can also be moved, renamed, and deleted. The FTP
class also includes a "mirroring" function that permits an entire local
directory to be recreated remotely via FTP.

.. note:: SFTP and SSL FTP protocols are not supported, only standard
	FTP.

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

**************************
FTPクラスの機能
**************************

クラスの初期化
======================

Like most other classes in CodeIgniter, the FTP class is initialized in
your controller using the $this->load->library function::

	$this->load->library('ftp');

Once loaded, the FTP object will be available using: $this->ftp

使用例
==============

In this example a connection is opened to the FTP server, and a local
file is read and uploaded in ASCII mode. The file permissions are set to
755.
::

	$this->load->library('ftp');

	$config['hostname'] = 'ftp.example.com';
	$config['username'] = 'your-username';
	$config['password'] = 'your-password';
	$config['debug']	= TRUE;

	$this->ftp->connect($config);

	$this->ftp->upload('/local/path/to/myfile.html', '/public_html/myfile.html', 'ascii', 0775);

	$this->ftp->close();

In this example a list of files is retrieved from the server.
::

	$this->load->library('ftp');

	$config['hostname'] = 'ftp.example.com';
	$config['username'] = 'your-username';
	$config['password'] = 'your-password';
	$config['debug']	= TRUE;

	$this->ftp->connect($config);

	$list = $this->ftp->list_files('/public_html/');

	print_r($list);

	$this->ftp->close();

In this example a local directory is mirrored on the server.
::

	$this->load->library('ftp');

	$config['hostname'] = 'ftp.example.com';
	$config['username'] = 'your-username';
	$config['password'] = 'your-password';
	$config['debug']	= TRUE;

	$this->ftp->connect($config);

	$this->ftp->mirror('/path/to/myfolder/', '/public_html/myfolder/');

	$this->ftp->close();

***************
クラスリファレンス
***************

.. class:: CI_FTP

	.. method:: connect([$config = array()])

		:パラメータ	array	$config: Connection values
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Connects and logs into to the FTP server. Connection preferences are set
		by passing an array to the function, or you can store them in a config
		file.

		Here is an example showing how you set preferences manually::

			$this->load->library('ftp');

			$config['hostname'] = 'ftp.example.com';
			$config['username'] = 'your-username';
			$config['password'] = 'your-password';
			$config['port']     = 21;
			$config['passive']  = FALSE;
			$config['debug']    = TRUE;

			$this->ftp->connect($config);

		**Setting FTP Preferences in a Config File**

		If you prefer you can store your FTP preferences in a config file.
		Simply create a new file called the ftp.php, add the $config array in
		that file. Then save the file at *application/config/ftp.php* and it
		will be used automatically.

		**Available connection options**

		============== =============== =============================================================================
		Option name    Default value   Description
		============== =============== =============================================================================
		**hostname**   n/a             FTP hostname (usually something like: ftp.example.com)
		**username**   n/a             FTP username
		**password**   n/a             FTP password
		**port**       21              FTP server port number
		**debug**      FALSE           TRUE/FALSE (boolean): Whether to enable debugging to display error messages
		**passive**    TRUE            TRUE/FALSE (boolean): Whether to use passive mode
		============== =============== =============================================================================

	.. method:: upload($locpath, $rempath[, $mode = 'auto'[, $permissions = NULL]])

		:パラメータ	string	$locpath: Local file path
		:パラメータ	string	$rempath: Remote file path
		:パラメータ	string	$mode: FTP mode, defaults to 'auto' (options are: 'auto', 'binary', 'ascii')
		:パラメータ	int	$permissions: File permissions (octal)
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Uploads a file to your server. You must supply the local path and the
		remote path, and you can optionally set the mode and permissions.
		Example::

			$this->ftp->upload('/local/path/to/myfile.html', '/public_html/myfile.html', 'ascii', 0775);

		If 'auto' mode is used it will base the mode on the file extension of the source file.

		If set, permissions have to be passed as an octal value.

	.. method:: download($rempath, $locpath[, $mode = 'auto'])

		:パラメータ	string	$rempath: Remote file path
		:パラメータ	string	$locpath: Local file path
		:パラメータ	string	$mode: FTP mode, defaults to 'auto' (options are: 'auto', 'binary', 'ascii')
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Downloads a file from your server. You must supply the remote path and
		the local path, and you can optionally set the mode. Example::

			$this->ftp->download('/public_html/myfile.html', '/local/path/to/myfile.html', 'ascii');

		If 'auto' mode is used it will base the mode on the file extension of the source file.

		Returns FALSE if the download does not execute successfully
		(including if PHP does not have permission to write the local file).

	.. method:: rename($old_file, $new_file[, $move = FALSE])

		:パラメータ	string	$old_file: Old file name
		:パラメータ	string	$new_file: New file name
		:パラメータ	bool	$move: Whether a move is being performed
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Permits you to rename a file. Supply the source file name/path and the new file name/path.
		::

			// Renames green.html to blue.html
			$this->ftp->rename('/public_html/foo/green.html', '/public_html/foo/blue.html');

	.. method:: move($old_file, $new_file)

		:パラメータ	string	$old_file: Old file name
		:パラメータ	string	$new_file: New file name
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Lets you move a file. Supply the source and destination paths::

			// Moves blog.html from "joe" to "fred"
			$this->ftp->move('/public_html/joe/blog.html', '/public_html/fred/blog.html');

		.. note:: If the destination file name is different the file will be renamed.

	.. method:: delete_file($filepath)

		:パラメータ	string	$filepath: Path to file to delete
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Lets you delete a file. Supply the source path with the file name.
		::

			 $this->ftp->delete_file('/public_html/joe/blog.html');

	.. method:: delete_dir($filepath)

		:パラメータ	string	$filepath: Path to directory to delete
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Lets you delete a directory and everything it contains. Supply the
		source path to the directory with a trailing slash.

		.. important:: Be VERY careful with this method!
			It will recursively delete **everything** within the supplied path,
			including sub-folders and all files. Make absolutely sure your path
			is correct. Try using ``list_files()`` first to verify that your path is correct.

		::

			 $this->ftp->delete_dir('/public_html/path/to/folder/');

	.. method:: list_files([$path = '.'])

		:パラメータ	string	$path: Directory path
		:返り値:	An array list of files or FALSE on failure
		:返り値型:	array

		Permits you to retrieve a list of files on your server returned as an
		array. You must supply the path to the desired directory.
		::

			$list = $this->ftp->list_files('/public_html/');
			print_r($list);

	.. method:: mirror($locpath, $rempath)

		:パラメータ	string	$locpath: Local path
		:パラメータ	string	$rempath: Remote path
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Recursively reads a local folder and everything it contains (including
		sub-folders) and creates a mirror via FTP based on it. Whatever the
		directory structure of the original file path will be recreated on the
		server. You must supply a source path and a destination path::

			 $this->ftp->mirror('/path/to/myfolder/', '/public_html/myfolder/');

	.. method:: mkdir($path[, $permissions = NULL])

		:パラメータ	string	$path: Path to directory to create
		:パラメータ	int	$permissions: Permissions (octal)
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Lets you create a directory on your server. Supply the path ending in
		the folder name you wish to create, with a trailing slash.

		Permissions can be set by passing an octal value in the second parameter.
		::

			// Creates a folder named "bar"
			$this->ftp->mkdir('/public_html/foo/bar/', 0755);

	.. method:: chmod($path, $perm)

		:パラメータ	string	$path: Path to alter permissions for
		:パラメータ	int	$perm: Permissions (octal)
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Permits you to set file permissions. Supply the path to the file or
		directory you wish to alter permissions on::

			// Chmod "bar" to 755
			$this->ftp->chmod('/public_html/foo/bar/', 0755);

	.. method:: changedir($path[, $suppress_debug = FALSE])

		:パラメータ	string	$path: Directory path
		:パラメータ	bool	$suppress_debug: Whether to turn off debug messages for this command
		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Changes the current working directory to the specified path.

		The ``$suppress_debug`` parameter is useful in case you want to use this method
		as an ``is_dir()`` alternative for FTP.

	.. method:: close()

		:返り値:	TRUE on success, FALSE on failure
		:返り値型:	bool

		Closes the connection to your server. It's recommended that you use this
		when you are finished uploading.
