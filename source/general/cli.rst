##############
CLI で実行する
##############

アプリケーション :doc:`コントローラ <./controllers>`
の呼び出しはブラウザによる URL だけでなく、コマンドラインインターフェイス
(CLI) を使用してロードすることもできます。

.. contents:: Page Contents

CLIとは何ですか？
=================

コマンドラインインターフェースは、コンピュータとのテキストベースでの対話方法です。
詳細については `Wikipedia
の記事 <http://en.wikipedia.org/wiki/Command-line_interface>`_ をご確認ください。

なぜ、コマンドラインで実行するのでしょうか？
============================================

CodeIgniterをコマンドラインから実行する理由は数多く存在しますが、
必ずしも明確ではありません。

-  *wget* や *curl* を使用することなく cron ジョブを実行する
-  :php:func:`is_cli()` の戻り値をチェックすることで、URL からロードされることがないように
   cron ジョブをアクセスできなくする
-  パーミッション設定、キャッシュフォルダの削除、
   バックアップを実行するなど、インタラクティブな「タスク」を作る
-  他の言語、他のアプリケーションとの統合。たとえば、てきとうな
   C++ スクリプトは 1 つのコマンドを呼び出してモデルのコードを実行できるでしょう！

やってみましょう: Hello World!
==============================

簡単なコントローラを作成してみて、その動きを見てみましょう。
テキストエディタを使って Tools.php というファイルを作成し、
その中に次のコードを配置します::

	<?php
	class Tools extends CI_Controller {

		public function message($to = 'World')
		{
			echo "Hello {$to}!".PHP_EOL;
		}
	}

次に、そのファイルを *application/controllers/* フォルダに保存します。

さて、通常次のような URL を使ってサイトを開きます::

	example.com/index.php/tools/message/to

そのかわりに、 Mac/Linux でのターミナルを開くか、 Windowsで >「 cmd 」を実行し、
CodeIgniter プロジェクトに移動しましょう。

.. code-block:: bash

	$ cd /path/to/project;
	$ php index.php tools message

正しく作業できたなら、 *Hello World!* がプリントされるでしょう。

.. code-block:: bash

	$ php index.php tools message "John Smith"

ここでは URL パラメータが動くのと同様の方法で引数を渡しています。
「 John Smith 」は、引数として渡され、出力されます::

	Hello John Smith!

さいごに！
==========

これが、つまり、コマンドライン上のコントローラについて知っておくべきすべてです。
これはただの標準的なコントローラであるため、ルーティングと
``_remap()`` も正常に動作することを忘れないでください。