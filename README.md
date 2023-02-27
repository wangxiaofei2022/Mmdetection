# 一.Mmdetection常用命令语句
1.      统计当前目录下文件的个数（不包括目录）：ls -l | grep "^-" | wc -l
        统计当前目录下文件的个数（包括子目录）：ls -lR| grep "^-" | wc -l
        看某目录下文件夹(目录)的个数（包括子目录）：s -lR | grep "^d" | wc
2.python安装包下载的慢可以用镜像：
        pip install opencv-python -i https://mirrors.aliyun.com/pypi/simple
# 二.Mmdetection中的问题
##1.批量检测图片的过程 
（1） 将多张图片和对应的含所有标签信息的instances_val2017.json文件分别放到mmdetection1/data/coco/val2017/和mmdetection1/data/coco/annotations/
（2）
