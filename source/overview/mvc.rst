#####################
Model-View-Controller
#####################

CodeIgniter はModel-View-Controller パターンに基づいています。MVC はプレ
ゼンテーションからアプリケーションロジックを分離するためのソフトウェア
アプローチです。実際には、PHPスクリプトからプレゼンテーションが分離される
ので、webページには、必要最小限のスクリプトだけを書くようにできます。

-  The **Model** represents your data structures. Typically your model
   classes will contain functions that help you retrieve, insert, and
   update information in your database.
-  The **View** is the information that is being presented to a user. A
   View will normally be a web page, but in CodeIgniter, a view can also
   be a page fragment like a header or footer. It can also be an RSS
   page, or any other type of "page".
-  The **Controller** serves as an *intermediary* between the Model, the
   View, and any other resources needed to process the HTTP request and
   generate a web page.

CodeIgniter has a fairly loose approach to MVC since Models are not
required. If you don't need the added separation, or find that
maintaining models requires more complexity than you want, you can
ignore them and build your application minimally using Controllers and
Views. CodeIgniter also enables you to incorporate your own existing
scripts, or even develop core libraries for the system, enabling you to
work in a way that makes the most sense to you.
