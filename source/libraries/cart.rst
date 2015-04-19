###################
ショッピングカートクラス
###################

カートクラスを使うと、ユーザがサイトを閲覧中に有効なセッションに、
商品を追加できます。 これらの商品は、カートから読みだすことができ、
ユーザが数量を変更したり、カートから商品を削除したりできる、よくある 
"ショッピングカート" の体裁に表示させることができます。

.. important:: カードライブラリは廃止され、使用すべきではありません。
現時点では、後方互換性のためだけに保持されます。

カートクラスは、"カート"のコアの機能「だけ」を提供するものであること
に注意してください。出荷、クレジットカードの有効性確認やその他の処理
コンポーネントは提供しません。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

********************
カートクラスを使う
********************

ショッピングカートクラスを初期化する
====================================

.. important:: カートクラスは、データベースに情報を保存するために
CodeIgniter の セッションクラス を利用しますので、カートク
ラスを利用する前に、セッションの説明で示しているように、
データベーステーブルをセットアップする必要があります。
また、データベースを利用するために、application/config/config.php 
ファイルの:doc:`セッション <sessions>`の設定項目を設定してください。

ショッピングカートクラスをコントローラのコンストラクタ
で初期化するには、$this->load->library メソッドを利用します:

	$this->load->library('cart');

ロードされると、カートオブジェクトが利用可能になります: $this->cart

	$this->cart

.. note:: カートクラスは、セッションクラスを自動的に読み込んで初期化します。
アプリケーションの中で、セッションをまだどこでも使っていなかったとしても、
セッションクラスをロードする必要はありません。

カートに商品を追加する
==========================

ショッピングカートに商品を追加するには、
下記のように ``$this->cart->insert()`` メソッドに、商品情報の配列を
渡すだけです::

	$data = array(
		'id'      => 'sku_123ABC',
		'qty'     => 1,
		'price'   => 39.95,
		'name'    => 'T-Shirt',
		'options' => array('Size' => 'L', 'Color' => 'Red')
	);

	$this->cart->insert($data);

.. important:: 上記の最初の4つのインデックス(id, qty, price, および name)
は **必須**です. ひとつでも省略した場合は、データはカートに保存されません。
5番目のインデックス (options) は、任意の項目です。この項目は、その商品
に関連するオプションがある場合に使われるのを想定しています。上で示され
ているように、options には、配列を使用してください。

5つの既定のインデックスは次のとおりです:

-  **id** - 店舗の各商品は、一意識別子を持っている必要があります。
一般的には、"sku" や、その他の識別子になります。
-  **qty** - 購入する数量。
-  **price** - 商品の価格。
-  **name** - 商品の名前。
-  **options** - 商品を識別するために必要なその他のすべての属性。
これらは、配列として渡す必要があります。

上の5つのインデックスに加え、2つの予約語: rowid と subtotal 
があります。これらは、カートクラスの内部で使用されます。 そ
れらの語をカートにデータを追加する際のインデックス名には使用
しないでください。

配列には、追加のデータを含むことができます。配列に追加したど
んなデータも、セッションに保存されます。とはいえ、 表にした
ときに情報を表示しやすいよう、すべての商品で使われるデータを
標準化するのが、一番良い方法です。

::

	$data = array(
		'id'      => 'sku_123ABC',
		'qty'     => 1,
		'price'   => 39.95,
		'name'    => 'T-Shirt',
		'coupon'	 => 'XMAS-50OFF'
	);

	$this->cart->insert($data);

あなたが正常に挿入した場合、 ``insert()`` メソッドは、
単一の項目として$ROWIDを返します。

カートに複数の商品を追加する
=================================

下記に示したように、多次元配列を使うことによって、一度の
アクションで複数の商品をカートに追加することができます。 
これは、同じページの中で、複数の商品を選ばせたいときな
どに有用です。

::

	$data = array(
		array(
			'id'      => 'sku_123ABC',
			'qty'     => 1,
			'price'   => 39.95,
			'name'    => 'T-Shirt',
			'options' => array('Size' => 'L', 'Color' => 'Red')
		),
		array(
			'id'      => 'sku_567ZYX',
			'qty'     => 1,
			'price'   => 9.95,
			'name'    => 'Coffee Mug'
		),
		array(
			'id'      => 'sku_965QRS',
			'qty'     => 1,
			'price'   => 29.95,
			'name'    => 'Shot Glass'
		)
	);

	$this->cart->insert($data);

カートを表示する
===================

カートを表示するには、下のようなコードの :doc:`ビューファイル
 </general/views>` を作成します。

この例では、:doc:`フォームヘルパー </helpers/form_helper>` を
利用しているのを注意してください。

::

	<?php echo form_open('path/to/controller/update/method'); ?>

	<table cellpadding="6" cellspacing="1" style="width:100%" border="0">

	<tr>
		<th>数量</th>
		<th>商品説明</th>
		<th style="text-align:right">価格</th>
		<th style="text-align:right">小計</th>
	</tr>

	<?php $i = 1; ?>

	<?php foreach ($this->cart->contents() as $items): ?>

		<?php echo form_hidden($i.'[rowid]', $items['rowid']); ?>

		<tr>
			<td><?php echo form_input(array('name' => $i.'[qty]', 'value' => $items['qty'], 'maxlength' => '3', 'size' => '5')); ?></td>
			<td>
				<?php echo $items['name']; ?>

				<?php if ($this->cart->has_options($items['rowid']) == TRUE): ?>

					<p>
						<?php foreach ($this->cart->product_options($items['rowid']) as $option_name => $option_value): ?>

							<strong><?php echo $option_name; ?>:</strong> <?php echo $option_value; ?><br />

						<?php endforeach; ?>
					</p>

				<?php endif; ?>

			</td>
			<td style="text-align:right"><?php echo $this->cart->format_number($items['price']); ?></td>
			<td style="text-align:right">$<?php echo $this->cart->format_number($items['subtotal']); ?></td>
		</tr>

	<?php $i++; ?>

	<?php endforeach; ?>

	<tr>
		<td colspan="2"> </td>
		<td class="right"><strong>Total</strong></td>
		<td class="right">$<?php echo $this->cart->format_number($this->cart->total()); ?></td>
	</tr>

	</table>

	<p><?php echo form_submit('', 'カートを更新する'); ?></p>

カートを更新する
=================

カートの情報を更新するには、Row ID と数量
を含む配列を``$this->cart->update()`` メソッド
に渡す必要があります

.. note:: 数量をゼロにセットした場合、その商品は、カートから
削除されます。

::

	$data = array(
		'rowid' => 'b99ccdf16028f015540f341130b6d8ec',
		'qty'   => 3
	);

	$this->cart->update($data);

	//  または、多次元配列

	$data = array(
		array(
			'rowid'   => 'b99ccdf16028f015540f341130b6d8ec',
			'qty'     => 3
		),
		array(
			'rowid'   => 'xw82g9q3r495893iajdh473990rikw23',
			'qty'     => 4
		),
		array(
			'rowid'   => 'fh4kdkkkaoe30njgoe92rkdkkobec333',
			'qty'     => 2
		)
	);

	$this->cart->update($data);

また、以前に定義した任意のプロパティ、オプションとして価格やその他の
カスタムフィールドとしてのアイテムを挿入し更新することができます。

::

	$data = array(
		'rowid'  => 'b99ccdf16028f015540f341130b6d8ec',
		'qty'    => 1,
		'price'	 => 49.95,
		'coupon' => NULL
	);

	$this->cart->update($data);

Row ID とは?
*****************

row ID は、商品がカートに追加される際に、カートのコード
で生成される一意識別子です。 一意識別子が生成される理由
は、異なるオプションを持つ同一の商品をカートで管理できる
ようにするためです。

たとえば、ある人が、サイズが異なる以外は同じ、2つのTシャツ
(同じ商品 ID )を購入するとするとします。 商品 ID (およびその他の属性)
は、同一のシャツなので、2つのサイズのシャツは同じものです。違うのは
サイズだけです。2つのサイズのシャツを別々に取り扱うことができるよう、
カートは、この違いを識別する方法を持っていなければなりません。カート
は、一意の "row ID"を商品 ID と関連するオプションを元に生成すること
で、違いを識別します。

ほとんどの場合、カートが更新されるのは、"カートの中身" のようなページで
ユーザが操作する事によってなされますので、 開発者のように、 "row ID" の
事をいつも考慮しなければならないというようなことは、考えられません。
ですので、"カートの中身" のページにこの情報を隠しフォームフィールドで確
実に保管させ、 更新フォームが送信されたときに、それを確実に update メソッド
に渡すようにしてください。より多くの情報を得るために、上の"カートの中身" 
のページの作成を試してみてください。


***************
クラスリファレンス
***************

.. class:: CI_Cart

	.. attribute:: $product_id_rules = '\.a-z0-9_-'

		デフォルトで、英数字、ダッシュ、アンダースコア、ピリオド - 
		これらは、製品IDを検証するために使用する正規表現ルールです

	.. attribute:: $product_name_rules	= '\w \-\.\:'

		デフォルトで 英数字、ダッシュ、アンダースコア、コロン、ピリオド
		これらは、製品IDと製品名を検証するために使用する正規表現ルールです

	.. attribute:: $product_name_safe = TRUE

		唯一で安全な製品名を判定する。 デフォルトでTRUE。


	.. method:: insert([$items = array()])

		:パラメータ	array	$items: カートに挿入する項目
		:返り値:	成功時　TRUE  失敗時　FALSE 
		:返り値型:	bool

		カートに項目を挿入しセッションテーブルに保存します。
		成功時TRUE、失敗した場合FALSEを返します


	.. method:: update([$items = array()])

		:パラメータ	array	$items: カートのアイテムを更新する
		:返り値:	成功時　TRUE、失敗時　FALSE 
		:返り値型:	bool

		このメソッドは、指定された項目のプロパティを変更することが可能です。
		数量の変更を加える場合、通常チェックアウトの前に「カートを見る」ページ
		から呼び出されます。その配列は、各項目のROWIDが含まれている必要が
		あります。

	.. method:: remove($rowid)

		:パラメータ	int	$rowid: アイテムのIDをショッピングカートから削除する
		:返り値:	成功時　TRUE、失敗時　FALSE 
		:返り値型:	bool

		`` $rowid``を渡すことでショッピングカートからアイテムを削除すること
		ができます。

	.. method:: total()

		:返り値:	合計金額
		:返り値型:	int

		カート内の合計金額が表示されます。


	.. method:: total_items()

		:返り値:	カート内のアイテムの合計額
		:返り値型:	int

		カート内のアイテムの合計数を表示します。


	.. method:: contents([$newest_first = FALSE])

		:パラメータ	bool	$newest_first: Whether to order the array with newest items first
		:返り値:	An array of cart contents
		:返り値型:	array

		カート内のすべてのものを含む配列を返します。
		あなたは返された配列、新から旧へまたは
		旧から新へ並べ替え合格した内容の順序を
		並べ替えることができます

	.. method:: get_item($row_id)

		:パラメータ	int	$row_id: Row ID の所得 
		:返り値:	アイテムデータの配列
		:返り値型:	array

		指定された行のIDと一致する項目の配列を含むデータを返し、
		またはそのような項目が存在しない場合はFALSEを返します。

	.. method:: has_options($row_id = '')

		:パラメータ	int	$row_id: Row ID の検査
		:返り値:	オプションが存在する場合TRUE、それ以外の場合はFALSE
		:返り値型:	bool

		カート内の特定の行がオプションが含まれている場合はTRUE（ブール値）を返します。
		このメソッドは、rowid を渡す必要があるので、カートを表示する の例
		で示すように、ループの中で、$this->cart->contents() と一緒に使われ
		るのを想定しています。

	.. method:: product_options([$row_id = ''])

		:パラメータ	int	$row_id: Row ID
		:返り値:	製品のオプションの配列
		:返り値型:	array

		特定の商品のオプションの配列を返します。このメソッドは、r
		owid を渡す必要があるので、カートを表示する の例で示すよう
		に、ループの中で、$this->cart->contents() と一緒に使われる
		のを想定しています。

	.. method:: destroy()

		:返り値型: void

		カートを破棄します。
		このメソッドは、顧客の注文が完了した際などに呼ばれます。
