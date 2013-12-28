######################
CodeIgniter ユーザーガイド
######################

******************
セットアップ手順
******************

CodeIgniterのユーザガイドは、ドキュメントを管理するためにスフィンクスを使用し、
様々なフォーマットへの出力します。 ページは人間が読める
'ReStructured Textフォーマット<http://sphinx.pocoo.org/rest.html>'で書かれています

前提条件
=============

SphinxはPythonが必要です。OSXの場合は既にインストールされています。
You can confirm in a Terminal window by executing the ``python`` command
without any parameters.  It should load up and tell you which version you have
installed.  If you're not on 2.7+, go ahead and install 2.7.2 from
http://python.org/download/releases/2.7.2/

インストール
============

1. Install `easy_install <http://peak.telecommunity.com/DevCenter/EasyInstall#installing-easy-install>`_
2. ``easy_install sphinx``
3. ``easy_install sphinxcontrib-phpdomain``
4. Install the CI Lexer which allows PHP, HTML, CSS, and JavaScript syntax highlighting in code examples (see *cilexer/README*)
5. ``cd user_guide_src``
6. ``make html``

ドキュメントの編集と作成
==================================

All of the source files exist under *source/* and is where you will add new
documentation or modify existing documentation.  Just as with code changes,
we recommend working from feature branches and making pull requests to
the *develop* branch of this repo.

HTMLは？
====================

Obviously, the HTML documentation is what we care most about, as it is the
primary documentation that our users encounter.  Since revisions to the built
files are not of value, they are not under source control.  This also allows
you to regenerate as necessary if you want to "preview" your work.  Generating
the HTML is very simple.  From the root directory of your user guide repo
fork issue the command you used at the end of the installation instructions::

	make html

You will see it do a whiz-bang compilation, at which point the fully rendered
user guide and images will be in *build/html/*.  After the HTML has been built,
each successive build will only rebuild files that have changed, saving
considerable time.  If for any reason you want to "reset" your build files,
simply delete the *build* folder's contents and rebuild.

***************
ガイドラインスタイル
***************

CodeIgniterのを文書化するためにスフィンクスを使用しています。
一般的なガイドラインについては、 source/documentation/index.rstを参照してください。
