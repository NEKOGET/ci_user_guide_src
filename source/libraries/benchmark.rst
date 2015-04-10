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

The Benchmark class can be used within your
:doc:`controllers </general/controllers>`,
:doc:`views </general/views>`, or your :doc:`models </general/models>`.
The process for usage is this:

#. Mark a start point
#. Mark an end point
#. Run the "elapsed time" function to view the results

Here's an example using real code::

	$this->benchmark->mark('code_start');

	// Some code happens here

	$this->benchmark->mark('code_end');

	echo $this->benchmark->elapsed_time('code_start', 'code_end');

.. note:: The words "code_start" and "code_end" are arbitrary. They
	are simply words used to set two markers. You can use any words you
	want, and you can set multiple sets of markers. Consider this example::

		$this->benchmark->mark('dog');

		// Some code happens here

		$this->benchmark->mark('cat');

		// More code happens here

		$this->benchmark->mark('bird');

		echo $this->benchmark->elapsed_time('dog', 'cat');
		echo $this->benchmark->elapsed_time('cat', 'bird');
		echo $this->benchmark->elapsed_time('dog', 'bird');


プロファイラ用の観測点
===============================

If you want your benchmark data to be available to the
:doc:`Profiler </general/profiling>` all of your marked points must
be set up in pairs, and each mark point name must end with _start and
_end. Each pair of points must otherwise be named identically. Example::

	$this->benchmark->mark('my_mark_start');

	// Some code happens here...

	$this->benchmark->mark('my_mark_end');

	$this->benchmark->mark('another_mark_start');

	// Some more code happens here...

	$this->benchmark->mark('another_mark_end');

Please read the :doc:`Profiler page </general/profiling>` for more
information.

総計実行時間の表示
===============================

If you would like to display the total elapsed time from the moment
CodeIgniter starts to the moment the final output is sent to the
browser, simply place this in one of your view templates::

	<?php echo $this->benchmark->elapsed_time();?>

You'll notice that it's the same function used in the examples above to
calculate the time between two point, except you are **not** using any
parameters. When the parameters are absent, CodeIgniter does not stop
the benchmark until right before the final output is sent to the
browser. It doesn't matter where you use the function call, the timer
will continue to run until the very end.

An alternate way to show your elapsed time in your view files is to use
this pseudo-variable, if you prefer not to use the pure PHP::

	{elapsed_time}

.. note:: If you want to benchmark anything within your controller
	functions you must set your own start/end points.

メモリ使用量の表示
=============================

If your PHP installation is configured with --enable-memory-limit, you
can display the amount of memory consumed by the entire system using the
following code in one of your view file::

	<?php echo $this->benchmark->memory_usage();?>

.. note:: This function can only be used in your view files. The consumption
	will reflect the total memory used by the entire app.

An alternate way to show your memory usage in your view files is to use
this pseudo-variable, if you prefer not to use the pure PHP::

	{memory_usage}


***************
クラスリファレンス
***************

.. class:: CI_Benchmark

	.. method:: mark($name)

		:パラメータ	string	$name: the name you wish to assign to your marker
		:返り値型:	void

		Sets a benchmark marker.

	.. method:: elapsed_time([$point1 = ''[, $point2 = ''[, $decimals = 4]]])

		:パラメータ	string	$point1: a particular marked point
		:パラメータ	string	$point2: a particular marked point
		:パラメータ	int	$decimals: number of decimal places for precision
		:返り値:	Elapsed time
		:返り値型:	string

		Calculates and returns the time difference between two marked points.

		If the first parameter is empty this function instead returns the
		``{elapsed_time}`` pseudo-variable. This permits the full system
		execution time to be shown in a template. The output class will
		swap the real value for this variable.


	.. method:: memory_usage()

		:返り値:	Memory usage info
		:返り値型:	string

		Simply returns the ``{memory_usage}`` marker.

		This permits it to be put it anywhere in a template without the memory
		being calculated until the end. The :doc:`Output Class <output>` will
		swap the real value for this variable.
