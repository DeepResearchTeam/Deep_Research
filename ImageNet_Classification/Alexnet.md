# Image Net Classification with Deep Convolutional Deep Learning

[Image Net Classification with Deep Convolutional Deep Learning](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf)についてまとめる

## Abstract

1.2millionの高解像度画像を1000クラスに分類。`ImageNet LSVRC-2010コンテスト`で優勝。   
使用したニューラルネットワークは60millionのパラメータと650000のニューロンを持つ。5層のconv layerとmax pooling, 3層のfc layerを使用。dropoutを使用してoverfittingを防ぐ。

## Introduction

近年、[ImageNet](http://www.image-net.org/papers/imagenet_cvpr09.pdf)や[LabelMe](http://labelme.csail.mit.edu/Release3.0/)などfully-segmentedな画像が出現してきた。このような膨大なデータセットを学習するには大きなlearning capacityを持つモデルが必要である。CNNでは、このcapacityを層の厚さを変更することによりコントロールできる。彼らは規模の大きなCNN構造をもちいてImagenetを学習させたが、overfittingが問題であった(dropoutの提案)。5-6日を要して学習を行い、state of the art を獲得した。

## The Dataset



