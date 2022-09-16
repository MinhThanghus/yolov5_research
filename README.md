
<div align='left'>
  <img src=https://img.shields.io/github/stars/positive666/yolov5_research.svg?style=social >
  <img src=https://img.shields.io/github/forks/positive666/yolov5_research.svg?style=social >
  <img src=https://img.shields.io/github/watchers/positive666/yolov5_research.svg?style=social>
</div> 


<div align="center">
<p>
   <a align="left" href="https://ultralytics.com/yolov5" target="_blank">
   <img src="./data/images/yolov.jpg" width="87%"></a>
</p>
</div>



##  <div align="left">🚀 yolov5_reserach  High-level项目(近期回项目重整优化！！！waiting)</div>


更新升级中.....

### <div align="left">⭐新闻板块【实时更新/计划】</div>


	- 分类、检测、分割、关键点检测基本整合完毕，工程结构精简化中，关键点检测训练正常已经验证，分割待调试，火速迭代中
	- 分割代码结合V5和V7的代码进行了合并DEBUG调试，训练部分待验证，另外注意力层训练过程中，没法收敛或者NAN的情况，排除代码问题，需要在超参数YAML里，先对学习衰减率从改为0.2 ，比如GAM的注意力头部问题，训练周期加到400epoch+ 就可以。
	- 去年的decoupled结构虽然能提点，不过FLOPS增加的太多，目前用V5作者分支的解耦头替换，效果待验证。
	- 融合了代码做了部分的优化，这里看了下V7的代码优化较差，后续会集成精简版本的分类、分割、检测、POSE检测的结构，目前已经完成了一部分工作，更新频繁有问题欢迎反馈和提供实验结果。
<p>

# Feature 🚀 

     - 基于torch以最新的yoloV5工程风格精简融合High-level任务：完成先进的检测器、分类器、分割、关键点检测功能集成，删除额外的三方库依赖，快速迭代
     
     - 实时的v5代码更新改动&&v7的适配
     
     - 早期集成的attention、self-attention维护和调试
     
     - 额外的网络结构和Tricks补充

[CSDN同步更新](https://blog.csdn.net/weixin_44119362/article/details/125665404)
(保证每周同步更新一次维护，不定期更新算法代码和引进结果实验！关于消融实验大多来自朋友的热心反馈，探究范式CNN和transformer，如何根据经验设计网络结构、LOSS改进、辅助训练分支、样本匹配....  欢迎提供实验数据和結果~)

对于自注意力机制的使用：很多人与CNN相结合使用得到精度提升，个人理解：原因不仅仅是长距离的依赖，早期我们使用固定权重的滤波器提取边缘再到CNN，CNN也许是对应着高通滤波，而self-attention对应于低通滤波，那么相当于对featuremap进行了一次平滑，这样从某种程度上可以解释互补之后的提升；而且transfromer是很难发生过拟合或者说不存在，动态特性确实更具泛化性，常规情况中优先考虑你训练数据集的拟合够不够好，你的模型是否能反映出数据之间的特征特异性，其次扩充构建相应的辅助分支加入特征属性描述。
声明：这一年里有很多朋友用修改的idea方式以及注意力和设计结构去做Paper，不用咨询我意见：一切free！有成效自己论文开源随便，与我无关，只是希望各位能把实验数据反馈给我，因为平时工作业务比较忙，实验的时间不够。也是借此目的来和大家一起学习交流。

<details open>
<summary>Install</summary>
Clone repo and install [requirements.txt](https://github.com/positive666/yolov5_research/requirements.txt) 

```bash
git clone https://github.com/positive666/yolov5_research  # clone
cd yolov5
pip install -r requirements.txt  # install
```
</details>

## <div align="center">目标检测篇</div>

<details>
<summary>Inference with detect.py</summary>


```bash
python detect.py --source 0  # webcam     --weights <your model weight>
                          img.jpg  # image
                          vid.mp4  # video
                          path/  # directory
                          'path/*.jpg'  # glob
                          'https://youtu.be/Zgi9g1ksQHc'  # YouTube
                          'rtsp://example.com/media.mp4'  # RTSP, RTMP, HTTP stream
```
</details>  

yolov7 官方的訓練权重打包链接：https://pan.baidu.com/s/1UIYzEZqTPMUpWWBBczOcgA?pwd=v7v7(由于我删除了P6模型里的Reorg操作和Focus没本质区别，所以删掉需要重新训练，如果你想使用V7原始权重，你只需要在YAML里改回去，还有一种方式是遍历Reorg的权重把它替换掉) 提取码：v7v7

###  Train

see train.py args ,command as :

```bash
python train.py --data <your data yaml>  --cfg  <your model yaml> --weights <weights path>  --batch-size 128    --hyp   <hyps yaml>  --batch-size <numbers>  
```
<details open>
<summary>Notes</summary>

- "--swin_float"  for "SwinTransformer_Layer" layers,because of " @" not support  fp16,so you can use offical yolov7 “ Swinv2Block”
- "--aux_ota_loss" for aux- head only . Such "models/v7_cfg/training/yolov7x6x.yaml, (P6-model) ,you can create aux -head models.		
- "ota_loss"  in hyps filse ,ota_loss default=0 
</details>   

<details>
<summary>Training commnd example </summary>

-  run yolov7-P5 model train and yolov5 seriese models ,scratch or fine ,your need a weights 

```bash 
python train.py  --data data/coco128.yaml  --cfg models/yolov5s_decoupled.yaml   
```
```bash 
python train.py  --cfg  models/v7_cfg/training/yolov7.yaml  --weights yolov7.pt  --data (custom datasets) --hyp data/hyps/hyp.scratch-v7.custom.yaml	
```
-  run yolov7-aux model train ,your model must P6-model !
```bash 
python train.py  --cfg  models/v7_cfg/training/yolov7w6.yaml --imgsz 1280  --weights 'yolov7-w6_training.pt'  --data (custom datasets)  --aux_ota_loss  --hyp data/hyps/hyp.scratch-v7.custom.yaml
```
- After training/under yaml structure, your initial weight xxx. PT will become a trained yolov7xxx.pt , with specific references to reparameterized scripts. 
Then use the deploy model to load the weights of your training, change the index and structure to re-parameterize the model.

</details>

<details open>
<summary>re-parameterizetrained yolov7 model  </summary>

```bash 
   python reparameterization.py  --weights <yolov7.pt,yolov7e6e.pt.....>  --name <model name > --save_file   models/v7_cfg/deploy  --cfg <model.yaml>
```
</details>


##  <div align="center">关键点检测篇</div>

<details>
<summary>数据集构建</summary>

```
yolov5_research
│   pose  
│   └─────(key point detect code )
│   ...   
│
coco_kpts(your data yaml path name )
│   images
│   annotations/**.json
|   labels
│   └─────train2017
│       │       └───
|       |       └───
|       |       '
|       |       .
│       └─val2017
|               └───
|               └───
|               .
|               .
|    train2017.txt
|    val2017.txt

```
</details>

###  Inference
refernce v7 weights.
[yolov7-w6-pose.pt](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-w6-pose.pt)

``` bash 
python pose/detect.py --weights pose/pose_weights/yolov7-w6-pose.pt  --source  data/images/bus.jpg   --kpt-label 
```
###  Train

[yolov7-w6-person.pt](https://github.com/WongKinYiu/yolov7/releases/download/v0.1/yolov7-w6-person.pt)

``` shell
    python pose/train.py --data  data/coco_kpts.yaml  --cfg  pose/cfg/yolov7-w6-pose.yaml weights yolov7-w6-person.pt --img 960  --kpt-label --hyp data/hyps/hyp.pose.yaml

```

## <div align="left">历史更新</div>

<details>
<summary>更新记录</summary>
- 2020/9/15  High-level 集成待验证，目前姿态训练和检测已经调试完成。
- 2022/7/21  除关键点部分的V7代码以及V5代码风格优化合并更新，改善了重参数脚本的功能，详情看	reparameterization.py

- 2022/7/13  同步更新加入了yolov7的P6模型訓練部分代碼，p6是需要aux的所以需要添加Loss部分計算，代碼和CSDN持續更新中,由于我删除了P6模型里的Reorg操作其实就说FOcus，所以需要重新训练，如果你想使用V7原始权重，你只需要在YAML里改回去

- 2022/7/7   依旧全网首发 ：以目前仓库的魔改版V5为基准同步了YOLOV7的核心改动，代码风格是最新的，后续会持续完善优化，完美融合V7，后续博客争取更新第一时间！

- 2022/5/23  合并更新了YOLOV5仓库的最新版本，作者代码有点小问题就是数据集会重复下载，这部分我没就没合并他的更新，引入了新的算子，看来他也在探索实验

- 2022/3/26  测试下解耦训练结果/更新GAM注意力层代码：按照论文示意在大模型中使用分组卷积降低FLOPs,同步简单实验下，关于实验在闲暇之余都会慢慢完善的。
    以small模型，在Visdrone数据下的简单验证：
	|   Model     		 |   mAP@.5  | mAP@.5:95 | Parameters(M) | GFLOPs |
	| --------    		 |   ------  |  ------   | ------------- | ------ |
	| YOLOv5s     		 |   0.351    |  0.194   |     7.2       | 16.5   |
	| YOLOv5s+GAM 		 |   0.35    |  0.194    |     22.2      | 36.9   |
	| YOLOv5s_decoup     |   0.367   |  0.203    |     7.1       | 17.2   |
    | YOLOv5s_GAM_group   |  0.353  	|  0.192 	 |     11       | 21.4   |  （待进一步更新）


- 2022/3/26  1.修复了一些常规的问题BUG并合并了V5作者的最新代码更新，大概包含之前缺少了一些可学习参数和代码优化,如添加了swintransformerV2.0的相对位置编码加入优化器等。 2.目前看来GAM换用组卷积效果有待商榷，后续进一步整理消融实验总结。
- 2022/3/16  对上传的GAM注意力层进行了简单的实验，yolov5s+GAM在Visdrone数据集上的结果举例参考，后续的话其实难点在于轻量化，探究大模型的骨干估计只有大厂研究资源能有成本去做。
- 2022/3/5   近期会整理一些去年的实验数据/、使用swin2的骨干，超参数需要调试一下，首先要稍微减低学习率，（实测SGD）；也可以把SWIN层作为注意力插件训练，这个和以往的操作类似，不再赘述了 需要开启--swin_float   命令参数，因为点积不被cuda的half支持，而优化器的问题，那么问题基本就是较多的swin block 堆积导致的增量更新。同时伴随着GPU的开销。 
- 2022/3.1   （不完整更新,供参考，怕忙断更，所以先放出部分修改，目前还在动态调试中）按照SWintransformerV2.0 的改进点：修改了NORM层的位置/attention将dot换成scaled cosine self-attention，待更新的优化部分：1.序列窗口注意力计算，降低显存开销 2、训练优化器
- 2022/2.28  添加了一个Swintransformer的Backbone和yaml示意结构，很多人把SWIN还像之前做成注意力层，但是其实swin设计是为了摒弃CNN去和NLP一统，而且精髓在于控制计算复杂度，其实backbone的全替换也许更值得尝试 ，内存开销和结构设计待优化
- 2022/2.22  忙里抽闲：更新了今天的yolov5的工程修复，修改了解耦头的代码风格，直接yaml选择参考使用，服务器回滚了代码。SWIN算子在，YAML文件丢失了，找时间从新写一个再上传，太忙看更新可能优先GIT，等有空博客细致归纳下
- 2022/2.6   ASFF使用的BUG已经修复;近期更新Swintransformer代码，简单说明下程序上其实是两种改法：1.类似算子层的修改，这个比较简单 2、全部替换成Swintransformer，这个对于整个程序存在一定的代码修改地方，稍微复杂点。
- 2022/1.9   补充一些注意力算子GAM，原理后续CSDN说明，修复BUG
- 2021/11.3  合并最新的YOLOV5的改动， 替换了CSPBOTTLENNECK的LeakRELUw为SLIU，其余全是代码和工程规范修改
- 2021.10.25 修复BUG，恢复EIOU
- 2021.10.13 更新合并YOLOV5v6.0版本，改进点：第一时间的更新解析可参考[CSND博客](https://blog.csdn.net/weixin_44119362/article/details/120748319?spm=1001.2014.3001.5501)
- 2021.9.25  将自注意力位置编码设置成可选项，默认取消，CBAM不收敛——将激活函数改回Sigmoid
- 2021.6.25  添加BIFPN结构包含P5/P6层，增大开销但是对于一些任务是能够提点的
- 2021.6     Botnet transformer 算子块引入于Backbone底层
- 2021.2.10  全网首发的YOLOV5魔改，ASFF检测头封装加入、注意力机制CBAM、CooRD、等注意力算子引入，并介绍了通用修改方式
***

</details>

## <div align="center">高性能视频推理部署</div>
<details>
<summary>工程部署 Why Deepstream?</summary>

工程部署：该仓库只属于研究探索，但是工程部署讲究简单高效、故可以参考我的Deepstream SDK改的项目，集合了通用检测、人脸识别、OCR三个项目，高性能的部署AI框架开发逻辑，这个项目是我2021年整理并开源的，代码还未规范，但程序是没问题的。
 DS_5.1&&Tensorrt7+ ：https://github.com/positive666/Deepstream_Project

     1.英伟达提供的Deepstream &&Tensorrt，应用于流媒体处理，因为做过业务的都知道，推理性能不等于程序运行性能，核心除了模型的本身剪枝量化之外，涉及到了对数据输入的处理，这里的核心问题是如何提高GPU的利用率，那么最直接的就是GPU编解码.
     2.目前嵌入式部署可能大多采用剪枝通道压缩模型的流程，在结合一些框架去进行引擎推理，推荐Yolov5nano或者nanodetplus,(工程上主流是通道裁剪，替换如C3的BOLOCK，你可以在仔细比对YOLOV5的迭代。还有就是如何使用SGD炼丹的经验了)
     还有就是deepstream的普及，网上很多剪枝版本我也看了值得学习，但是工程不只在于学习，而在于成本和结果。
     3.x86和Jeston都可以部署，既有一站式解决方案，我觉得工程和研究应用是完全不同的操作思路，精简高效达到目的.deepstream全做了并完成降维打击 ，当然也需要一定的综合开发能力。

</details>

## C++ sdk的完整Deepstream5.1部署（内置C++嵌入的Kafka服务） 
  目前是5.1版本，近期更新6.0(主要区别在于Tensorrt7和Tensorrt8的源码区别导致的，部分6.0 SDK有变动)
  [Deepsteam YOLOV5 V5.0]https://github.com/positive666/Deepstream_Project/tree/main/Deepstream_Yolo 



## Acknowledgements

<details><summary> <b>Expand</b> </summary>

* [https://github.com/WongKinYiu/yolov7](https://github.com/WongKinYiu/yolov7)
* [https://github.com/ultralytics/yolov5](https://github.com/ultralytics/yolov5)
* [https://github.com/TexasInstruments/edgeai-yolov5/tree/yolo-pose](https://github.com/TexasInstruments/edgeai-yolov5/tree/yolo-pose)
* [https://github.com/ChristophReich1996/Swin-Transformer-V2](https://github.com/ChristophReich1996/Swin-Transformer-V2)
* https://github.com/positive666/Deepstream_Project

</details>