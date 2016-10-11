############
静的なページ
############

**注意:** このチュートリアルでは、CodeIgniterをダウンロードし、開発環境に
:doc:`インストール <../installation/index>` されている
ものとします。

まず最初に静的なページを処理する  **controller** をセットアップします。
controllerは処理を指示するだけのシンプルなクラスです。
ここからWebアプリケーションを始められます。

たとえば、以下のようなURLにアクセスする場合を考えます。

	http://example.com/news/latest/10

この場合、 "news"という部分が controller を指します。
"news"で呼び出されるメソッドは"latest"で、"latest"メソッドは
10件の項目を取得し、ページ上に表示するメソッドだと仮定します。MVCモデルに則り、
URL構成は以下のようになっています。

	http://example.com/[controller-class]/[controller-method]/[arguments]

URL構成が複雑になるにつれて、これは変更することがありますが、
最初のうちは、これさえ知っていれば大丈夫です。

*application/controllers/Pages.php* ファイルを作成し、
以下のコードを書き込みます。

::

	<?php 
	class Pages extends CI_Controller { 

		public function view($page = 'home') 
		{
	        }
	}

ここでは ``$page`` という引数1つを受け付ける ``Pages`` というメソッドを作成しています。
``Pages`` クラスは ``CI_Controller`` クラスを継承しています。
これは、作成したPagesクラスが ``CI_Controller`` クラスで定義されている
メソッドと変数を使える事を意味しています。
(*system/core/Controller.php*).

あなたのWebアプリケーションにおいて、 **controllerは全てのリクエストの中心** になります。
CodeIgniterの高度な技術ディスカッションでは、これは *super object* と呼ばれます。
多くのPHPのクラスと同じく、controllerは ``$this`` という変数で参照できます。
``$this`` を参照する事で、
ライブラリやビュー、その他の CodeIgniter の機能をロードします。

今、基本的なページを作成する最初の段階としてメソッドを作成しました。
これから、ページのフッターとヘッダーとして動作する2つの
"views" （ページテンプレート）を作成します

*application/views/templates/header.php* にヘッダーを作成し、
以下のコードを書き込みます。

::

	<html>
		<head>
			<title>CodeIgniter Tutorial</title>
		</head>
		<body>

			<h1><?php echo $title; ?></h1>

The header contains the basic HTML code that you'll want to display
before loading the main view, together with a heading. It will also
output the ``$title`` variable, which we'll define later in the controller.
Now, create a footer at *application/views/templates/footer.php* that
includes the following code:

::

			<em>&copy; 2015</em>
		</body>
	</html>

コントローラにロジックの追加する
--------------------------------

Earlier you set up a controller with a ``view()`` method. The method
accepts one parameter, which is the name of the page to be loaded. The
static page templates will be located in the *application/views/pages/*
directory.

In that directory, create two files named *home.php* and *about.php*.
Within those files, type some text − anything you'd like − and save them.
If you like to be particularly un-original, try "Hello World!".

In order to load those pages, you'll have to check whether the requested
page actually exists:

::

	public function view($page = 'home')
	{
	        if ( ! file_exists(APPPATH.'/views/pages/'.$page.'.php'))
		{
			// Whoops, we don't have a page for that!
			show_404();
		}

		$data['title'] = ucfirst($page); // Capitalize the first letter

		$this->load->view('templates/header', $data);
		$this->load->view('pages/'.$page, $data);
		$this->load->view('templates/footer', $data);
	}

Now, when the page does exist, it is loaded, including the header and
footer, and displayed to the user. If the page doesn't exist, a "404
Page not found" error is shown.

The first line in this method checks whether the page actually exists.
PHP's native ``file_exists()`` function is used to check whether the file
is where it's expected to be. ``show_404()`` is a built-in CodeIgniter
function that renders the default error page.

In the header template, the ``$title`` variable was used to customize the
page title. The value of title is defined in this method, but instead of
assigning the value to a variable, it is assigned to the title element
in the ``$data`` array.

The last thing that has to be done is loading the views in the order
they should be displayed. The second parameter in the ``view()`` method is
used to pass values to the view. Each value in the ``$data`` array is
assigned to a variable with the name of its key. So the value of
``$data['title']`` in the controller is equivalent to ``$title`` in the
view.

ルーティング
------------

The controller is now functioning! Point your browser to
``[your-site-url]index.php/pages/view`` to see your page. When you visit
``index.php/pages/view/about`` you'll see the about page, again including
the header and footer.

Using custom routing rules, you have the power to map any URI to any
controller and method, and break free from the normal convention:
``http://example.com/[controller-class]/[controller-method]/[arguments]``

Let's do that. Open the routing file located at
*application/config/routes.php* and add the following two lines.
Remove all other code that sets any element in the ``$route`` array.

::

	$route['default_controller'] = 'pages/view';
	$route['(:any)'] = 'pages/view/$1';

CodeIgniter reads its routing rules from top to bottom and routes the
request to the first matching rule. Each rule is a regular expression
(left-side) mapped to a controller and method name separated by slashes
(right-side). When a request comes in, CodeIgniter looks for the first
match, and calls the appropriate controller and method, possibly with
arguments.

More information about routing can be found in the URI Routing
:doc:`documentation <../general/routing>`.

Here, the second rule in the ``$routes`` array matches **any** request
using the wildcard string ``(:any)``. and passes the parameter to the
``view()`` method of the ``Pages`` class.

Now visit ``index.php/about``. Did it get routed correctly to the ``view()``
method in the pages controller? Awesome!
