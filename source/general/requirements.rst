###################
サーバ要件
###################

`PHP <http://php.net/>`_ のバージョンは5.5以降が推奨されます。

It should work on 5.2.4 as well, but we strongly advise you NOT to run
such old versions of PHP, because of potential security and performance
issues, as well as missing features.

データベースはほとんどのWebアプリケーションプログラムで必要とされます。
現在サポートしているデータベースは次の通りです:

  - *mysql* (非推奨) および *mysqli* と *pdo* ドライバを利用しての MySQL (5.1+)
  - *oci8* と *pdo* ドライバを利用しての Oracle
  - *postgre* と *pdo* ドライバを利用しての PostgreSQL
  - *mssql* 、 *sqlsrv* (バージョン 2005 以上のみ ) と  *pdo* ドライバを利用しての MS SQL
  - *sqlite* (version 2)、 *sqlite3* (version 3) と  *pdo* ドライバを利用しての SQLite
  - *cubrid* と *pdo* ドライバを利用しての CUBRID
  - *ibase* と *pdo* ドライバを利用しての Interbase/Firebird
  - *odbc* と *pdo* ドライバを利用してのODBC  (you should know that ODBC is actually an abstraction layer)is actually an abstraction layer)
