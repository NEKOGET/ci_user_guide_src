########################
テンプレートパーサクラス
########################

テンプレートパーサクラスを使うと、ビューファイルに含まれる
擬似変数を解析できます。
単一の擬似変数と擬似変数のペアを解析できます。

テンプレートエンジンを一度も使ったことがないでしょうか？
擬似変数とは次のようなものです::

	<html>
		<head>
			<title>{blog_title}</title>
		</head>
		<body>
			<h3>{blog_heading}</h3>

		{blog_entries}
			<h5>{title}</h5>
			<p>{body}</p>
		{/blog_entries}

		</body>
	</html>

これらの疑似変数は、本当の PHP 変数ではなく、
テンプレート (ビューファイル)からPHPを取り除くための、 
普通のテキストです。

.. note:: ビューページで純粋なPHPを使う方が少し早いので、CodeIgniterでは、
	このクラスを必ずしも 必要としません。
        しかし、PHPのコードで混乱してしまうデザイナーと一緒に仕事をして
        いる場合、開発者の中には、テンプレートエンジンを使用したい人も
        いると思います。

.. important:: テンプレートパーサクラスは、本格的なテンプレート解析ソリューション
	ではありません。 私達は、パフォーマンス最大化のために、これを非常にスマート
	にしています。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

********************************
テンプレートパーサクラスの使い方
********************************

クラスの初期化
==============

Like most other classes in CodeIgniter, the Parser class is initialized
in your controller using the ``$this->load->library()`` method::

	$this->load->library('parser');

Once loaded, the Parser library object will be available using:
$this->parser

テンプレートを解析すること
==========================

You can use the ``parse()`` method to parse (or render) simple templates,
like this::

	$data = array(
		'blog_title' => 'My Blog Title',
		'blog_heading' => 'My Blog Heading'
	);

	$this->parser->parse('blog_template', $data);

The first parameter contains the name of the :doc:`view
file <../general/views>` (in this example the file would be called
blog_template.php), and the second parameter contains an associative
array of data to be replaced in the template. In the above example, the
template would contain two variables: {blog_title} and {blog_heading}

There is no need to "echo" or do something with the data returned by
$this->parser->parse(). It is automatically passed to the output class
to be sent to the browser. However, if you do want the data returned
instead of sent to the output class you can pass TRUE (boolean) as the
third parameter::

	$string = $this->parser->parse('blog_template', $data, TRUE);

ペアの擬似変数
==============

The above example code allows simple variables to be replaced. What if
you would like an entire block of variables to be repeated, with each
iteration containing new values? Consider the template example we showed
at the top of the page::

	<html>
		<head>
			<title>{blog_title}</title>
		</head>
		<body>
			<h3>{blog_heading}</h3>

		{blog_entries}
			<h5>{title}</h5>
			<p>{body}</p>
		{/blog_entries}

		</body>
	</html>

In the above code you'll notice a pair of variables: {blog_entries}
data... {/blog_entries}. In a case like this, the entire chunk of data
between these pairs would be repeated multiple times, corresponding to
the number of rows in the "blog_entries" element of the parameters array.

Parsing variable pairs is done using the identical code shown above to
parse single variables, except, you will add a multi-dimensional array
corresponding to your variable pair data. Consider this example::

	$this->load->library('parser');

	$data = array(
		'blog_title'   => 'My Blog Title',
		'blog_heading' => 'My Blog Heading',
		'blog_entries' => array(
			array('title' => 'Title 1', 'body' => 'Body 1'),
			array('title' => 'Title 2', 'body' => 'Body 2'),
			array('title' => 'Title 3', 'body' => 'Body 3'),
			array('title' => 'Title 4', 'body' => 'Body 4'),
			array('title' => 'Title 5', 'body' => 'Body 5')
		)
	);

	$this->parser->parse('blog_template', $data);

If your "pair" data is coming from a database result, which is already a
multi-dimensional array, you can simply use the database ``result_array()``
method::

	$query = $this->db->query("SELECT * FROM blog");

	$this->load->library('parser');

	$data = array(
		'blog_title'   => 'My Blog Title',
		'blog_heading' => 'My Blog Heading',
		'blog_entries' => $query->result_array()
	);

	$this->parser->parse('blog_template', $data);

使用メモ
========

If you include substitution parameters that are not referenced in your
template, they are ignored::

	$template = 'Hello, {firstname} {lastname}';
	$data = array(
		'title' => 'Mr',
		'firstname' => 'John',
		'lastname' => 'Doe'
	);
	$this->parser->parse_string($template, $data);

	// Result: Hello, John Doe

If you do not include a substitution parameter that is referenced in your
template, the original pseudo-variable is shown in the result::

	$template = 'Hello, {firstname} {initials} {lastname}';
	$data = array(
		'title' => 'Mr',
		'firstname' => 'John',
		'lastname' => 'Doe'
	);
	$this->parser->parse_string($template, $data);

	// Result: Hello, John {initials} Doe

If you provide a string substitution parameter when an array is expected,
i.e. for a variable pair, the substitution is done for the opening variable
pair tag, but the closing variable pair tag is not rendered properly::

	$template = 'Hello, {firstname} {lastname} ({degrees}{degree} {/degrees})';
	$data = array(
		'degrees' => 'Mr',
		'firstname' => 'John',
		'lastname' => 'Doe',
		'titles' => array(
			array('degree' => 'BSc'),
			array('degree' => 'PhD')
		)
	);
	$this->parser->parse_string($template, $data);

	// Result: Hello, John Doe (Mr{degree} {/degrees})

If you name one of your individual substitution parameters the same as one
used inside a variable pair, the results may not be as expected::

	$template = 'Hello, {firstname} {lastname} ({degrees}{degree} {/degrees})';
	$data = array(
		'degree' => 'Mr',
		'firstname' => 'John',
		'lastname' => 'Doe',
		'degrees' => array(
			array('degree' => 'BSc'),
			array('degree' => 'PhD')
		)
	);
	$this->parser->parse_string($template, $data);

	// Result: Hello, John Doe (Mr Mr )

ビューフラグメント
==================

You do not have to use variable pairs to get the effect of iteration in
your views. It is possible to use a view fragment for what would be inside
a variable pair, and to control the iteration in your controller instead
of in the view.

An example with the iteration controlled in the view::

	$template = '<ul>{menuitems}
		<li><a href="{link}">{title}</a></li>
	{/menuitems}</ul>';

	$data = array(
		'menuitems' => array(
			array('title' => 'First Link', 'link' => '/first'),
			array('title' => 'Second Link', 'link' => '/second'),
		)
	);
	$this->parser->parse_string($template, $data);

Result::

	<ul>
		<li><a href="/first">First Link</a></li>
		<li><a href="/second">Second Link</a></li>
	</ul>

An example with the iteration controlled in the controller, 
using a view fragment::

	$temp = '';
	$template1 = '<li><a href="{link}">{title}</a></li>';
	$data1 = array(
		array('title' => 'First Link', 'link' => '/first'),
		array('title' => 'Second Link', 'link' => '/second'),
	);

	foreach ($data1 as $menuitem)
	{
		$temp .= $this->parser->parse_string($template1, $menuitem, TRUE);
	}

	$template = '<ul>{menuitems}</ul>';
	$data = array(
		'menuitems' => $temp
	);
	$this->parser->parse_string($template, $data);

Result::

	<ul>
		<li><a href="/first">First Link</a></li>
		<li><a href="/second">Second Link</a></li>
	</ul>

******************
クラスリファレンス
******************

.. php:class:: CI_Parser

	.. php:method:: parse($template, $data[, $return = FALSE])

		:param	string	$template: Path to view file
		:param	array	$data: Variable data
		:param	bool	$return: Whether to only return the parsed template
		:returns:	Parsed template string
		:rtype:	string

		Parses a template from the provided path and variables.

	.. php:method:: parse_string($template, $data[, $return = FALSE])

		:param	string	$template: Path to view file
		:param	array	$data: Variable data
		:param	bool	$return: Whether to only return the parsed template
		:returns:	Parsed template string
		:rtype:	string

		This method works exactly like ``parse()``, only it accepts
		the template as a string instead of loading a view file.

	.. php:method:: set_delimiters([$l = '{'[, $r = '}']])

		:param	string	$l: Left delimiter
		:param	string	$r: Right delimiter
		:rtype: void

		Sets the delimiters (opening and closing) for a
		pseudo-variable "tag" in a template.
