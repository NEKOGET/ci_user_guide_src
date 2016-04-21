######
予約語
######

手助けとするため、 CodeIgniter はその処理において一連の関数、メソッド、
クラス、変数を使用しています。
このため、いくつかの名前は開発者による使用ができません。
以下は、使用できない予約語のリストです。

コントローラ名
--------------

あなたのコントローラクラスはメインのアプリケーションコントローラを継承しますので、
親クラスで使っているメソッド名と同一の名前をつけないように注意する必要があります。
さもなくばあなたの関数がそれらをオーバーライドしてしまいます。
以下は予約語の一覧です。
これらのいずれもコントローラの名前としないでください:

-  CI_Controller
-  Default
-  index

関数
----

-  :php:func:`is_php()`
-  :php:func:`is_really_writable()`
-  ``load_class()``
-  ``is_loaded()``
-  ``get_config()``
-  :php:func:`config_item()`
-  :php:func:`show_error()`
-  :php:func:`show_404()`
-  :php:func:`log_message()`
-  :php:func:`set_status_header()`
-  :php:func:`get_mimes()`
-  :php:func:`html_escape()`
-  :php:func:`remove_invisible_characters()`
-  :php:func:`is_https()`
-  :php:func:`function_usable()`
-  :php:func:`get_instance()`
-  ``_error_handler()``
-  ``_exception_handler()``
-  ``_stringify_attributes()``

変数
----

-  ``$config``
-  ``$db``
-  ``$lang``

定数
----

-  ENVIRONMENT
-  FCPATH
-  SELF
-  BASEPATH
-  APPPATH
-  VIEWPATH
-  CI_VERSION
-  MB_ENABLED
-  ICONV_ENABLED
-  UTF8_ENABLED
-  FILE_READ_MODE
-  FILE_WRITE_MODE
-  DIR_READ_MODE
-  DIR_WRITE_MODE
-  FOPEN_READ
-  FOPEN_READ_WRITE
-  FOPEN_WRITE_CREATE_DESTRUCTIVE
-  FOPEN_READ_WRITE_CREATE_DESTRUCTIVE
-  FOPEN_WRITE_CREATE
-  FOPEN_READ_WRITE_CREATE
-  FOPEN_WRITE_CREATE_STRICT
-  FOPEN_READ_WRITE_CREATE_STRICT
-  SHOW_DEBUG_BACKTRACE
-  EXIT_SUCCESS
-  EXIT_ERROR
-  EXIT_CONFIG
-  EXIT_UNKNOWN_FILE
-  EXIT_UNKNOWN_CLASS
-  EXIT_UNKNOWN_METHOD
-  EXIT_USER_INPUT
-  EXIT_DATABASE
-  EXIT__AUTO_MIN
-  EXIT__AUTO_MAX