# Reid2020
首先添加2019年行人重识别复赛数据，并将标记序号在2020标记基础上顺延。删除2019数据中图片名与2020冲突的图片，分别为  
00015970.png  00032710.png  00047507.png  00049382.png  00053208.png  00056739.png  00061620.png  00078937.png  00083951.png  
使用的基础代码为  
https://github.com/heshuting555/NAIC_Person_ReID_DMT  
安装环境需求  
pip install opencv-python  
pip install yacs  
模型文件  
链接: https://pan.baidu.com/s/1Kvrmrgsmm-dET-S3zbQrXA  
提取码: dw6p  

以下所有代码均在NAIC路径下运行  

训练阶段进行了2次训练，全部是在在imagenet预训练模型基础上进行的，如果仅需要检查模型预测结果，可略过训练阶段，直接进行运行预测阶段代码。  
第一次训练  
data/test下存放2020测试集数据  
运行以下代码，将图片分为normal和green两类。  
python divided_dataset.py --data_dir_query ../data/test/query --data_dir_gallery ../data/test/gallery --save_dir ../data/test/  
开始训练
python train.py --config_file configs/a.yml  
每10个epoch保存一次模型，训练50个epoch结束。  
  
第二次训练，需在第一次训练完成后进行，需要用到第一次训练的模型结果  
data/test下存放2020训练集集2万无标记数据,随机选取了2792张作为query，剩余图片作为gallery，路径分别为data/test/query和data/test/gallery，用于无监督训练，具体图片见unlabel/query.txt和unlabel/gallery.txt  
运行以下代码，将图片分为normal和green两类。  
python divided_dataset.py --data_dir_query ../data/test/query --data_dir_gallery ../data/test/gallery --save_dir ../data/test/  
开始训练
python train_UDA.py --config_file configs/c.yml --config_file_test configs/a.yml --data_dir_query ../data/test/query --data_dir_gallery ../data/test/gallery
  
预测阶段进行了3次预测，最后加权融合，预测时使用了Rerank。  
data/test下存放2020测试集数据  
运行以下代码，将图片分为normal和green两类。  
python divided_dataset.py --data_dir_query ../data/test/query --data_dir_gallery ../data/test/gallery --save_dir ../data/test/  

模型1.第一次训练40个epoch的模型。  
开始预测  
python test.py --config_file configs/a.yml  
  
模型2.第一次训练50个epoch的模型。  
开始预测  
python test.py --config_file configs/b.yml  
  
模型3.第二次加入无监督数据训练50个epoch的模型。 
开始预测  
python test.py --config_file configs/c.yml  

最终将3个模型对测试集的预测结果进行加权融合。  
python ensemble_dist.py  
会在NAIC路径下生成submit_final.json结果文件。



