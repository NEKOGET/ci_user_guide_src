##################
補助的なクラス作成
##################

いくつかのケースでは、クラスをコントローラから切り離して作りつつも
CodeIgniter のあらゆるリソースを利用したい場合があります。
それはつぎに示すように、簡単です。

get_instance()
==============

.. php:function:: get_instance()

	:returns:	コントローラのインスタンスへの参照
	:rtype:	CI_Controller

**コントローラのメソッド内でインスタンス化するクラスはすべて
CodeIgniter のネイティブリソースにアクセスすることができます** 、シンプルに
``get_instance()`` 関数を利用しましょう。この関数はメインの
CodeIgniter オブジェクトを返します。

通常、 CodeIgniter の利用可能なメソッドを呼び出すには
``$this`` 構文を使用する必要があります::

	$this->load->helper('url');
	$this->load->library('session');
	$this->config->item('base_url');
	// など。

しかしながら ``$this`` はこれはコントローラ内、モデル内、
ビュー内で使用する場合にだけ動作します。独自のカスタムクラス内から CodeIgniter
のクラスを使用したい場合は、以下のようにできます:

まず、変数に CodeIgniter のオブジェクトを割り当てます::

	$CI =& get_instance();

オブジェクトを変数に割り当てたなら、
``$this`` の *かわりに* その変数を使用します::

	$CI =& get_instance();

	$CI->load->helper('url');
	$CI->load->library('session');
	$CI->config->item('base_url');
	// など。

もし別のクラス内で ``get_instance()`` を使うなら、
プロパティに割り当てるのが良いでしょう。そうすればそれぞれのメソッドで
``get_instance()`` を呼び出す必要がなくなります。

例::

	class Example {

		protected $CI;

		// プロパティ定義では関数を直接呼び出せませんので
		// コンストラクタを使います。
		public function __construct()
		{
			// CodeIgniter のスーパーオブジェクトを割り当てます
			$this->CI =& get_instance();
		}

		public function foo()
		{
			$this->CI->load->helper('url');
			redirect();
		}

		public function bar()
		{
			$this->CI->config->item('base_url');
		}
	}

上記の例では ``foo()`` と ``bar()``
のそれぞれで
``get_instance()`` を呼び出すことなく動作します。
