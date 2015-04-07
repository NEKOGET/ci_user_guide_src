#########################
インストール方法
#########################

CodeIgniter は次の4つのステップでインストールできます:

#. ZIP パッケージを展開します。
#. サーバに CodeIgniter のフォルダとファイルをアップロードします。
   通常は、index.php ファイルがルートになります。
#. application/config/config.php ファイルをテキストエディタで開き、
   ベース URL を記入します。暗号化、もしくはセッションを利用する際には
   暗号鍵を記入します。
#. データベースを使用する際には、 application/config/database.php 
   ファイルをテキストエディタで開き、データベースの設定を
   記入します。

CodeIgniter のファイルの設置場所を隠すことでセキュリティを高めたい場合は、 system 
および application フォルダを別の名前に変更することができます。
これらのファイル名を変更した場合は、 index.php を開き $system_path 変数と $application_folder 
変数の値を新しいフォルダ名に
書き換えてください。

セキュリティ的にベストなのは、system および application フォルダ
をブラウザから直接アクセスできない、ウェブルートより上の階層に
移すことです。デフォルトでは、それぞれのフォルダに置かれた .htaccess 
ファイルにより、直接アクセスすることができないようになっています。
しかし、Web サーバの設定変更や .htaccess に対応しない場合に備えて、
これらは公開ディレクトリには置かないのがベストです。

views を公開したままにしたい場合は、 application フォルダの外に 
views フォルダを移動することもできます。

各フォルダを移動した場合は、 index.php を開き、 $system_path 変数と
$application_folder 変数、 $view_folder 変数の値を
フルパスで指定するのが好ましいでしょう。例) '/www/MyUser/system'。

追加の対策として、本番環境での PHP のエラー報告と、他の開発専用の
機能を無効にしました。
CodeIgniter では、環境変数は、設定することによって使用できます。
詳細は :doc:`セキュリティページ <../general/security>` を
確認してください。

これで終わりです！

初めて CodeIgniter を使うのでしたら、
ユーザガイドの :doc:`はじめよう <../overview/getting_started>`
から読んでみてください。動的な PHP アプリケーションの構築方法を学習できますよ！

.. toctree::
	:hidden:
	:titlesonly:

	downloads
	self
	upgrading
	troubleshooting

