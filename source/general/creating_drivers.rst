##############
ドライバの作成
##############

ドライバのディレクトリとファイルの構造
======================================

ドライバのディレクトリとファイル構造のレイアウトサンプル:

-  /application/libraries/Driver_name

   -  Driver_name.php
   -  drivers

      -  Driver_name_subclass_1.php
      -  Driver_name_subclass_2.php
      -  Driver_name_subclass_3.php

.. note:: 大文字と小文字が区別されるファイルシステム上で互換性を維持するためには、
	Driver_name ディレクトリは
	``ucfirst()`` により返される形式で指定する必要があります。

.. note:: ドライバライブラリのアーキテクチャでは、
	サブクラスは継承をしていません。
	そのため、メインドライバのプロパティやメソッドを引き継ぎません。