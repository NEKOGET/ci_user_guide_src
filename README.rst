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
ターミナルで、``python``とコマンドを打ち込む事でインストールされているか確認する事ができます。
パラメータを指定しない事で、どのバージョンであるかを教えてくれます。
2.7+でない場合は、次から2.7.2をインストールしてください
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

すべてのソース·ファイルは、*source/*の下に存在し、
新しいドキュメントを追加したり修正します。
コードの変更と同様に、マニュアルまたは既存のドキュメントを変更します。
私たちはfeature branchesで作業してに*develop*ブランチにpull requestsを送る事をお勧めします。

HTMLは？
====================

HTMLのドキュメントは私たちユーザーが遭遇する主要なドキュメントであり、
私たちの最も関心のあるものであることは明らかです。
改訂される前にビルドされたファイルはソース管理下に存在する価値がありません。
それはまた、あなたが"プレビュー"を求める時、必要に応じて、再生成することができます。
HTMLはとてもシンプルです。  ユーザーガイドのレポジトリのルートディレクトリからフォークコマンドを発行します。
それはインストール手順の最後に書かれています::


	make html

それは目を見張るようなコンパイルを行います。
レンダリングされたユーザガイドとイメージは*build/html/*になります。 HTMLが構築された後、
連続した各ビルドは変更されたファイルを再構築し、かなりの時間を節約することができます。
もし何らかの理由で、ビルドファイルを「リセット」したい場合は
単に*build* フォルダの内容を削除して再構築します。

***************
ガイドラインスタイル
***************

CodeIgniterのを文書化するためにスフィンクスを使用しています。
一般的なガイドラインについては、 source/documentation/index.rstを参照してください。
