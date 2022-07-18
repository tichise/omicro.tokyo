---
layout: post
title: omicroとomicro Sandbox | マーカーレスARと仮想障害物を用いた自走式球体型ロボットの制御補助
---

## 1. はじめに
近年、複数のロボットを用いたパフォーマンスが子供向けの展示会で数多く見受けられる。本研究の著者も複数の球体型のロボットでそのようなパフォーマンスを行ってる。それらのロボットの操作を1人で継続して行うことは難しいため、自動制御システムを構築したいと考えている。
一方で、複数の球体型ロボットで制御補助を行う場合、球体が赤外線やToFに影響を与えるため、内部センサによる周辺の正確な位置の取得が難しい問題がある。
本研究では、スマホやAppleWatchから操作できるロボットボール omicroとマーカーレスARと仮想障害物を用いた自走式球体型ロボットの制御システム omicro Sandboxを提案する。

<img src="{{ site.baseurl }}public/image/omicro-sandbox.jpeg" />

本論文では、2章にて関連研究を紹介し、3章で提案システムの概要について述べる。4章で評価実験について説明し、5章にて本論文のまとめをおこなう。

## 2. 関連研究

## 3. 提案システム

#### 3.1 設計方針
提案システムの目的は、球体型ロボットの制御を補助し、展示会で子供向けのパフォーマンスを安定して行うことにある。そこでスマホやAppleWatchから操作できるロボットボール omicroの開発とマーカーレスARと仮想障害物を用いた自走式球体型ロボットの制御補助システム omicro Sandboxの開発を構築する。

#### 3.2 システム構成
システムの利用手順は以下の通りである。

1. 床に開発した球型ロボットを複数設置する。床や壁が反射しやすい場合はLEDの2値化に失敗するので、反射しにくい場所の上に置く。LEDと同色の光を放つプロダクトの近くも避ける。
2. 球型ロボットの電源をいれて、iPhoneアプリケーションとBLEで接続する。複数の球型ロボットと接続する場合は、iPhoneアプリを複数接続モードに切り替える。
3. 球型ロボットで電子コンパスのキャリブレーションを行う。キャリブレーションはiOSアプリから行う。
4. iPad Proを卓上三脚で机の上に固定する。球型ロボットはiPadのカメラの画角に入るように設置する。
5. iPad ProでUnityアプリケーションを起動する。メッシュモードをオンにして床やオブジェクトをメッシュ化する。必要に応じて移動可能エリアを指定する仮想障害物を空間に設置する。
6. UnityアプリケーションでOpenCVモードをオンにして、球体型ロボットの中心点を取得。動作環境が明るすぎたり暗すぎる場合は、必要に応じてOpenCVモードの2値化の色範囲を変更して、AR空間上に球型ロボットのデジタルツインを出現させる。
7. 球型ボールをiOSやwatchOSアプリケーションで動かす。球型ロボットのデジタルツインがAR空間上で衝突した場合は、ハードウェアの球型ロボットに状態をフィードバックする。

##### 3.2.1 球型ロボット
球体型ロボットの外側の球体はプラスチックBOX 球体 クリア 径170mmを利用している。材質はスチロール樹脂。パフォーマンスに使う球体型ロボットはマイコンはmbed LPC1768を利用している。Bletoothモジュールはkoshian、2つのマトリックスLEDはAdafruit ミニLEDマトリックス基板。モータードライバはSparkfun TB6612FNG。6軸センサはTPU6050。地磁気センサはGROVE 三軸デジタルコンパスモジュール v1、Ring LEDはNeoPixel Ring 16連フルカラーシリアルLED。
駆動部はタミヤ 楽しい工作シリーズ No.168 ダブルギヤボックス 左右独立4速タイプ、タイヤやスペーサー、固定具は3Dプリンタで自作の部品を用いてる。フレームはレーザーカットしたアクリルを使用。
電池はエネループを6本使用し、2本と４本を分割して配置している。ロボットの1番下にはパラストも配置している。これらは重心を整えるため、分散して設置している。
マイコンの制御はiOSアプリケーションからBLEで行っている。球体の中のロボットが球体の内側の壁を駆けあがろうとすると、重心が前方に偏り球体が前に進む。それを繰り返すことで前後左右に進む。方向転換は左右のモーターを同速で逆方向で回転させて超信地旋回で行っている。

<img src="{{ site.baseurl }}public/image/omicro-fusion360.jpeg" />

##### 3.2.2 球型ロボットを操作するためのiOSアプリケーション
アプリケーションはXcodeを用いて、Swiftで作成してる。アプリケーションは球型ロボットと各種アプリのハブとなる役割をになっており、複数の球型ロボットとの通信CoreBluetoothを用いてBLEで行っている。後述するwatchOSアプリとの接続もBLEで行ってる。
Unityアプリケーションとの接続にはFirebase Cloud Firestoreをもちいてる。

##### 3.3.3 球型ロボットを操作するためのwatchOSアプリケーション
watchOSアプリケーションの構成を図に示す。

このアプリケーションはiOSアプリケーションに付随するアプリケーションである。iOSアプリケーションと同様に球型ロボットを操作するのが主な機能だが、その操作の際は必ずiOSアプリケーションを経由して、球型ロボットに指示を送る。

iOSにないwatchOSアプリケーションならではの機能としては、ハンドモーション機能が挙げられる。これはパフォーマンスを行う球型ロボット操縦者の手の動きをAppleWatchのジャイロセンサと加速度センサで読み取って、それに応じて球型ロボットを操縦する機能である。
手を素早く下げた場合には機能がオフになり、素早く水平に上げた場合は機能がオンになる。手を水平状態から20°あげると、球体型ロボットが直進し、手を水平にあげたまま手のひらを左右に捻ると球体型ロボットが左右に回転する。
現在は誤作動が多く実用レベルではないが、watchOSのCoreMLを用いて手の動きを機械学習し、より複雑なハンドモーションで球型ロボットを操作する機能も実装されている。


##### 3.3.4 Unityアプリケーション
Unityアプリケーションの構成を図に示す。

アプリケーションはUnityで作成している。ARFoundationを用いてiOSのARKitの機能を呼び出している。PlaneDetection、LiDAR情報をもとにした空間のMesh化、HumanStencilを用いた人間の位置取得、仮想オブジェクトの設置、ShaderやVFXGraphを用いた視覚効果は全てARFoundationと組み合わせて行っている。
球体型ロボットの位置の取得はOpenCVを用いて行っている。アプリケーションで取得した画像を球体型ロボットのLEDの青色を基準にして2値化し、ボール型ロボットの中心座標を取得。その後で中心座標に対してARFoundationでRaycastを行い、ボールの奥行き取得を取得。AR空間上の仮想の座標に球型ロボットのデジタルツインを配置し、デジタルツインがAR空間内でARで出現させた別のGameObjectと衝突した場合は、ハードウェアの球型ロボットにもフィードバックを返すようにしている。
球型ロボットの向きの取得はこのアプリケーションでは行えないので、ハードウェアの方でデジタルコンパスを用いて方位を計算し、それをUnityアプリの方に返す形をとっている。

<iframe width="560" height="315" src="https://www.youtube.com/embed/Q72rKRp1n6g" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/WezOrvHdP-4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZCqCP0iw_rY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

##### 3.3.5 Firebase Cloud Firestore
Firebase Cloud Firestoreは2つの用途で使ってる。1つは球型ボールから受け取った角度情報をUnityアプリケーションに送信するため。もう1つはUnityアプリケーションから受け取った衝突判定のフラグを球型ロボットに送信するためである。

## 4 評価実験[WIP]
### 4.1 実験環境[WIP]
システムを利用してパフォーマンスを行い、システムの操作性や安定性について調査した。

<iframe width="560" height="315" src="https://www.youtube.com/embed/WWQ7j0a7_0U" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/Lots1cQWn_I" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### 4.2 評価と考察[WIP]
### 4.3 実演での評価[WIP]
以上より、展示会の実際の環境において十分に実用的なシステムであることを証明できた。

## 5. まとめ[WIP]
本研究では、スマホやAppleWatchから操作できるロボットボール omicroとマーカーレスARと仮想障害物を用いた自走式球体型ロボットの制御補助システム omicro Sandboxを構築した。システムの有効性を示すため、展示会で制御の実演を行った。また〜も確認した。
今後は、システムを利用して様々な展示を行い、ARを用いた球体型ロボットの制御について他のパターンも模索する。