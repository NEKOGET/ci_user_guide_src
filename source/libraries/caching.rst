####################
キャッシングドライバ
####################

CodeIgniter は、いくつかのもっとも人気のある形式の高速な動的キャッシング
へのラッパーを提供します。ファイルベースのキャッシング以外は特定のサーバ
要件を必要とし、もし、サーバ要件が満たされない場合は致命的な例外が投げら
れます。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

******
使用例
******

The following example will load the cache driver, specify `APC <#alternative-php-cache-apc-caching>`_
as the driver to use, and fall back to file-based caching if APC is not
available in the hosting environment.

::

	$this->load->driver('cache', array('adapter' => 'apc', 'backup' => 'file'));

	if ( ! $foo = $this->cache->get('foo'))
	{
		echo 'Saving to the cache!<br />';
		$foo = 'foobarbaz!';

		// Save into the cache for 5 minutes
		$this->cache->save('foo', $foo, 300);
	}

	echo $foo;

**key_prefix** セッティングを通してキャッシュ・アイテム名に接頭辞を付けることもできます。
複数のアプリケーションを同じ環境で実行しているとき、名前の衝突を避けることに役立ちます。

::

	$this->load->driver('cache',
		array('adapter' => 'apc', 'backup' => 'file', 'key_prefix' => 'my_')
	);

	$this->cache->get('foo'); // Will get the cache entry named 'my_foo'

******************
クラスリファレンス
******************

.. php:class:: CI_Cache

	.. php:method:: is_supported($driver)

		:パラメータ	string	$driver: キャッシング・ドライバの名前
		:返り値:	対応できればTRUE、そうでなければFALSEを返します。
		:返り値型:	bool

		このメソッドは、``$this->cache->get()`` 経由でドライバにアクセスする際に、
		自動的に呼び出されます。しかしながら、もし、個別のドライバを使用する
		場合、このメソッドを呼び出し、そのドライバがホスト環境でサポートされ
		ているか確かめてください。
		::

			if ($this->cache->apc->is_supported())
			{
				if ($data = $this->cache->apc->get('my_cache'))
				{
					// do things.
				}
			}

	.. php:method:: get($id)

		:パラメータ	string	$id: キャッシュ・アイテム名
		:返り値:	アイテムが存在しない場合、FALSE を返します。
		:返り値型:	mixed

		このメソッドはキャッシュから1つのアイテムを取得することを試みます。
		もしそのアイテムが存在しない場合、このメソッドは FALSE を返します。
		::

			$foo = $this->cache->get('my_cached_item');

	.. php:method:: save($id, $data[, $ttl = 60[, $raw = FALSE]])

		:パラメータ	string	$id: キャッシュ・アイテム名
		:パラメータ	mixed	$data: 保存するデータ
		:パラメータ	int	$ttl: Time To Live のデフォルトは 60 秒です。
		:パラメータ	bool	$raw: 元の値を保存するべきかどうか
		:返り値:	成功時　TRUE、 失敗時　FALSE
		:返り値型:	string

		このメソッドはキャッシュに1つのアイテムを保存します。
		もし、保存に失敗した場合、このメソッドは FALSE を返します。
		::

			$this->cache->save('cache_item_id', 'data_to_cache');

		.. note:: ``$raw`` パラメータは、APCとMemcacheによって増加``increment()``と
                        漸減``decrement()``に適切に使用されます。

	.. php:method:: delete($id)

		:パラメータ	string	$id: キャッシュされたアイテムの名前
		:返り値:	成功時 TRUE、失敗時 FALSE
		:返り値型:	bool

		このメソッドは特定の1つのアイテムをキャッシュから削除します。
		もし、削除に失敗した場合、このメソッドは FALSE を返します。
		::

			$this->cache->delete('cache_item_id');

	.. php:method:: increment($id[, $offset = 1])

		:パラメータ	string	$id: キャッシュID
		:パラメータ	int	$offset: 値に進行上の数値を追加する
		:返り値:	新しい値が保存されたら成功、失敗時　FALSE 
		:返り値型:	mixed

		元の保存された値に極小の増加を実行します。
		::

			// 'iterator' は2の値を持っています

			$this->cache->increment('iterator'); // 'iterator' は3です。

			$this->cache->increment('iterator', 3); // 'iterator' は6です

	.. php:method:: decrement($id[, $offset = 1])

		:パラメータ	string	$id: キャッシュID
		:パラメータ	int	$offset: S値に進行上の数値を減算する
		:返り値:	新しい値が保存されたら成功、失敗時　FALSE
		:返り値型:	mixed

		元の保存された値に極小の減算を実行します。
		::

			// 'iterator' の値は 6です

			$this->cache->decrement('iterator'); // 'iterator' は 5です。

			$this->cache->decrement('iterator', 2); // 'iterator' は 3です。

	.. php:method:: clean()

		:返り値:	成功時　TRUE、失敗時　FALSE
		:返り値型:	bool

		このメソッドはキャッシュ全体をクリアします。もしキャッシュファイルの
		削除に失敗した場合、このメソッドは FALSE を返します。
		::

			$this->cache->clean();

	.. php:method:: cache_info()

		:返り値:	全キャッシュ・データベースの情報
		:返り値型:	mixed

		このメソッドはキャッシュ全体の情報を返します。
		::

			var_dump($this->cache->cache_info());

		.. note:: The information returned and the structure of the data is dependent
			on which adapter is being used.

	.. php:method:: get_metadata($id)

		:パラメータ	string	$id: キャッシュ・アイテム名
		:返り値:	キャッシュアイテムのメタデータ
		:返り値型:	mixed

		このメソッドは、キャッシュの中の特定の1つのアイテムに
		関する詳細な情報を返します。
		::

			var_dump($this->cache->get_metadata('my_cached_item'));

		.. note:: 情報はデータの構造がどのアダプターが使われているか
			依存した上で返ります。

********
ドライバ
********

Alternative PHP Cache (APC) キャッシング
========================================

上記で記載されたすべてのメソッドは、以下のように特定のアダプタを
ドライバのローダーに渡すことなくアクセスできます::

	$this->load->driver('cache');
	$this->cache->apc->save('foo', 'bar', 10);

APC に関するさらなる情報は、`http://php.net/apc <http://php.net/apc>`_
を参照してください。

ファイルベースキャッシュ
========================

出力クラスのキャッシングとは異なり、ファイルベースのキャッシングドライバ
は、ビューファイルの一部分をキャッシュできます。アプリケーションをベンチ
マークしながら、キャッシングによる効果をディスク I/O が打ち消してしまう
ポイントに到達しないよう注意して使用してください。

上記で記載されたすべてのメソッドは、以下のように特定のアダプタを
ドライバのローダーに渡すことなくアクセスできます::

	$this->load->driver('cache');
	$this->cache->file->save('foo', 'bar', 10);

Memcached キャッシュ
====================

複数の Memcached サーバは、application/config/ ディレクトリに
置いた memcached.php 設定ファイルで指定することができます。 

上記で記載されたすべてのメソッドは、以下のように特定のアダプタ
をドライバのローダーに渡すことなくアクセスできます::

	$this->load->driver('cache');
	$this->cache->memcached->save('foo', 'bar', 10);

Memcached に関するさらなる情報は、`http://php.net/memcached <http://php.net/memcached>`_
を参照してください。

WinCache キャッシュ
===================

Windowsの下で、WinCacheドライバを利用することもできます。

上記で記載されたすべてのメソッドは、以下のように特定のアダプタ
をドライバのローダーに渡すことなくアクセスできます::

	$this->load->driver('cache');
	$this->cache->wincache->save('foo', 'bar', 10);

WinCache, に関するさらなる情報は、`http://php.net/wincache <http://php.net/wincache>`_.
を参照してください。

Redis キャッシュ
================

Redis is an in-memory key-value store which can operate in LRU cache mode. 
To use it, you need `Redis server and phpredis PHP extension <https://github.com/phpredis/phpredis>`_.

redisサーバーに接続する構成オプションは、application/config/redis.phpファイルに格納されなければなりません。
利用できるオプション::
	
	$config['socket_type'] = 'tcp'; //`tcp` または `unix`
	$config['socket'] = '/var/run/redis.sock'; // `unix`ソケットタイプの場合
	$config['host'] = '127.0.0.1';
	$config['password'] = NULL;
	$config['port'] = 6379;
	$config['timeout'] = 0;

上記で記載されたすべてのメソッドは、以下のように特定のアダプタ
をドライバのローダーに渡すことなくアクセスできます::

	$this->load->driver('cache');
	$this->cache->redis->save('foo', 'bar', 10);

に関するさらなる情報は、`http://redis.io <http://redis.io>`_.
を参照してください。

ダミー キャッシュ
=================

これは絶えず 'miss' するキャッシングバックエンドです。データをストアし
ませんが、指定したキャッシュをサポートしない環境で、キャッシュを使用す
るコードをそのままにできます。
