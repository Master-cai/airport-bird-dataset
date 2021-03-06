### 数据集
1. 做数据集这个事情该开展起来了
    * 工作量较大，团队合作
    * 这个事情要让多人参与进来
2. 一些顶层设计
    * 看看相关的数据集是怎么做的
    * 想想自己的特色是什么
    * 至少2万张标注图片，标注时和通用格式对接
    * 场景类别尽量丰富，需要背景图片吗
2. 先选取视频，再将视频过算法，保存视频文件，最后用VLC截取图像
    * 数据集应当是带时序信息的，图片排列按照时间先后
    * 保存视频文件时同时要保存位置信息，现在一个问题是检测框有点大，位置信息不太准，如何让检测框耦合得更好一些？
        - 保存的格式按照已知的来：coco、voc、YOLO
        - 要存归一化的位置信息，还是原始的位置信息，我觉得存原始的信息吧，从原始到归一化可以自己转换，这里还要存图片分辨率
    * 另外，检测框对不对应id信息，就是说哪只鸟对应哪个检测框，目前貌似做不到对应
3. 后续人工修正标注怎么做
    - 这是第二轮人工检查
    - 肯定要用工具来手动话，问题是怎么存标注的结果，且格式要和处理视频时保存的标注结果一致
        * 关键是labelme存的json，里面还有图片的二进制，怎么不要这个二进制，只存bbox信息
        * 查找到了其他软件，可以不保存图片信息，这还是蛮正常的
        * 关键要解决这个问题，解决之后就可以搞数据集了
    * 备用软件，我的想法是先找5个软件，分工给大家探讨
        - labelme
        - labelimg
        - vgg annotation，目前来看是一种走得通的方案，缺点是保存的数据格式不是coco/voc/yolo这些耳熟能详的
        - imglab
        - labelbox
4. 自己先摸索一条路子，剩下就是换标注软件，以及中间文件格式的修改
    * 这条路从哪开始，开始不需要收集大量数据，用少量数据跑起来即可
    * 在服务器上弄还是自己电脑，我想探索路子的时候用自己电脑，修正标注时要用图形界面，也用自己电脑
    * 目前初步定下labelImg这个工具，只保存标签；打开图片时，如果文件有标签，会显示对应矩形框
        - 缺点是不存图片分辨率，获取时候要读取图片
    * 现在该处理视频了，保存对应的标签
    * 每个视频命名的时候，是不是应该突出天气、环境、日期等，最终统计的时候才方便
    * 现在的问题是有好多空的txt文件，而且多数是空的，意味着视频里多数帧没鸟
        - 这问题该如何解决
1. 目前这只是一个飞鸟数据集，不是一个鸟击数据集
    - 从kaggle上看到一个鸟击分析 [博客](https://www.kaggle.com/mayshen/a-bird-eye-view-of-bird-strikes) ，分析的维度很多，缺点是只有文本信息，没有鸟的图片
    - 要了解相关的飞鸟数据集，资料该收集起来了，和其他方法在各个属性进行对比
    - 要把数据集制作过程说清楚，有哪些困难和挑战
    - 要把对比方法找好，分析各项指标，以及本文最关心的指标
    - 样例展示和分析，最开始要放一张全局牛逼的图
    - 相机的部署、和跑道的位置关系，相机的型号，图片分辨率这些都要
    - 标注该什么时候搞起来呢？
    - 统计图片数量，飞鸟框数量，天气环境类型等
2. 现在的图片帧率，每秒30张，全部保存密度太大了，想办法解决这个问题
    - 解决方法每10张图片选一张？看到TOD Challenge的方法是每50帧选一张图片，拍脑袋做决定不好吧
    - 这个问题到底怎么解决？数据已经划分开了，下一步就是标注了，这么盲目地做是不是浪费大家时间和精力
    - 即使密度大，没有想清楚前也不要删东西
    - 所以现在应该做什么？开始标注？
    1. 让大家装软件，告诉他们怎么使用：位置不准确的，修正位置；漏掉的飞鸟，增加标注
    2. 分一下图片，目前6个人，每人3000张，deadline是3个星期后，最好2个星期弄完
        * sljc: 1-2513(2513), 2514-4813(2294，有6个标签txt文件，删掉了)
        * IP_jkdhj: 1-3000, 3001-6000, 6001-9000, 9001-10264

3. 另外还有没有飞鸟的图片太多了，调研一下其他相关数据集需不需要这样的负例，如果不需要就删掉
    - 看了coco数据集，包含121,408张图，883,331个物体，没有说专门不带标注的图片作为负例
    - 下一步只需要把这些文件名记下来，存在另一个地方就好了；弄好了，40，000+ 图有一多半是空的
    - 现在这么做有点鲁莽了，上来就把他们删掉了，以后想找回也没有简便方法了，只能重新跑

4. 对比方法
    - TinyPerson dataset, ECCV TOD challenge
    - 检测
        - opencv动态物体检测
        - YOLOv3/4/5
        - EfficientDet
        - HRNet：增大分辨率是提升小目标检测效果的正常思路。**更多是一个backbone**，还得看用什么检测头，有对应的论文，要做实验啊
        - SNIPER
    - 跟踪：带有时间序列，与其看作检测问题，**还不如看作跟踪问题**？
        - opencv目标跟踪器
            * MEDIANFLOW: works well when the motion is predictable and there is no occlusion; fails when large motion
            * GOTURN: robust to viewpoint changes, lighting changes, deformations
            * KCF: Accuracy and speed are both better than MIL and it reports tracking failure better than BOOSTING and MIL. If you are using OpenCV 3.1 and above, I recommend using this for most applications; Accuracy and speed are both better than MIL and it reports tracking failure better than BOOSTING and MIL. If you are using OpenCV 3.1 and above, I recommend using this for most applications.
            * TLD: Works the best under occlusion over multiple frames. Also, tracks best over scale changes; Lots of false positives making it **almost unusable**
        - sort、deepsort
    - 各方法运行时间对比，不能是离线系统

5. 强调时间跨度，一年、白天、夜晚、春夏秋冬；相机布置，和跑道位置关系；天气条件，刮风下雨，风和日丽，雪花飘飘
    - 统计目标面积小于36像素的物体有多少，相对于其他small、medium、large的定义，到底有多小

### 问题
1. 保存图片的时候，每帧都保存，密度太大了，另外没有飞鸟的图片很多

2. 这个工作需要团队一起来做