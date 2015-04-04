##############################
設計とアーキテクチャのゴール
##############################

CodeIgniterのゴールは 最小かつ可能な限り軽量なパッケージで、
パフォーマンス・許容性・柔軟性を最大化することです。

このゴールを達成するため、私たちはベンチマークを行い、
リファクタリングし、開発プロセスを単純化し、この目標に
そぐわないあらゆるものを捨て去りました。

技術的・設計的視点に立つと、CodeIgniter は次の目標にもとづいて
つくられているといえます:

-  **動的なインスタンス化。** CodeIgniterでは、グローバルに読み
   込んだり実行したりせず、必要とされたときだけ、コンポーネントを
   ロードし、処理を実行します。システムが動作するのには、最低限の
   コア以上のものを前提としないので、システムはデフォルトでは大変
   軽量です。HTTPリクエストなどをトリガーとして起こるイベントで、
   そして、プログラマが設計したコントローラやビューで、何を呼び出
   すかを定義します。
-  **Loose Coupling.** Coupling is the degree to which components of a
   system rely on each other. The less components depend on each other
   the more reusable and flexible the system becomes. Our goal was a
   very loosely coupled system.
-  **Component Singularity.** Singularity is the degree to which
   components have a narrowly focused purpose. In CodeIgniter, each
   class and its functions are highly autonomous in order to allow
   maximum usefulness.

CodeIgniter is a dynamically instantiated, loosely coupled system with
high component singularity. It strives for simplicity, flexibility, and
high performance in a small footprint package.
