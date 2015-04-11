#################
カレンダークラス
#################

カレンダークラスを使うと動的にカレンダーを
生成できます。カレンダーはデザインのどんな
側面も100%コントロール可能なカレンダーテン
プレートをもとに整形されます。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

***************************
カレンダークラスを使う
***************************

クラスの初期化
======================

CodeIgniter の大半のクラスと同様に、カレンダークラスはコントローラの中で、 
$this->load->library メソッドを使って初期化します::

	$this->load->library('calendar');

一旦呼び出せば、カレンダーオブジェクトは $this->calendar を使用することで利用できます。::

	$this->calendar

カレンダーの表示
=====================

カレンダーの表示方法の非常に簡単な例です::

	$this->load->library('calendar');
	echo $this->calendar->generate();

上のコードは、サーバ時間の年月のカレンダーを生成します。 
特定の年月のカレンダーを表示させるには、カレンダー生成
関数に次のように情報を渡します::

	$this->load->library('calendar');
	echo $this->calendar->generate(2006, 6);

上のコードは、2006年6月を表示するカレンダ
ーを生成します。最初の引数は年を、2番目の
引数は月を指定します。

カレンダーのセルへのデータ引き渡し
===================================

カレンダーのセルにデータを追加するには、埋め込みたい日をキーにし、
それに対応する値に埋め込むデータを入れた連想配列を作成します。 
作成した配列は、カレンダー生成関数の第3引数に渡します。 
次のような例が考えられます::

	$this->load->library('calendar');

	$data = array(
		3  => 'http://example.com/news/article/2006/03/',
		7  => 'http://example.com/news/article/2006/07/',
		13 => 'http://example.com/news/article/2006/13/',
		26 => 'http://example.com/news/article/2006/26/'
	);

	echo $this->calendar->generate(2006, 6, $data);

上の例を使用すると、3,7,13および26日が渡された 
URL を指すリンクになります。

.. note:: 初期状態では、配列にはリンクが含まれるものとされています。
下のカレンダーテンプレートを説明した箇所では、セルに渡されたデータが
どのように扱われるかが示されています。 そこで、違う情報のタイプを指定
することができ、どのようにしてカスタマイズ可能かを知ることができます。

表示設定の変更
===========================

7つの設定項目でカレンダーの様々な面をコントロールできます。 
設定項目は呼び出し関数の第2引数に配列として渡して設定します。
例です::

	$prefs = array(
		'start_day'    => 'saturday',
		'month_type'   => 'long',
		'day_type'     => 'short'
	);

	$this->load->library('calendar', $prefs);

	echo $this->calendar->generate();

上のコードは、土曜日から始まり、「長い」形式の月名と「短い」
形式の曜日名をヘッダに使用します。 設定項目についての詳しい
情報は下記のとおりです。

======================  =================  ============================================  ===================================================================
設定項目                初期値             オプション                                    説明
======================  =================  ============================================  ===================================================================
**template**           	None               None                                          カレンダーテンプレートの文字列。テンプレート
											 のセクションを参照。
**local_time**        	time()             None                                          現在時刻として関連づける UNIX タイムスタンプ。
**start_day**           sunday             Any week day (sunday, monday, tuesday, etc.)  カレンダーの開始曜日。
**month_type**          long               long, short                                   ヘッダの月名をどの形式にするか。 
											   long = January, short = Jan.
**day_type**            abr                long, short, abr                              ヘッダのカラムの曜日名の形式にどの形式を用いるか。
											 long = Sunday, short = Sun, abr = Su.
**show_next_prev**      FALSE              TRUE/FALSE (boolean)                          次/ 前 の 月に移動できる URL を表示するかどうか。
											 この機能については下記参照。
**next_prev_url**       controller/method  A URL                                         次の月/ 前の月 のリンクを利用する場合の基準パス。
**show_other_days**     FALSE              TRUE/FALSE (boolean)                          暦月の最初であるか先週を分ける他の月日を表示するべきか
											 どうか決定します。
======================  =================  ============================================  ===================================================================


次の月 / 前の月 リンクの表示
=================================

次へ / 前へ のリンクを経由してカレンダーを動的に進める / 戻る 
ようにするには、次の例のようなコードをカレンダーのコードに設定
する必要があります::

	$prefs = array(
		'show_next_prev'  => TRUE,
		'next_prev_url'   => 'http://example.com/index.php/calendar/show/'
	);

	$this->load->library('calendar', $prefs);

	echo $this->calendar->generate($this->uri->segment(3), $this->uri->segment(4));

上のサンプルで、何点か気づいた点があるはずです:

-  "show_next_prev" を TRUE にする必要があります。
-  "next_prev_url" の設定項目でカレンダーが設置されている
   コントローラへの URL を指定しなければなりません。
   指定しなければ、*controller/method*にセットされます。
-  カレンダー生成関数に指定する「年」と「月」には、
   URI セグメントに出現するものを指定します
   (Note: カレンダークラスは基準の URL に対して
   年と月を自動的に追加します)

カレンダーテンプレートの作成
============================

カレンダーテンプレートを作成することで、カレンダーのデザイン
を100%コントロールできます。 カレンダーの各部品は次に示す
擬似変数のペアの間に配置されます::

	$prefs['template'] = '

		{table_open}<table border="0" cellpadding="0" cellspacing="0">{/table_open}

		{heading_row_start}<tr>{/heading_row_start}

		{heading_previous_cell}<th><a href="{previous_url}">&lt;&lt;</a></th>{/heading_previous_cell}
		{heading_title_cell}<th colspan="{colspan}">{heading}</th>{/heading_title_cell}
		{heading_next_cell}<th><a href="{next_url}">&gt;&gt;</a></th>{/heading_next_cell}

		{heading_row_end}</tr>{/heading_row_end}

		{week_row_start}<tr>{/week_row_start}
		{week_day_cell}<td>{week_day}</td>{/week_day_cell}
		{week_row_end}</tr>{/week_row_end}

		{cal_row_start}<tr>{/cal_row_start}
		{cal_cell_start}<td>{/cal_cell_start}
		{cal_cell_start_today}<td>{/cal_cell_start_today}
		{cal_cell_start_other}<td class="other-month">{/cal_cell_start_other}

		{cal_cell_content}<a href="{content}">{day}</a>{/cal_cell_content}
		{cal_cell_content_today}<div class="highlight"><a href="{content}">{day}</a></div>{/cal_cell_content_today}

		{cal_cell_no_content}{day}{/cal_cell_no_content}
		{cal_cell_no_content_today}<div class="highlight">{day}</div>{/cal_cell_no_content_today}

		{cal_cell_blank}&nbsp;{/cal_cell_blank}

		{cal_cell_other}{day}{cal_cel_other}

		{cal_cell_end}</td>{/cal_cell_end}
		{cal_cell_end_today}</td>{/cal_cell_end_today}
		{cal_cell_end_other}</td>{/cal_cell_end_other}
		{cal_row_end}</tr>{/cal_row_end}

		{table_close}</table>{/table_close}
	';

	$this->load->library('calendar', $prefs);

	echo $this->calendar->generate();

配列を使用して、`key=>value`ペアを渡すことで多くの
望む値を渡すことができます。省略するとCalendarクラス
で継承されたデフォルト値を使用します

使用例::

	$prefs['template'] = array(
		'table_open'           => '<table class="calendar">',
		'cal_cell_start'       => '<td class="day">',
		'cal_cell_start_today' => '<td class="today">'
	);
    
	$this->load->library('calendar', $prefs);
    
	echo $this->calendar->generate();

***************
クラスリファレンス
***************

.. class:: CI_Calendar

	.. method:: initialize([$config = array()])

		:パラメータ/////	array	$config: Configuration parameters
		:returns:	CI_Calendar instance (method chaining)
		:rtype:	CI_Calendar

		Initializes the Calendaring preferences. Accepts an associative array as input, containing display preferences.

	.. method:: generate([$year = ''[, $month = ''[, $data = array()]]])

		:param	int	$year: Year
		:param	int	$month: Month
		:param	array	$data: Data to be shown in the calendar cells
		:returns:	HTML-formatted calendar
		:rtype:	string

		Generate the calendar.


	.. method:: get_month_name($month)

		:param	int	$month: Month
		:returns:	Month name
		:rtype:	string

		Generates a textual month name based on the numeric month provided.

	.. method:: get_day_names($day_type = '')

		:param	string	$day_type: 'long', 'short', or 'abr'
		:returns:	Array of day names
		:rtype:	array

		Returns an array of day names (Sunday, Monday, etc.) based on the type
		provided. Options: long, short, abr. If no ``$day_type`` is provided (or
		if an invalid type is provided) this method will return the "abbreviated"
		style.

	.. method:: adjust_date($month, $year)

		:param	int	$month: Month
		:param	int	$year: Year
		:returns:	An associative array containing month and year
		:rtype:	array

		This method makes sure that you have a valid month/year. For example, if
		you submit 13 as the month, the year will increment and the month will
		become January::

			print_r($this->calendar->adjust_date(13, 2014));

		outputs::

			Array
			(    
				[month] => '01'
				[year] => '2015'
			)

	.. method:: get_total_days($month, $year)

		:param	int	$month: Month
		:param	int	$year: Year
		:returns:	Count of days in the specified month
		:rtype:	int

		Total days in a given month::

			echo $this->calendar->get_total_days(2, 2012);
			// 29

		.. note:: This method is an alias for :doc:`Date Helper
			<../helpers/date_helper>` function :func:`days_in_month()`.

	.. method:: default_template()

		:returns:	An array of template values
		:rtype:	array

		Sets the default template. This method is used when you have not created
		your own template.


	.. method:: parse_template()

		:returns:	CI_Calendar instance (method chaining)
		:rtype:	CI_Calendar

		Harvests the data within the template ``{pseudo-variables}`` used to
		display the calendar.
