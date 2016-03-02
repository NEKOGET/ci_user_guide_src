###################################
ビューファイルに対するPHP代替の構文
###################################

If you do not utilize CodeIgniter's :doc:`template
engine <../libraries/parser>`, you'll be using pure PHP in your
View files. To minimize the PHP code in these files, and to make it
easier to identify the code blocks it is recommended that you use PHPs
alternative syntax for control structures and short tag echo statements.
If you are not familiar with this syntax, it allows you to eliminate the
braces from your code, and eliminate "echo" statements.

自動ショートタグのサポート
===========================

.. note:: If you find that the syntax described in this page does not
	work on your server it might be that "short tags" are disabled in your
	PHP ini file. CodeIgniter will optionally rewrite short tags on-the-fly,
	allowing you to use that syntax even if your server doesn't support it.
	This feature can be enabled in your *config/config.php* file.

Please note that if you do use this feature, if PHP errors are
encountered in your **view files**, the error message and line number
will not be accurately shown. Instead, all errors will be shown as
``eval()`` errors.

echoのかわりになるもの
=================

通常、変数をプリントアウトする場合はechoもしくはこの操作を行います::

	<?php echo $variable; ?>

代替構文を使用すると、このように行うことができます::

	<?=$variable?>

代替の制御構造
==============================

if、 for、foreachと while といった制御構造について簡略化した形式で記述することができます。
これは ``foreach`` を使用した場合の例です::

	<ul>

	<?php foreach ($todo as $item): ?>

		<li><?=$item?></li>

	<?php endforeach; ?>

	</ul>

中括弧がないことに注意してください。 Instead, the end brace is replaced with
``endforeach``. Each of the control structures listed above has a similar
closing syntax: ``endif``, ``endfor``, ``endforeach``, and ``endwhile``

Also notice that instead of using a semicolon after each structure
(except the last one), there is a colon. This is important!

``if``/``elseif``/``else`` を使用した別の例です。コロンに注意してください::

	<?php if ($username === 'sally'): ?>

		<h3>Hi Sally</h3>

	<?php elseif ($username === 'joe'): ?>

		<h3>Hi Joe</h3>

	<?php else: ?>

		<h3>Hi unknown user</h3>

	<?php endif; ?>
