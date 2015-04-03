#####################
Model-View-Controller
#####################

CodeIgniter はModel-View-Controller パターンに基づいています。MVC はプレ
ゼンテーションからアプリケーションロジックを分離するためのソフトウェア
アプローチです。実際には、PHPスクリプトからプレゼンテーションが分離される
ので、webページには、必要最小限のスクリプトだけを書くようにできます。

-  
   **モデル** はデータ構造をあらわします。モデルクラスは多くの場合、
   データベースから情報を取得・挿入・更新するメソッドを持ちます。
-  **ビュー** はユーザに表示される情報です。ビューは通常はwebページですが、
   CodeIgniterでは、ヘッダ・フッタなどのページの断片である場合もありま
   す。またRSSページや他のタイプのページである場合もあります。
-  **コントローラ** はモデルとビュー、それから、HTTPリクエストを処理し
   webページを生成するために必要とされるリソース、これらを仲介しながら
   動作します。

CodeIgniter has a fairly loose approach to MVC since Models are not
required. If you don't need the added separation, or find that
maintaining models requires more complexity than you want, you can
ignore them and build your application minimally using Controllers and
Views. CodeIgniter also enables you to incorporate your own existing
scripts, or even develop core libraries for the system, enabling you to
work in a way that makes the most sense to you.
