### 数据集
1. 做数据集这个事情该开展起来了
    * 这个事情要让多人参与进来
    * 哪些人可以参与进来：王硕、黄哲、（吴登智、章瑜），三到四个人就够了
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

### 缺点
1. 保存图片的时候，每帧都保存，密度太大了，另外没有飞鸟的图片很多