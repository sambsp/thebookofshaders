# ジェネラティブデザイン

繰り返しと秩序を十分に堪能したので、今度は多少の混沌を持ち込んでみましょう。

## ランダム

[![Ryoji Ikeda - test pattern (2008) ](ryoji-ikeda.jpg) ](http://www.ryojiikeda.com/project/testpattern/#testpattern_live_set)

ランダムはエントロピーが最大になった状態です。一見厳格で規則正しいコードの世界で、どのようにしてランダムな要素を生成することができるのでしょうか。

下記の関数を検討することから始めましょう。

<div class="simpleFunction" data="y = fract(sin(x)*1.0);"></div>

ここではサイン波から小数点部分を取り出しています。```-1.0``` から ```1.0``` の間を往復する [```sin()```](../glossary/?search=sin) の値から、小数点の後ろだけを切り取ると ```0.0``` から ```1.0``` の間の正の値だけが残ります。これを利用し、さらにサイン波を細かな部分に分割することで擬似的にランダムな値を得ることができます。どういうことのでしょう。[```sin(x)```](../glossary/?search=sin) の結果の値に大きな数を掛けてみます。上の関数をクリックして 0 を幾つか書き加えてみましょう。

```100000.0```　に至る頃には（式は ```y = fract(sin(x)*100000.0)``` のようになります）もうサインカーブには見えなくなっているでしょう。小数点部分のサイクルは非常に短くなりサイン波の流れるような曲線は潰されて、ランダムに見えるカオス状態を作り出しています。

## カオスを制御する

乱数を使いこなすのは難しいこともあります。無秩序すぎたり、十分にランダムでないこともあります。下記のグラフを見てください。このグラフは、上で述べた通りの方法で実装した ```rand()``` 関数を使って作られています。

よく見ると ```-1.5707``` と ```1.5707``` のあたりに小さな裂け目のようなものがあるのが分かるでしょう。これは [```sin()```](../glossary/?search=sin) の描く波が最大と最小になる場所です。

乱数の分布に注目すると、端にくらべて中央に値が集中しているのが分かるでしょう。


<div class="simpleFunction" data="y = rand(x);
//y = rand(x)*rand(x);
//y = sqrt(rand(x));
//y = pow(rand(x),5.);"></div>

以前に[Pixelero](https://pixelero.wordpress.com)は[ランダムな値の分布についての興味深い記事](https://pixelero.wordpress.com/2008/04/24/various-functions-and-various-distributions-with-mathrandom/)を公開しました。この記事から、上記のグラフに幾つかの関数を加えておきました。どのように値の分布が変化するか試してみてください。関数のコメントを外して何が起こるか見てみましょう。

[Pixeleroの記事](https://pixelero.wordpress.com/2008/04/24/various-functions-and-various-distributions-with-mathrandom/)を読むときには、ここで私たちが作った ```rand()``` 関数は擬似ランダムとも呼ばれる決定的な（結果の値が一意に定まる）乱数だということを頭に置いておいてください。例えば ```rand(1.)``` は常に同じ値を返します。[Pixelero](https://pixelero.wordpress.com/2008/04/24/various-functions-and-various-distributions-with-mathrandom/)が引き合いにしているのはActionScriptの ```Math.random()``` で、これは非決定的な、つまり毎回異なる値を返す関数です。

## 2Dランダム

ランダムの性質についての理解が深まったところで、次に二次元、つまり ```x``` 軸と ```y``` 軸の両方に適用してみましょう。そのためには、二次元ベクトルを一次元の浮動小数点の値に変換することが必要です。いろいろなやり方がありますが、[```dot()```](../glossary/?search=dot) 関数は特に便利です。 [```dot()```](../glossary/?search=dot) 関数は2つのベクトルの組に対して、```0.0``` から ```1.0``` の間の浮動小数点の値を返してくれます。

（訳注：値が ```0.0``` から ```1.0``` の間に収まるには2つのベクトルが正規化されている必要があります。下記のサンプルではベクトルは正規化されていないので ```dot``` の戻り値は ```1.0``` を大きく超えます。）

<div class="codeAndCanvas" data="2d-random.frag"></div>

13行目から15行目を見てみましょう。```vec2 st``` ともう1つの二次元ベクトルである ( ```vec2(12.9898,78.233)```) の使い方に注目してください。

* 14行目と15行目の値を変えてみましょう。ランダムなパターンがどのように変化したか観察し、そこから何が学べるか考えてみましょう。

* このランダム関数の仕組みをより理解するために、マウスのインタラクション (```u_mouse```) と時間 (```u_time```) に反応させてみましょう。

## カオスを使いこなす

二次元のランダムはまるでテレビのノイズのように見えますね。この未加工な素材からイメージを作り出すのは簡単なことではありません。ここでは、素材の料理方法を学んでいきましょう。

まずは最初のステップとしてグリッドを適用してみましょう。 [```floor()```](../glossary/?search=floor) 関数を使って 整数の表を作り出します。下記のコードの、特に22行目と23行目を見てみてください。

<div class="codeAndCanvas" data="2d-random-mosaic.frag"></div>

空間座標を10倍に拡大した後に（21行目）、座標の整数部分を小数点部分から切り離します。続く23行目の処理は、前章で空間を ```0.0``` から ```1.0``` の座標値を持つ小さな部分に分けるときに使ったお馴染みの方法です。
座標から整数を取り出すことによって、それぞれのセル（描画領域を 10×10 に分割したマス目）に含まれるピクセルに共通の値を取り出します。
そしてこの整数をそのセルについてランダムな値を得るために使います。ここで使っているランダム関数は決定性のものなので、戻り値はそのセルの全てのピクセルに対し同じものになります。

座標の小数点部分の値も保持されていることを、29行目のコメントを外して確認しましょう。こうして、それぞれのセルの内部でもさらに座標系を用いて描画することができます。

これら2つの値、つまり座標の整数部分と端数部分を組み合わせることで、変化と秩序を混ぜ合わせることができます。

有名な [```10 PRINT CHR$(205.5+RND(1)); : GOTO 10```](https://www.google.com/search?q=10+PRINT+CHR%24%28205.5%2BRND%281%29%29%3B+%3A+GOTO+10) 迷路ジェネレーターのGLSL版を見てみましょう。

<div class="codeAndCanvas" data="2d-random-truchet.frag"></div>

ここでは、前章の ```truchetPattern()``` 関数と、セルのランダム関数を合わせて用い、あっちに行ったりこっちに行ったりする線を描いています（41行目から47行目）。

50行目から53行目までのコメントを外すと、もう1つの興味深いパターンを見ることができます。また、35行目と36行目のコメントを外すと、パターンに動きを与えることができます。

## ランダムを極める

日本の電子音楽家でありビジュアルアーティストでもある[池田亮司](http://www.ryojiikeda.com/)は、ランダムの扱い方に熟達しています。彼の作品は感動的で魅力的なものです。彼は音とビジュアルの領域でランダムな要素を、いらだたしい無秩序を生み出すのではなく、現代テクノロジー文化の複雑さを鏡映しにするかのように用います。

<iframe src="https://player.vimeo.com/video/76813693?title=0&byline=0&portrait=0" width="800" height="450" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

[池田亮司](http://www.ryojiikeda.com/)の作品をよく観察しながら、下記の課題に挑戦しましょう。

* ランダムな値を用いて（反対の方向に）動くセルの列をつくってみましょう。より明るい値のセルのみを表示させてみましょう。列の速さを時間とともに変化させてみましょう。

<a href="../edit.html#10/ikeda-00.frag"><canvas id="custom" class="canvas" data-fragment-url="ikeda-00.frag"  width="520px" height="200px"></canvas></a>

* 同様に、違う方向に違うスピードで動くいくつかの列をつくってみましょう。どのセルを表示させるかの閾値として、マウスの位置情報を適用してみましょう。

<a href="../edit.html#10/ikeda-03.frag"><canvas id="custom" class="canvas" data-fragment-url="ikeda-03.frag"  width="520px" height="200px"></canvas></a>

* 他にも面白いエフェクトを作ってみましょう。

<a href="../edit.html#10/ikeda-04.frag"><canvas id="custom" class="canvas" data-fragment-url="ikeda-04.frag"  width="520px" height="200px"></canvas></a>

美しい表現のためにランダムを用いても上手くいかないことがあります。自然に見えるシミュレーションをつくりたいと思っているときは特にそうです。ランダムは単純に無秩序すぎて、現実世界には ```random()``` に見えるものはほんの少ししかありません。雨の様子や株価のチャートはどちらもとても不規則なものですが、私たちがこの章の始めに生成したランダムとは似ても似つかないものです。なぜでしょう。ランダムの値はそれぞれの値の間にまったく相関性をもっていないのに対し、多くの自然界のパターンには過去の状態の記憶が含まれているからです。

次の章ではノイズ、つまりスムーズで自然に見えるカオスを計算によって生み出す方法について学びます。