######################
アプリケーションの管理
######################

デフォルトでは CodeIgniter はアプリケーションを 1 つだけ作りたいものと仮定し、
*application/* ディレクトリに作るものとします。
これはこれで十分そうですが、しかしながら、
複数のアプリケーションセットを持ちたいときは、
アプリケーションディレクトリの名前を変更、再配置することができます。

アプリケーションディレクトリの名前を変更する
============================================

アプリケーションディレクトリの名前を変更したい場合、実際に変更をして、
メインの index.php ファイルを開いて
``$application_folder`` 変数にその名前を設定します::

	$application_folder = 'application';

アプリケーションディレクトリの再配置
====================================

サーバ上の別の場所にアプリケーションディレクトリを移動させ、
ドキュメントルートから外すことが可能です。
これを行うため、メインの index.php を開き、 *サーバのフルパス* を
``$application_folder`` 変数に設定します::

	$application_folder = '/path/to/your/application';

ひとつの CodeIgniter のインストールで複数のアプリケーションを実行する
=====================================================================

共通の CodeIgniter のインストールを共有していくつかの異なるアプリケーションを管理したい場合、
単純にアプリケーションディレクトリ内に
それそのものをサブディレクトリとしてすべて並べます。

たとえば、「 foo 」と
「 bar 」という名前の 2 つのアプリケーションを作成するとしましょう。つぎのようにアプリケーションのディレクトリを構築できます::

	applications/foo/
	applications/foo/config/
	applications/foo/controllers/
	applications/foo/libraries/
	applications/foo/models/
	applications/foo/views/
	applications/bar/
	applications/bar/config/
	applications/bar/controllers/
	applications/bar/libraries/
	applications/bar/models/
	applications/bar/views/

どのアプリケーションを使うかを選択するために、メインの
index.php ファイルを開き ``$application_folder`` 変数を設定する必要があります。
たとえば、「 foo 」というアプリケーションを使うためには::

	$application_folder = 'applications/foo';

.. note:: アプリケーションにはそれぞれ、必要なアプリケーションを呼び出す独自の index.php
	ファイルが必要になります。 index.php ファイルには、
	お望みの名前を自由につけることができます。