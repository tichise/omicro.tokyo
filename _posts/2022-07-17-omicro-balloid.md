---
layout: post
title: omicro balloid：複合現実と自律型AIを用いたマルチエージェントシステム
image: public/image/omicro-sandbox.jpeg
---

## 1. はじめに
ロボットを用いたデモンストレーションは展示会で数多く見受けられる。本作品の作者もSTEAM教育用の球体型のロボット omicroを自作し、それを用いたデモンストレーションを行ってる。ロボットの操作をマニュアルで長時間行うことは難しいため、AIをもちいて群制御で行えるようにしたいと考えている。<br />
一方で、球体型ロボットの群制御をハードウェア側で行う場合、プラスチック球体がセンシングデバイスに影響を与えるため、内部センサによる周辺の正確な位置の取得が難しい問題がある。また球体が透明なため機械学習による物体検出も精度が出づらいという問題もある。<br />

本作品では、[球体型ロボットomicro](https://omicro.tokyo/2022/07/18/omicro/)と複合現実と自律型AIを用いたマルチエージェントシステム omicro balloidを提案する。

<img src="{{ site.url }}/public/image/omicro-sandbox.jpeg" />

<div class="frame-wrapper__video">
<iframe src="https://www.youtube.com/embed/OW55twYWHYI?si=52gw0198qPPuJHVf" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>

<div class="frame-wrapper__video">
<iframe src="https://www.youtube.com/embed/RzXbb--4pYU?si=CZkfbftlSrA576Lc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>


## 2. 関連作品

## 3. 提案システム

#### 3.1 設計方針
提案システムの目的は、球体型ロボットの制御を補助し、展示会でデモンストレーションを安定して行うことにある。そこでスマホやAppleWatchから操作できる球体型ロボットボール omicroと複合現実と自律型AIを用いた球体型ロボットの群制御補助システムを構築する。

#### 3.2 システム構成
システムの利用手順は以下の通りである。

1. 開発した球型ロボットをその上に配置する。床や壁が反射しやすい場合はLEDの2値化に失敗するので、反射しにくい黒のシートを床の上に置く。LEDと同色の光を放つプロダクトの近くも避ける。
2. 球型ロボットの電源をいれて、iPhoneアプリケーションとBLEで接続する。複数の球型ロボットと接続する場合は、iPhoneアプリを複数接続モードに切り替える。
3. 球型ロボットで電子コンパスのキャリブレーションを行う。キャリブレーションはiOSアプリから行う。
4. iPad Proを三脚で固定する。球型ロボットはiPadのカメラの画角に入るように設置する。iPad Proの高さは地上から130cm前後に設定する。
5. iPad ProでUnityアプリケーションを起動する。メッシュモードをオンにして床やオブジェクトをメッシュ化する。必要に応じて移動可能エリアを指定する仮想障害物を空間に設置する。
6. iPad Pro上で動かすUnityアプリと球体型ロボットの操作で使ってるiOSアプリをBluetoothで接続して、iOSアプリを経由して球体型ロボットの電子コンパスから取得した方位情報をUnityアプリに渡す。
7. UnityアプリケーションでOpenCVモードをオンにして、球体型ロボットの中心点を取得。動作環境が明るすぎたり暗すぎる場合は、必要に応じてOpenCVモードの2値化の色範囲を変更して、AR空間上に球型ロボットのデジタルツインを出現させる。環境光の影響で検出位置がずれるので、OpenCV検出位置の補正機能を用いて、奥行きや縦横の位置を手動調整する。
8. 球型ロボットをiOSやwatchOSアプリケーションで動かす。球型ロボットのデジタルツインがAR空間上で衝突した場合は、ハードウェアの球型ロボットに状態をフィードバックする。
9. AR空間上で球体型ロボットを追尾するゲームAIを走らせる。ゲームAIが現実空間に存在する球体型ロボットと接触すると、現実空間に存在する球体型ロボットが回避行動を起こす。追尾と回避を繰り返して、結果として球体型ロボットが自動で動き続ける。

##### 3.2.1 球型ロボット
球体型ロボット omicroは球体と球体の中で動くロボットで構成される。
外側の球体はプラスチックBOX 球体 クリア 径170mmを利用している。材質はスチロール樹脂。表面には硬化ガラスコーティング剤を塗装している。
球体の中を走行するロボットはmbedマイコン LPC1768、koshian、2つのマトリックスLED、モータードライバ Sparkfun TB6612FNG、6軸センサ TPU6050、地磁気センサ GROVE 三軸デジタルコンパスモジュール v1、NeoPixel Ring 16連フルカラーシリアルLEDで構成されている。タイヤやスペーサー、固定具は3Dプリンタ ORIGINAL PRUSA MINI+でプリントした自作の部品を用いてる。フレームはレーザーカットしたアクリルを使用。電池はエネループを6本使用し、2本と４本を分割して配置している。ロボットの1番下にはパラストも配置している。これらは重心を整えるため、分散して設置している。

車輪が全部で10個ついているが、動力に繋がってるのは下部の大きな2つ車輪のみである。タイヤを直接球体と接触させるとグリップが効きすぎるので医療用途で使われるサージカルテープを巻いて摩擦を減らしている。他の8個の車輪は静止時は球体の内壁に接触していない。走行時のみ内壁と接触する。補助輪には「ボールの直進安定性向上」「衝撃吸収」「回転時の軸を保つ」役割がある。
車輪が回転して内側のロボットが球体の内側の壁を駆け上がろうとすると、前が重くなり、ボールが転がる。それを繰り返すことでボールが前に進む。ボールの向きの変更は超信地旋回で行っている。車輪が逆方向に等速で回転すると、ボールはその場で回転を始める。

<img src="{{ site.url }}/public/image/omicro-fusion360.jpeg" />

##### 3.2.2 球型ロボットを操作するためのiOSアプリケーション
このiOSアプリケーションはこのシステムのハブの役割を担っており、複数の球型ロボット omicroとwatchOSアプリ、Unityアプリの間の通信は全てこのアプリを介して行われている。複数の球型ロボットとwatchOSアプリとの通信はCoreBluetoothを用いてBLEで行っている。
Unityアプリケーションとの接続にはFirebase Cloud Firestoreをも利用している。Firebase Cloud Firestoreは2つの用途で使っていて、1つは球型ロボットから受け取った角度情報をUnityアプリケーションに送信するため。もう1つはUnityアプリケーションから受け取った衝突判定のフラグを球型ロボットに送信するためである。

##### 3.3.3 球型ロボットを操作するためのwatchOSアプリケーション
このアプリはiOSアプリに付随するものである。iOSアプリと同様に球型ロボットを操作するのが主な機能だが、その操作の際は必ずiOSアプリケーションを経由して、球型ロボットに指示を送る。
iOSにないwatchOSアプリケーションならではの機能としては、ハンドモーション機能が挙げられる。これはパフォーマンスを行う球型ロボット操縦者の手の動きをAppleWatchのジャイロセンサと加速度センサで読み取って、それに応じて球型ロボットを操縦する機能である。 手を素早く下げた場合には機能がオフになり、素早く水平に上げた場合は機能がオンになる。手を水平状態から20°あげると、球体型ロボットが直進し、手を水平にあげたまま手のひらを左右に捻ると球体型ロボットが左右に回転する。 
現在は検出精度が低く実用レベルではないが、watchOS上のCoreMLを用いて手の動きを機械学習し、複雑なハンドモーションで操作する機能も実装されている。手の動きのログをCoreMLのActivity Classificationでトレーニングさせて、センシングだけより複雑な手の動きのパターンを読み取れるようにしている。

<div class="frame-wrapper__video">
<iframe src="https://www.youtube.com/embed/jYlJKQ_Lz2w?loop=1&playlist=jYlJKQ_Lz2w" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>


##### 3.3.4 Unityアプリケーション
本システムの中核であるomicro balloidと名付けられた複合現実アプリはUnityで作成している。ARFoundationを用いてARKitの機能を呼び出している。PlaneDetection、LiDAR情報をもとにした空間のMesh化、HumanStencilを用いた人間の位置取得、仮想オブジェクトの設置、ShaderやVFXGraphを用いた視覚効果は全てARFoundationと組み合わせて行っている。
球体型ロボットの位置の取得はOpenCVを用いて行っている。アプリケーションで取得した画像を球体型ロボットのLEDの青色を基準にして2値化し、ボール型ロボットの中心座標を取得。その後で中心座標に対してARFoundationでRaycastを行い、ボールの奥行き取得を取得。AR空間上の仮想の座標に球型ロボットのデジタルツインを配置し、デジタルツインがAR空間内でARで出現させた別のGameObjectと衝突した場合は、ハードウェアの球型ロボットにもフィードバックを返すようにしている。 
球型ロボットの向きの取得はこのアプリケーションでは行えないので、ハードウェアの方でデジタルコンパスを用いて方位を計算し、それをUnityアプリの方に返す形をとっている。

omicro balloidはiPad ProのLiDARやコンピュータビジョンライブラリを用いて現実空間の上にデジタルツインをリアルタイム作成し、その上で動くゲームAIが起こした事象が現実空間に存在する球体型ロボットに反映される。また現実に存在する球体型ロボットに仮想空間上での質量を与えてるので、現実のロボットが動くと仮想空間上のゲーム AIやゲームオブジェクトにも影響を与える。

<div class="frame-wrapper__video">
<iframe  src="https://www.youtube.com/embed/1QpAbbGFEUQ?si=lWYz64B2NPQe1yKP" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>

<div class="frame-wrapper__video">
<iframe src="https://www.youtube.com/embed/xmWBEk5yjAA?si=pk4ZRXjhm0Ok0q7u" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>


## 4 評価実験
### 4.1 実験環境
システムを利用してパフォーマンスを行い、システムの操作性や安定性について調査した。

<div class="frame-wrapper__video">
<iframe  src="https://www.youtube.com/embed/WWQ7j0a7_0U?loop=1&playlist=WWQ7j0a7_0U" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

<div class="frame-wrapper__video">
<iframe src="https://www.youtube.com/embed/Lots1cQWn_I?loop=1&playlist=Lots1cQWn_I" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>