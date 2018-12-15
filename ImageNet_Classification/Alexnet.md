# Image Net Classification with Deep Convolutional Deep Learning

[Image Net Classification with Deep Convolutional Deep Learning](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf)についてまとめる

## Abstract

1.2millionの高解像度画像を1000クラスに分類。`ImageNet LSVRC-2010コンテスト`で優勝。   
使用したニューラルネットワークは60millionのパラメータと650000のニューロンを持つ。5層のconv layerとmax pooling, 3層のfc layerを使用。dropoutを使用してoverfittingを防ぐ。

## Introduction

近年、[ImageNet](http://www.image-net.org/papers/imagenet_cvpr09.pdf)や[LabelMe](http://labelme.csail.mit.edu/Release3.0/)などfully-segmentedな画像が出現してきた。このような膨大なデータセットを学習するには大きなlearning capacityを持つモデルが必要である。CNNでは、このcapacityを層の厚さを変更することによりコントロールできる。彼らは規模の大きなCNN構造をもちいてImagenetを学習させたが、overfittingが問題であった(dropoutの提案)。5-6日を要して学習を行い、state of the art を獲得した。

## The Dataset

ImageNetは22000クラスに分類された15millionもの高解像度の画像データセットである。コンペ(ImageNet Large-Scale Visual Recognition Challenge)ではこれらのうち1.2millionのトレーニングデータ,150000のテストデータ, 50000の検証データを使用している。ImageNetではerror rateとしてtop-1とtop-5を用いる。top-5ではprobabilityがtop-5から外れているときのerror rateである。   
ImageNetでは様々な解像度の画像が存在する。従って彼らはダウンサンプルを行い、`256x256`に揃えた。長方形の画像においては、短辺を256にまず合わせ、その後、中心を切り取る手法を用いた。preprocessingは特別なことは行っておらず、meanを引いたのみである。ピクセル・チャンネルごとに平均を出し入力から差し引く。つまり、256×256×3個の平均値を出し、各入力画像から差し引く

## The Architecture

![](http://tecmemo.wpblog.jp/wp-content/uploads/2017/03/dl_alexnet-01.png)

８つの層で構成されており、5つの`conv層`と3つの`fully connected`層である。

### ReLU Nonlinearity

活性化関数*f*としてこれまで一般的に、<img src='https://latex.codecogs.com/gif.latex?f(x)=&space;tanh(x)'/>や<img src='https://latex.codecogs.com/gif.latex?f(x)=&space;(1&plus;e^{-x})'/>などのsaturationする関数を使用してきたが, gradient descentにおいてはtraining timeはこのようなsaturationのある関数よりもない関数(ReLU)の方が収束が数倍早い。実際、CIFAR-10でテストしたところ、training error 25%に達する時間は6倍速いことが分かった。

### Training on Multiple GPUs

memoryが3GBのGTX580を使用しているため、ネットワークのサイズに限りがある。また、1.2millionの画像はそのネットワークには大きすぎるデータセットであるため、2つのGPUをまたいで、ネットワークを構築している。現在のGPUはホストPCのメモリを通さずにr/wできるため、並列化しやすい。それぞれのGPUに半分ずつカーネルを敷き、カーネルへの入力は同じGPUにあるカーネルからのみ受け渡される(kernel3を除く)。

### Local Response Normalization

輝度の平均を0にしていないので輝度の正規化としてLRNを行う。「同一ピクセルにおいて複数の特徴マップ間で正規化する」ということをしている。[ここ](http://tatsudoya.blog.fc2.com/blog-entry-181.html)の説明が分かりやすい。特徴マップにおいて周りのn枚の層のあるピクセル位置の二乗の総和を取り、それで割ることにより、突出した値を抑制する。ここの論文ではLELUの後に入れている。Local Contrast normalizaionに似ているが、平均値を引いているわけではないのでその部分が違うらしい。error rateが1~2%ほど下がる。

### Overlapping Pooling

Max Poolingを取る時に各unitを今までは「重複しない」ようにしていたが、「少しかぶる」ようにすると過学習がおきにくくなり精度が向上した。これをoverlapping poolingと呼んでいる。3x3のpoolカーネルに対して、strideを2にしたことにより0.4%ほどerror rateが向上している。

### Overall Architecture

5層のconvと3層のfully-connected。1000クラス分類。
