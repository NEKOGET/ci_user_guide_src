######################
マイグレーションクラス
######################

マイグレーション（訳注：プログラムを使ったデータベーススキーマ変更）は、
お使いのデータベースを構造的かつ組織的に変更するために役に立つしくみです。
SQL をちょこちょこと手でいじることもできなくはないでしょうが、
他の開発者にも同じ変更作業をしてもらわなければなりません。
また、次回本番マシンに対して変更作業を行う際、
どの変更処理を適用しないといけないかを常に把握しておく必要があります。

データベーステーブルの **マイグレーション** は、過去にどのマイグレーションが
行われたのかを追跡しており、アプリケーションのファイルを更新した後に
``$this->migration->current()`` を呼ぶだけでどのマイグレーションを実行
すればよいのかをうまく解決してくれます。

マイグレーション処理の現在のバージョンは **application/config/migration.php** 
にあります。

.. contents::
  :local:

.. raw:: html

  <div class="custom-index container"></div>

**************************
マイグレーションファイル名
**************************

それぞれのマイグレーションは、渡されたメソッドによって、数字の順番で順方向
（ロールフォワード）もしくは逆方向（ロールバック）に実行されます。
２種類の付番スタイルが利用できます：

* **連番** それぞれのマイグレーションは、作成した順に **001** から付番します。
  番号は３桁の連番であり、欠番があってはなりません。
  （これは CideIgniter 3.0 以前の付番スキームでした）

* **タイムスタンプ** それぞれのマイグレーションは、マイグレーションが作成された
  時点のタイムスタンプを使って **YYYYMMDDHHIISS** というフォーマットで付番
  します（例： **20121031100537** ）。これにより、チーム環境で作業している
  際に番号が衝突するのを防いでいます。これは CodeIgniter 3.0 以降で推奨される
  方法です。

お手元の *application/config/migration.php* ファイルの中の
``$config['migration_type']`` 設定により、どちらかの方法を選択します。

どちらを選択した場合でも、マイグレーションファイル名はマイグレーション番号に
続いてアンダースコアとマイグレーションの内容の説明文としてください。
たとえば以下のようになります:

* 001_add_blog.php (連番による付番)
* 20121031100537_add_blog.php (タイムスタンプによる付番)

**********************
マイグレーションの作成
**********************

これはブログ機能を持つ新しいサイトのための、最初のマイグレーションです。
すべてのマイグレーションには *20121031100537_add_blog.php* といった名前を付けて、
**application/migrations/** に置いておきます。
::

	<?php

	defined('BASEPATH') OR exit('No direct script access allowed');

	class Migration_Add_blog extends CI_Migration {

		public function up()
		{
			$this->dbforge->add_field(array(
				'blog_id' => array(
					'type' => 'INT',
					'constraint' => 5,
					'unsigned' => TRUE,
					'auto_increment' => TRUE
				),
				'blog_title' => array(
					'type' => 'VARCHAR',
					'constraint' => '100',
				),
				'blog_description' => array(
					'type' => 'TEXT',
					'null' => TRUE,
				),
			));
			$this->dbforge->add_key('blog_id', TRUE);
			$this->dbforge->create_table('blog');
		}

		public function down()
		{
			$this->dbforge->drop_table('blog');
		}
	}

その後、 **application/config/migration.php** の中で
``$config['migration_version'] = 20121031100537;`` のように指定します。

******
使用例
******

この例ではいくつかのシンプルなコードを **application/controllers/Migrate.php**
に置いて、スキーマをアップデートします。
::

	<?php
	
	class Migrate extends CI_Controller
	{

		public function index()
		{
			$this->load->library('migration');

			if ($this->migration->current() === FALSE)
			{
				show_error($this->migration->error_string());
			}
		}

	}

********************
マイグレーション設定
********************

マイグレーションで指定できる設定オプションは以下の通りです。

========================== ====================== ========================== =============================================
設定項目                   初期値                 オプション                 説明
========================== ====================== ========================== =============================================
**migration_enabled**      FALSE                  TRUE / FALSE               マイグレーションを実行するかどうか
**migration_path**         APPPATH.'migrations/'  None                       マイグレーションフォルダのパス
**migration_version**      0                      None                       使用するべきデータベースのバージョン番号
**migration_table**        migrations             None                       スキーマのバージョン番号を保持するテーブル名
**migration_auto_latest**  FALSE                  TRUE / FALSE               自動的にマイグレーションを実行するかどうか
**migration_type**         'timestamp'            'timestamp' / 'sequential' マイグレーションファイル名の識別部分の数値タイプ
========================== ====================== ========================== =============================================

******************
クラスリファレンス
******************

.. php:class:: CI_Migration

	.. php:method:: current()

		:戻り値:	マイグレーションが見つからない場合は TRUE、
			成功した場合現在のバージョン番号、FALSE.失敗
		:戻り値の型:	mixed
		:説明: （ *application/config/migration.php* 中の ``$config['migration_version']`` 
			の値にかかわらず）現在のバージョンまでマイグレート（追随）する。

	.. php:method:: error_string()

		:戻り値:	エラーメッセージ文字列
		:戻り値の型:	string
		:説明: マイグレーション実行中に検出したエラーの文字列を返す

	.. php:method:: find_migrations()

		:戻り値:	マイグレーションファイル名の配列
		:戻り値の型:	array
		:説明: **migration_path** プロパティで見つかったマイグレーションファイル名を
			配列として返す

	.. php:method:: latest()

		:戻り値:	成功した場合、現在のバージョン文字列。失敗したら FALSE
		:戻り値の型:	mixed
		:説明: これは ``current()`` と同じように動作するが、
			``$config['migration_version']`` を参照する代わりに、
			ファイルシステム内で最も新しいマイグレーションクラスを使用する。

	.. php:method:: version($target_version)

		:パラメータ:	mixed	$target_version: 処理するマイグレーションのバージョン
		:戻り値:	マイグレーションが見つからない場合は TRUE、成功した場合現在のバージョン番号、FALSE.失敗
		:戻り値の型:	mixed
		:説明: 変更をロールバックする場合、もしくはロールフォワードをプログラム的に
			順番に行う場合のターゲットバージョン。 ``current()`` と同じように動作するが
			``$config['migration_version']`` を参照しない。 ::

			$this->migration->version(5);
