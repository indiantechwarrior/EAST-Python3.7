# EAST: An Efficient and Accurate Scene Text Detector

### Introduction
This is a tensorflow re-implementation of [EAST: An Efficient and Accurate Scene Text Detector](https://arxiv.org/abs/1704.03155v2).
The features are summarized blow:
+ Only **RBOX** part is implemented.
+ A fast Locality-Aware NMS in C++ provided by the paper's author.
+ The pre-trained model provided achieves **80.83** F1-score on ICDAR 2015
	Incidental Scene Text Detection Challenge using only training images from ICDAR 2015 and 2013.
  see [here](http://rrc.cvc.uab.es/?ch=4&com=evaluation&view=method_samples&task=1&m=29855&gtv=1) for the detailed results.
+ Differences from original paper
	+ Use ResNet-50 rather than PVANET
	+ Use dice loss (optimize IoU of segmentation) rather than balanced cross entropy
	+ Use linear learning rate decay rather than staged learning rate decay
Thanks for the author's ([@zxytim](https://github.com/zxytim)) help!
Please cite his [paper](https://arxiv.org/abs/1704.03155v2) if you find this useful.

### Contents
1. [Installation](#installation)
2. [Download](#download)
2. [Demo](#demo)
3. [Test](#train)
4. [Train](#test)
5. [Examples](#examples)

### Installation
1. Any version of tensorflow version > 1.0 should be ok.
2. sudo apt-get install python3-tk

### Download
1. Models trained on ICDAR 2013 (training set) + ICDAR 2015 (training set): [BaiduYun link](http://pan.baidu.com/s/1jHWDrYQ) [GoogleDrive](https://drive.google.com/open?id=0B3APw5BZJ67ETHNPaU9xUkVoV0U)
2. Resnet V1 50 provided by tensorflow slim: [slim resnet v1 50](http://download.tensorflow.org/models/resnet_v1_50_2016_08_28.tar.gz)

### Train
If you want to train the model, you should provide the dataset path, in the dataset path, a separate gt text file should be provided for each image
and run

```
python multigpu_train.py --gpu_list=0 --input_size=512 --batch_size_per_gpu=8 --checkpoint_path=/content/EAST/tmp/east_icdar2015_resnet_v1_50_rbox/ --text_scale=512 --training_data_path=/content/EAST/data/sroie_train/ --geometry=RBOX --learning_rate=0.0001 --num_readers=4 --pretrained_model_path=/content/EAST/data/resnet_v1_50.ckpt
```


If you have more than one gpu, you can pass gpu ids to gpu_list(like --gpu_list=0,1,2,3)

**Note: you should change the names of text file in icdar2015's dataset to gt_img_\*.txt instead of img_\*.txt 
See the examples in training_samples/**



**This executable line is updated with reduced num_readers and batch_size_per_gpu, and code is running on top of pre-trained checkpoints. 
   Update current checkpoint file path in 'checkpoint' file in east_icdar2015_resnet_v1_50_rbox folder**
   
   

### Test
run
```
python eval.py --test_data_path=/content/EAST/tmp/images/ --gpu_list=0 --checkpoint_path=/content/EAST/east_icdar2015_resnet_v1_50_rbox/  --output_dir=/content/EAST/tmp/output/
```


a text file will be then written to the output path.

**Test Execution on Windows/CPU environment**

Open the x64 or x32 Visual Studio developer command prompt (or Native Tools Command Prompt) in Windows 10 and use the following command to generate the adaptor.pyd file:
first cd into lanms folder to execute below command in command prompt,

```
cl adaptor.cpp ./include/clipper/clipper.cpp /I ./include /I "C:\Python36\include" /LD /Fe:adaptor.pyd /link/LIBPATH:"C:\Python36\libs"
```
This will generate required files for Windows execution/testing



**Note** please make sure you comment some code in __init__.py in lanms folder
comment line 7 and 8
```
if subprocess.call(['make', '-C', BASE_DIR]) != 0:  # return value
    raise RuntimeError('Cannot compile lanms: {}'.format(BASE_DIR))
```




### Examples
Here are some test examples on icdar2015, enjoy the beautiful text boxes!
![image_1](demo_images/img_2.jpg)
![image_2](demo_images/img_10.jpg)
![image_3](demo_images/img_14.jpg)

### Troubleshooting
+ How to compile lanms on Windows ?
  + See https://github.com/argman/EAST/issues/120

Please let me know if you encounter any issues(my email boostczc@gmail dot com).
