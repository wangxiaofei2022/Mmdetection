# 一.Mmdetection常用命令语句

1.统计当前目录下文件的个数（不包括目录）：ls -l | grep "^-" | wc -l

统计当前目录下文件的个数（包括子目录）：ls -lR| grep "^-" | wc -l

看某目录下文件夹(目录)的个数（包括子目录）：s -lR | grep "^d" | wc

2.python安装包下载的慢可以用镜像：**pip install opencv-python -i https://mirrors.aliyun.com/pypi/simple**

# 二.Mmdetection应用
## 2.1 批量检测图片的过程 

1.将多张图片和对应的含所有标签信息的instances_val2017.json文件分别放到mmdetection1/data/coco/val2017/和mmdetection1/data/coco/annotations/

2.使用coco 评估标准计算所有类别 bbox_mAP50, 75, 95, bbox_mAP_s/m/l的值可以通过以下两种语句

（1）直接运行**CUDA_VISIBLE_DEVICES=7 python tools/test.py configs/faster_rcnn/faster_rcnn_r50_fpn_1x_coco.py work_dirs/faster-rcnn/epoch_12.pth  --eval bbox**即可得到相应指标结果

（2）**CUDA_VISIBLE_DEVICES=7 python tools/test.py configs/ssd/ssdlite_mobilenetv2_scratch_600e_coco.py work_dirs/ssd/epoch_120.pth --out results.pkl**

生成results.pkl文件，生成的results.pkl文件在/data/wangxiaofei/mmdetection1/下

使用上述得到的 results.pkl 文件在运行下列语句即可得到检测结果

**CUDA_VISIBLE_DEVICES=4 python tools/analysis_tools/eval_metric.py --config configs/ssd/ssdlite_mobilenetv2_scratch_600e_coco.py --pkl_results results.pkl --eval bbox**

![image](https://github.com/wangxiaofei2022/Mmdetection/blob/main/AP_AR值.png)

3.绘制每个类别bbox 的结果曲线图并保存

（1）先使用 test.py 生成输出 results.bbox.json 文件，生成的results.bbox.json在mmdetection1/目录下

**python tools/test.py  configs/faster_rcnn/faster_rcnn_r50_fpn_1x_coco.py work_dirs/faster-rcnn/epoch_12.pth --format-only --options "jsonfile_prefix=./results"**

（2）获得COCO bbox错误结果每个类别，保存分析结果图像到目录results/

**python tools/analysis_tools/coco_error_analysis.py results.bbox.json results --ann=/data/wangxiaofei/mmdetection1/data/coco/annotations/instances_val2017.json**

    results.bbox.json:上一步生成的文件
    results: 结果曲线图的生成目录, 此处将生成到results/ 目录下
    --ann=xxx.json: 指定数据集的标注文件, 需要修改成你自己的, 默认为 data/coco/annotations/instances_val2017.json, 用的是官方的

4.关于分析日志的工具（暂未测试）

python tools/analyze_logs.py plot_curve [--keys ${KEYS}] [--title ${TITLE}] [--legend ${LEGEND}] [--backend ${BACKEND}] [--style ${STYLE}] [--out ${OUT_FILE}]

    keys: 要展示的关键字
    title: 图的标题
    legend: 指定图例

（1）绘制一些运行的分类损失。

python tools/analysis_tools/analyze_logs.py plot_curve log.json --keys loss_cls --legend loss_cls

（2）绘制一些运行的分类和回归损失，并将该图保存为pdf。

python tools/analysis_tools/analyze_logs.py plot_curve log.json --keys loss_cls loss_reg --out losses.pdf

（3）比较同一图中两次运行的bbox mAP。

python tools/analysis_tools/analyze_logs.py plot_curve log1.json log2.json --keys bbox_mAP --legend run1 run2

（4）计算平均训练速度。

python tools/analyze_logs.py cal_train_time ${CONFIG_FILE} [--include-outliers]

预期输出将如下所示。

    -----Analyze train time of work_dirs/some_exp/20190611_192040.log.json-----
    slowest epoch 11, average time is 1.2024
    fastest epoch 1, average time is 1.1909
    time std over epochs is 0.0028
    average iter time: 1.1959 s/iter

# 三.运行过程中遇到的bug

1.运行CUDA_VISIBLE_DEVICES=7 python tools/test.py configs/swin/mask_rcnn_swin-t-p4-w7_fpn_1x_coco.py work_dirs/swin/epoch_24.pth  --eval bbox时遇到的问题：

![image](https://github.com/wangxiaofei2022/Mmdetection/blob/main/IndexError_list_index_out_of_range.png)

解决办法：

https://github.com/open-mmlab/mmdetection/issues/4243

将configs/_base_/datasets/目录下的**coco_detection.py**和**coco_instance.py**添加classes = ('car', 'truck')  classes=classes,

        ...
        dataset_type = 'CocoDataset'
        classes = ('car', 'truck')
        ...
        data = dict(
            samples_per_gpu=2,
            workers_per_gpu=2,
            train=dict(
                type=dataset_type,
                classes=classes,
                ann_file='path/to/your/train/data',
                ...),
            val=dict(
                type=dataset_type,
                classes=classes,
                ann_file='path/to/your/val/data',
                ...),
            test=dict(
                type=dataset_type,
                classes=classes,
                ann_file='path/to/your/test/data',
                ...))
        ...





























