# 1.Mmdetection常用命令语句
（1）统计当前目录下文件的个数（不包括目录）ls -l | grep "^-" | wc -l   <br>
>>统计当前目录下文件的个数（包括子目录）ls -lR| grep "^-" | wc -l    <br>
>>看某目录下文件夹(目录)的个数（包括子目录）s -lR | grep "^d" | wc
（2）2022.4.25）mmdetection-master从3080上移到2080时，运行test.py报错cuda out of memory，没有具体显示需要多少显存，剩余多少显存
