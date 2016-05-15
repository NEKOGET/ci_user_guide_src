###################################
XML-RPC および XML-RPC サーバクラス
###################################

CodeIgniter の XML-RPC クラスを使うと、他のサーバにリクエストを送信したり、
リクエストを受信するために XML-RPCサーバをセットアップしたりできます。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

************
XML-RPCとは?
************

インターネット上の2つのコンピュータが対話する方法で非常にシンプル
なのは、XML を使うものです。 クライアントと呼ばれる一方のコンピュ
ータは、XML-RPC リクエストを サーバと呼ばれるもう一方のコンピュー
タに送信します。サーバがリクエストを受信し、処理すると、 クライア
ントにレスポンスを返します。

たとえば、MetaWeblog API を使う場合、XML-RPC クライアント (大抵は、
デスクトップの投稿ツール) は、 サイトで実行されている XML-RPC サ
ーバにリクエストを送ります。記事を公開するために、 新しいブログの
記事を作成するリクエストであったり、既存の記事を編集するためのリク
エストであったりします。 XML-RPC サーバはリクエストを受信すると、
リクエストを処理するために、どのクラス/メソッドを呼び出せばいいの
かを決定します。 処理されると、サーバはレスポンスメッセージを送り
返します。

仕様の詳細については、`XML-RPC <http://www.xmlrpc.com/>`_ のサイトをみてください。

*********************
XML-RPCクラスの使い方
*********************

クラスの初期化
==============

CodeIgniter のほとんどのクラスと同様、XML-RPC および XML-RPCS
クラスは、コントローラの中で $this->load->library メソッドを使
って初期化します:

XML-RPC クラスを読み込むには次のようにします::

	$this->load->library('xmlrpc');

一旦読み込まれると、xml-rpc ライブラリのオブジェクトは、次のようにして利用できます:
$this->xmlrpc

XML-RPC Server クラスを読み込むには次のようにします::

	$this->load->library('xmlrpc');
	$this->load->library('xmlrpcs');

読み込まれると、xml-rpcs ライブラリのオブジェクトは次のようにして利用できます::
$this->xmlrpcs

.. note:: XML-RPC Sever クラスを使うときは、XML-RPC クラスと 
	XML-RPC Server クラスの「両方」を読み込む必要があります。

XML-RPC リクエストの送信
========================

XML-RPC サーバにリクエストを送信するには、次の情報を指定する
必要があります:

-  サーバの URL
-  呼び出したいサーバ上のメソッド
-  *リクエスト データ* (後述)

以下は、`Ping-o-Matic <http://pingomatic.com/>`_ で、
単純にWeblogs.comにPingを送信する基本的な例です。

::

	$this->load->library('xmlrpc');

	$this->xmlrpc->server('http://rpc.pingomatic.com/', 80);
	$this->xmlrpc->method('weblogUpdates.ping');

	$request = array('My Photoblog', 'http://www.my-site.com/photoblog/');
	$this->xmlrpc->request($request);

	if ( ! $this->xmlrpc->send_request())
	{
		echo $this->xmlrpc->display_error();
	}

解説
----

上のコードでは、XML-RPC クラスを初期化し、サーバの URL と呼び出すメソッド
(weblogUpdates.ping)をセットしています。 リクエスト (この場合は、タイトル
と自分のサイトの URL) が送信用に配列に格納され、 request() メソッドを使っ
てコンパイルされます。 最後に、完全なリクエストが送信されます。send_request() 
メソッドが FALSE を返した場合は、 XML-RPC サーバから返ってきたエラーメッ
セージを表示します。

リクエストの詳細
================

リクエストとは単純に XML-RPC サーバに送信するデータのことです。 
リクエストの中の各データはリクエストパラメータとして参照されます。
上の例では、2つのパラメータをもっています: 自分のサイトの URL と 
タイトルです。XML-RPC サーバがリクエストを受信したとき、必要なパラ
メータを探します。

リクエストパラメータは、送信のため、配列に格納する必要があり、 
各パラメータは7つのデータ型(文字列、数値、日付 など) のうちのどれか
になります。 パラメータが文字列型以外の場合は、データ型をリクエスト
の配列に含める必要があります。

下記は、3つのパラメータの単純な配列の例です::

	$request = array('John', 'Doe', 'www.some-site.com');
	$this->xmlrpc->request($request);

文字列でないデータ型のデータや何種類かの異なるデータ型のデータを
指定するときは、 各パラメータを配列にし、その配列の2番目でデータ
型を指定します::

	$request = array(
		array('John', 'string'),
		array('Doe', 'string'),
		array(FALSE, 'boolean'),
		array(12345, 'int')
	); 
	$this->xmlrpc->request($request);

下の`データ型 <#datatypes>`_ のセクションにデータ型の
全リストがあります。 

XML-RPC サーバの作成
====================

XML-RPC サーバは交通整理役として動作し、送信されてくるリ
クエストを待ち受け、 その処理を行う適切なメソッドへリダ
イレクトします。

XML-RPC サーバを作成するには、XML-RPC Server クラスを、 
送信されてくるリクエストを受け取るコントローラで初期化し、 
処理できる適切なクラス/メソッドに送信されてきたリクエスト
を送るためにマッピングを指示した配列をセットアップする必
要があります。

次は、説明のための例です::

	$this->load->library('xmlrpc');
	$this->load->library('xmlrpcs');

	$config['functions']['new_post'] = array('function' => 'My_blog.new_entry');
	$config['functions']['update_post'] = array('function' => 'My_blog.update_entry');
	$config['object'] = $this;

	$this->xmlrpcs->initialize($config);
	$this->xmlrpcs->serve();

上のサンプルには、サーバが処理できる2つのメソッドが配列に指定されて
いる箇所があります。 処理できる公開メソッドは、配列の左側になります。
メソッドのどちらかのリクエストが受信されると、右側で指定したクラスと
メソッドにマッピングされます。

'object' キーは、インスタンス化されたクラスを渡すための特別なキーです。
これは、あなたがマッピングするメソッドが CodeIgniter スーパーオブジェクト
の一部でない場合に必要となります。

言い換えると、XML-RPC クライアントがnew_post メソッドを呼び出すリクエスト
を送信した場合、 サーバはMy_blog クラスを読み込み、new_entry メソッドを呼
び出します。 update_post メソッドを呼び出すリクエストを送信した場合は、 サ
ーバはMy_blog クラスを読み込み、update_entry メソッドを呼び出します。

The function names in the above example are arbitrary. You'll decide
what they should be called on your server, or if you are using
standardized APIs, like the Blogger or MetaWeblog API, you'll use their
function names.

There are two additional configuration keys you may make use of when
initializing the server class: debug can be set to TRUE in order to
enable debugging, and xss_clean may be set to FALSE to prevent sending
data through the Security library's ``xss_clean()`` method.

サーバリクエストの処理
======================

When the XML-RPC Server receives a request and loads the class/method
for processing, it will pass an object to that method containing the
data sent by the client.

Using the above example, if the new_post method is requested, the
server will expect a class to exist with this prototype::

	class My_blog extends CI_Controller {

		public function new_post($request)
		{

		}
	}

The $request variable is an object compiled by the Server, which
contains the data sent by the XML-RPC Client. Using this object you will
have access to the *request parameters* enabling you to process the
request. When you are done you will send a Response back to the Client.

Below is a real-world example, using the Blogger API. One of the methods
in the Blogger API is ``getUserInfo()``. Using this method, an XML-RPC
Client can send the Server a username and password, in return the Server
sends back information about that particular user (nickname, user ID,
email address, etc.). Here is how the processing function might look::

	class My_blog extends CI_Controller {

		public function getUserInfo($request)
		{
			$username = 'smitty';
			$password = 'secretsmittypass';

			$this->load->library('xmlrpc');

			$parameters = $request->output_parameters();

			if ($parameters[1] != $username && $parameters[2] != $password)
			{
				return $this->xmlrpc->send_error_message('100', 'Invalid Access');
			}

			$response = array(
				array(
					'nickname'  => array('Smitty', 'string'),
					'userid'    => array('99', 'string'),
					'url'       => array('http://yoursite.com', 'string'),
					'email'     => array('jsmith@yoursite.com', 'string'),
					'lastname'  => array('Smith', 'string'),
					'firstname' => array('John', 'string')
				),
	                         'struct'
			);

			return $this->xmlrpc->send_response($response);
		}
	}

Notes:
------

The ``output_parameters()`` method retrieves an indexed array
corresponding to the request parameters sent by the client. In the above
example, the output parameters will be the username and password.

If the username and password sent by the client were not valid, and
error message is returned using ``send_error_message()``.

If the operation was successful, the client will be sent back a response
array containing the user's info.

レスポンスのフォーマット
========================

Similar to *Requests*, *Responses* must be formatted as an array.
However, unlike requests, a response is an array **that contains a
single item**. This item can be an array with several additional arrays,
but there can be only one primary array index. In other words, the basic
prototype is this::

	$response = array('Response data', 'array');

Responses, however, usually contain multiple pieces of information. In
order to accomplish this we must put the response into its own array so
that the primary array continues to contain a single piece of data.
Here's an example showing how this might be accomplished::

	$response = array(
		array(
			'first_name' => array('John', 'string'),
			'last_name' => array('Doe', 'string'),
			'member_id' => array(123435, 'int'),
			'todo_list' => array(array('clean house', 'call mom', 'water plants'), 'array'),
		),
		'struct'
	);

Notice that the above array is formatted as a struct. This is the most
common data type for responses.

As with Requests, a response can be one of the seven data types listed
in the `Data Types <#datatypes>`_ section.

エラー応答の送信
================

If you need to send the client an error response you will use the
following::

	return $this->xmlrpc->send_error_message('123', 'Requested data not available');

The first parameter is the error number while the second parameter is
the error message.

独自のクライアントとサーバの作成
================================

To help you understand everything we've covered thus far, let's create a
couple controllers that act as XML-RPC Client and Server. You'll use the
Client to send a request to the Server and receive a response.

クライアント
------------

Using a text editor, create a controller called Xmlrpc_client.php. In
it, place this code and save it to your application/controllers/
folder::

	<?php

	class Xmlrpc_client extends CI_Controller {

		public function index()
		{
			$this->load->helper('url');
			$server_url = site_url('xmlrpc_server');

			$this->load->library('xmlrpc');

			$this->xmlrpc->server($server_url, 80);
			$this->xmlrpc->method('Greetings');

			$request = array('How is it going?');
			$this->xmlrpc->request($request);

			if ( ! $this->xmlrpc->send_request())
			{
				echo $this->xmlrpc->display_error();
			}
			else
			{
				echo '<pre>';
				print_r($this->xmlrpc->display_response());
				echo '</pre>';
			}
		}
	}
	?>

.. note:: In the above code we are using a "url helper". You can find more
	information in the :doc:`Helpers Functions <../general/helpers>` page.

サーバ
------

Using a text editor, create a controller called Xmlrpc_server.php. In
it, place this code and save it to your application/controllers/
folder::

	<?php

	class Xmlrpc_server extends CI_Controller {

		public function index()
		{
			$this->load->library('xmlrpc');
			$this->load->library('xmlrpcs');

			$config['functions']['Greetings'] = array('function' => 'Xmlrpc_server.process');

			$this->xmlrpcs->initialize($config);
			$this->xmlrpcs->serve();
		}


		public function process($request)
		{
			$parameters = $request->output_parameters();

			$response = array(
				array(
					'you_said'  => $parameters[0],
					'i_respond' => 'Not bad at all.'
				),
				'struct'
			);

			return $this->xmlrpc->send_response($response);
		}
	}


やってみよう!
-------------

Now visit the your site using a URL similar to this::

	example.com/index.php/xmlrpc_client/

You should now see the message you sent to the server, and its response
back to you.

The client you created sends a message ("How's is going?") to the
server, along with a request for the "Greetings" method. The Server
receives the request and maps it to the ``process()`` method, where a
response is sent back.

リクエストパラメータに連想配列を使用する
========================================

If you wish to use an associative array in your method parameters you
will need to use a struct datatype::

	$request = array(
		array(
			// Param 0
			array('name' => 'John'),
			'struct'
		),
		array(
			// Param 1
			array(
				'size' => 'large',
				'shape'=>'round'
			),
			'struct'
		)
	);

	$this->xmlrpc->request($request);

You can retrieve the associative array when processing the request in
the Server.

::

	$parameters = $request->output_parameters();
	$name = $parameters[0]['name'];
	$size = $parameters[1]['size'];
	$shape = $parameters[1]['shape'];

データ型
========

According to the `XML-RPC spec <http://www.xmlrpc.com/spec>`_ there are
seven types of values that you can send via XML-RPC:

-  *int* or *i4*
-  *boolean*
-  *string*
-  *double*
-  *dateTime.iso8601*
-  *base64*
-  *struct* (contains array of values)
-  *array* (contains array of values)

******************
クラスリファレンス
******************

.. php:class:: CI_Xmlrpc

	.. php:method:: initialize([$config = array()])

		:param	array	$config: Configuration data
		:rtype:	void

		Initializes the XML-RPC library. Accepts an associative array containing your settings.

	.. php:method:: server($url[, $port = 80[, $proxy = FALSE[, $proxy_port = 8080]]])

		:param	string	$url: XML-RPC server URL
		:param	int	$port: Server port
		:param	string	$proxy: Optional proxy
		:param	int	$proxy_port: Proxy listening port
		:rtype:	void

		Sets the URL and port number of the server to which a request is to be sent::

			$this->xmlrpc->server('http://www.sometimes.com/pings.php', 80);

		Basic HTTP authentication is also supported, simply add it to the server URL::

			$this->xmlrpc->server('http://user:pass@localhost/', 80);

	.. php:method:: timeout($seconds = 5)

		:param	int	$seconds: Timeout in seconds
		:rtype:	void

		Set a time out period (in seconds) after which the request will be canceled::

			$this->xmlrpc->timeout(6);

	.. php:method:: method($function)

		:param	string	$function: Method name
		:rtype:	void

		Sets the method that will be requested from the XML-RPC server::

			$this->xmlrpc->method('method');

		Where method is the name of the method.

	.. php:method:: request($incoming)

		:param	array	$incoming: Request data
		:rtype:	void

		Takes an array of data and builds request to be sent to XML-RPC server::

			$request = array(array('My Photoblog', 'string'), 'http://www.yoursite.com/photoblog/');
			$this->xmlrpc->request($request);

	.. php:method:: send_request()

		:returns:	TRUE on success, FALSE on failure
		:rtype:	bool

		The request sending method. Returns boolean TRUE or FALSE based on success for failure, enabling it to be used conditionally.

	.. method set_debug($flag = TRUE)

		:param	bool	$flag: Debug status flag
		:rtype:	void

		Enables or disables debugging, which will display a variety of information and error data helpful during development.

	.. php:method:: display_error()

		:returns:	Error message string
		:rtype:	string

		Returns an error message as a string if your request failed for some reason.
		::

			echo $this->xmlrpc->display_error();

	.. php:method:: display_response()

		:returns:	Response
		:rtype:	mixed

		Returns the response from the remote server once request is received. The response will typically be an associative array.
		::

			$this->xmlrpc->display_response();

	.. php:method:: send_error_message($number, $message)

		:param	int	$number: Error number
		:param	string	$message: Error message
		:returns:	XML_RPC_Response instance
		:rtype:	XML_RPC_Response

		This method lets you send an error message from your server to the client.
		First parameter is the error number while the second parameter is the error message.
		::

			return $this->xmlrpc->send_error_message(123, 'Requested data not available');

	.. method send_response($response)

		:param	array	$response: Response data
		:returns:	XML_RPC_Response instance
		:rtype:	XML_RPC_Response

		Lets you send the response from your server to the client. An array of valid data values must be sent with this method.
		::

			$response = array(
				array(
					'flerror' => array(FALSE, 'boolean'),
					'message' => "Thanks for the ping!"
				),
				'struct'
			);

			return $this->xmlrpc->send_response($response);
