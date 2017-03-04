# Caffe-model
Python script to generate prototxt on Caffe, specially the inception_v3\inception_v4\inception_resnet\fractalnet

# Models

The prototxts can be visualized by [ethereon](http://ethereon.github.io/netscope/quickstart.html).

Every model has a bn (batch normalization) version (maybe only bn version), the paper is [Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift](http://arxiv.org/pdf/1502.03167v3)




# Classificaiton (imagenet)


### Introduction
This folder contains the deploy files(include generator scripts) and pre-train models of resnet-v1, resnet-v2, inception-v3, inception-resnet-v2 and densenet(coming soon).

We didn't train any model from scratch, some of them are converted from other deep learning framworks (inception-v3 from [mxnet](https://github.com/dmlc/mxnet-model-gallery/blob/master/imagenet-1k-inception-v3.md), inception-resnet-v2 from [tensorflow](https://github.com/tensorflow/models/blob/master/slim/nets/inception_resnet_v2.py)), some of them are converted from other modified caffe ([resnet-v2](https://github.com/yjxiong/caffe/tree/mem)). But to achieve the original performance, finetuning is performed on imagenet for several epochs. 

The main contribution belongs to the authors and model trainers.

### Performance on imagenet
0. Top-1/5 accuracy of pre-train models in this repository.

 Network|224/299(single-crop)|224/299(12-crop)|320/395(single-crop)|320/395(12-crop)
 :---:|:---:|:---:|:---:|:---:
 resnet101-v2| 78.05/93.88 | 80.01/94.96 | 79.63/94.84 | 80.71/95.43
 resnet152-v2| 79.15/94.58 | 80.76/95.32 | 80.34/95.26 | 81.16/95.68 
 resnet269-v2| **80.29**/95.00 | **81.75**/95.80 | **81.3**0/95.67 | **82.13**/96.15 
 inception-v3| 78.33/94.25 | 80.40/95.27 | 79.90/95.18 | 80.75/95.76 
 inception-resnet-v2| 80.14/**95.17** | 81.54/**95.92** | 81.25/**95.98** | 81.85/**96.29**

 - All the pre-train models are tested on original [caffe](https://github.com/BVLC/caffe) by [evaluation_cls.py](https://github.com/soeaver/caffe-model/blob/master/cls/evaluation_cls.py).
 - 224x224(base_size=256) and 320x320(base_size=320) crop size for resnet-v2, 299x299(base_size=320) and 395x395(base_size=395) crop size for inception.

0. Top-1/5 accuracy with different crop sizes.
![teaser](https://github.com/soeaver/caffe-model/blob/master/cls/accuracy.png)
 - Figure: Accuracy curves of inception_v3(left) and resnet101_v2(right) with different crop sizes.

0. **Download url** and forward time cost for each model.

 Forward time cost is evaluated with one image/mini-batch using cuDNN 5.1 on a Pascal Titan X GPU.

 Network|224/299|320/395|Download(BaiDuYun)
 :---:|:---:|:---:|:---:
 resnet101-v2| 58.0ms | 69.1ms | [caffemodel (170.3MB)](https://pan.baidu.com/s/1kVQDHFx)
 resnet152-v2| 84.6ms | 100.8ms | [caffemodel (230.2MB)](https://pan.baidu.com/s/1dFIc4vB)
 resnet269-v2| 146.9ms | 173.2ms | [caffemodel (390.4MB)](https://pan.baidu.com/s/1qYbICs0)
 inception-v3| 58.3ms | 82.8ms | [caffemodel (91.1MB)](https://pan.baidu.com/s/1boC0HEf)
 inception-resnet-v2| 127.1ms | 172.4ms | [caffemodel (213.4MB)](https://pan.baidu.com/s/1jHPJCX4)

### Check the performance
0. Download the ILSVRC 2012 classification val set [6.3GB](http://www.image-net.org/challenges/LSVRC/2012/nnoupb/ILSVRC2012_img_val.tar), and put the extracted images into the directory:
    ```
    ~/Database/ILSVRC2012
    ```

0. Check the resnet-v2 (101, 152 and 269) performance, the settings of [evaluation_cls.py](https://github.com/soeaver/caffe-model/blob/master/cls/evaluation_cls.py):
   
    ```
    val_file = 'ILSVRC2012_val.txt' # download from this folder, label range 0~999
    ... ...
    model_weights = 'resnet-v2/resnet101_v2.caffemodel' # download as below
    model_deploy = 'resnet-v2/deploy_resnet101_v2.prototxt' # check the parameters of input_shape
    ... ...
    mean_value = np.array([102.9801, 115.9465, 122.7717])  # BGR
    std = np.array([1.0, 1.0, 1.0])  # BGR
    crop_num = 1    # perform center(single)-crop
    ```

    Check the inception-v3 performance, the settings of [evaluation_cls.py](https://github.com/soeaver/caffe-model/blob/master/cls/evaluation_cls.py):
   
    ```
    val_file = 'ILSVRC2015_val.txt' # download from this folder, label range 0~999
    ... ...
    model_weights = 'inception_v3/inception_v3.caffemodel' # download as below
    model_deploy = 'inception_v3/deploy_inception_v3.prototxt' # check the parameters of input_shape
    ... ...
    mean_value = np.array([128.0, 128.0, 128.0])  # BGR
    std = np.array([128.0, 128.0, 128.0])  # BGR
    crop_num = 1    # perform center(single)-crop
    ```
    
    Check the inception-resnet-v2 performance, the settings of [evaluation_cls.py](https://github.com/soeaver/caffe-model/blob/master/cls/evaluation_cls.py):
   
    ```
    val_file = 'ILSVRC2012_val.txt' # download from this folder, label range 0~999
    ... ...
    model_weights = 'inception_resnet_v2/inception_resnet_v2.caffemodel' # download as below
    model_deploy = 'inception_resnet_v2/deploy_inception_resnet_v2.prototxt' # check the parameters of input_shape
    ... ...
    mean_value = np.array([128.0, 128.0, 128.0])  # BGR
    std = np.array([128.0, 128.0, 128.0])  # BGR
    crop_num = 1    # perform center(single)-crop
    ```
    
0. then
    ```
    python evaluation_cls.py
    ```


# Acknowlegement

I greatly thank [Yangqing Jia](https://github.com/Yangqing) and [BVLC group](https://www.github.com/BVLC/caffe) for developing Caffe

And I would like to thank all the authors of every cnn model
