##################
ベンチマーククラス
##################

CodeIgniter には、常時アクティブなベンチマーククラスがあります。 ベンチマーククラスは、
あらかじめマークしておいた複数の観測点のうちの任意の2つの時点の時間差を計算できます。

.. note:: このクラスは、システムで自動的に初期化されるので、
手動で初期化する必要はありません。

さらに、ベンチマークは、フレームワークが呼び出される瞬間に必ず
開始され、出力クラスがブラウザに最後のビューを送信する直前に終
わるので、 非常に正確なタイミングで全システムの実行について示す
ことができます。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

*************************
ベンチマーククラスを使用する
*************************

ベンチマーククラスは:doc:`コントローラ </general/controllers>`、
:doc:`ビュー </general/views>`または:doc:`モデル 
</general/models>`の中で使用できます。
使う手順は次のとおりです:

#. 観測開始点をマークします。
#. 観測終了点をマークします。
#. 結果を見るため「経過時間("elapsed time")」メソッドを実行します。

実際のコードでの使用例です::

	$this->benchmark->mark('code_start');

	// ここに何かのコードを記述

	$this->benchmark->mark('code_end');

	echo $this->benchmark->elapsed_time('code_start', 'code_end');

.. note::  "code_start" と "code_end" のところの語は、任意の語が使えます。 
	これら2つの言葉は、単に観測点を2つ設定するために使われたに過ぎません。	好きな言葉を使えますし、
	重複させて観測点の組み合わせを設定することもできます。次のような例が考えられます:

		$this->benchmark->mark('dog');

		// ここに何かのコードを記述

		$this->benchmark->mark('cat');

		// さらに、ここに何かのコードを記述

		$this->benchmark->mark('bird');

		echo $this->benchmark->elapsed_time('dog', 'cat');
		echo $this->benchmark->elapsed_time('cat', 'bird');
		echo $this->benchmark->elapsed_time('dog', 'bird');


プロファイラ用の観測点
===============================

:doc:`プロファイラ </general/profiling>` でベンチマークデータを使用するには、 観測点はペアにする必要があり、
各々の観測点の名前の末尾を _start と _end にしなければなりません。 さらに、ペアになる観測点の名前は、
元が同じ(identicaly)である必要があります [ 訳注: ペアになっている開始点と終了点の名前は、末尾の_start _end 
を除いた部分が同じものでなければなりません ]。 例:

	$this->benchmark->mark('my_mark_start');

	// ここに何かのコードを記述...

	$this->benchmark->mark('my_mark_end');

	$this->benchmark->mark('another_mark_start');

	// さらに、ここに何かのコードを記述...

	$this->benchmark->mark('another_mark_end');

詳しくは、:doc:`プロファイラ </general/profiling>`のページ
をご覧ください。

総計実行時間の表示
===============================

CodeIgniter がスタートした瞬間から、出力がブラウザに送信された瞬間ま
での総計の経過時間を表示したいときには、 ビューのテンプレートに次の
コードを設置するだけです:

	<?php echo $this->benchmark->elapsed_time();?>

上の2つの観測点の計算で、使った関数と引数がないという
こと以外同じものを使用していることに気づいたと思います。 
CodeIgniterでは、引数が省略されたとき、最終出力がブラウ
ザに送信されるまでベンチマークは停止しません。どこで関
数を使用したかには関係なく、タイマーは最後の最後まで継
続されます。

PHP をそのまま使用したくないときは、ビューのファイル内で次の
擬似変数を用いれば、総計経過時間を見る代替手段になります::

	{elapsed_time}

.. note:: ベンチマークをコントローラの関数内で使用する場合は、
          必ず開始と終了の観測点をセットする必要があります。

メモリ使用量の表示
=============================

PHP の設定が --enable-memory-limit になっている場合、ビューのファイル内
に次のコードを配置することで、 システム全体のメモリの使用量を表示させる
ことができます::

	<?php echo $this->benchmark->memory_usage();?>

.. note:: この関数は、ビューのファイル内でのみ使用できます。この使用量は、
アプリケーション全体で使っているメモリの使用量です。

PHP をそのまま使用したくないときは、ビューのファイル内で次の擬似変数を用いれば、
メモリ消費量を見る代替手段になります:::

	{memory_usage}


***************
クラスリファレンス
***************

.. php:class:: CI_Benchmark

	.. php:method:: mark($name)

		:パラメータ	string	$name: マーカーにつけたい名前
		:返り値型:	void

		ベンチマークマーカーをセットします。

	.. php:method:: elapsed_time([$point1 = ''[, $point2 = ''[, $decimals = 4]]])

		:パラメータ	string	$point1: 特定のマークされた点
		:パラメータ	string	$point2: a particular marked point
		:パラメータ	int	$decimals: 小数点以下の桁数
		:返り値:	経過時間
		:返り値型:	string

		2つのマークされた点の時差を計算して、返します。

		最初の引数が省略されたとき、｛elapsed_time｝擬似
		変数を用いれば、システム全体のテンプレートで示さ
		れる実行時間を代替し、クラスが実行値をこの変数に
		変換して出力します。


	.. php:method:: memory_usage()

		:返り値:	Memory usage info
		:返り値型:	string

		単に``{memory_usage}``マーカーを返します。

		どこでもテンプレート上の最後まで計算された
		メモリ量を許容し、:doc:`出力クラス <output>`は
		実行値をこの変数に変換します。
