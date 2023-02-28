# 一.Mmdetection常用命令语句
1.统计当前目录下文件的个数（不包括目录）：ls -l | grep "^-" | wc -l

统计当前目录下文件的个数（包括子目录）：ls -lR| grep "^-" | wc -l

看某目录下文件夹(目录)的个数（包括子目录）：s -lR | grep "^d" | wc

2.python安装包下载的慢可以用镜像：**pip install opencv-python -i https://mirrors.aliyun.com/pypi/simple**

# 二.Mmdetection中的问题
## 2.1.批量检测图片的过程 

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

*results.bbox.json:上一步生成的文件
- results: 结果曲线图的生成目录, 此处将生成到results/ 目录下
- --ann=xxx.json: 指定数据集的标注文件, 需要修改成你自己的, 默认为 data/coco/annotations/instances_val2017.json, 用的是官方的


