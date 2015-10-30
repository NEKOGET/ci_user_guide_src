###############
トラブルシューティング
###############

トップページの読み込みだけは問題なく、他のページがうまく表示されない場合は、
検索エンジンに最適な URL を生成するのに必要な REQUEST_URI 環境変数が
サーバでサポートされていない可能性があります。
まず **application/config/config.php** ファイルを開き、URI Protocol の情報が
書いてある部分を探します。他の設定を試します。
それでもまだ動かない場合は、 application/config/config ファイルを開き、
CodeIgniterのURLにクエスチョンマークを強制的に
付加する必要があります。::

	$config['index_page'] = "index.php";

上記の部分を次のように変更します。::

	$config['index_page'] = "index.php?";
