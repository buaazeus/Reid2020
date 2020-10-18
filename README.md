# Reid2020
首先添加2019年行人重识别复赛数据，并将标记序号在2020标记基础上顺延。删除2019数据中图片名与2020冲突的图片，分别为  
00015970.png  00032710.png  00047507.png  00049382.png  00053208.png  00056739.png  00061620.png  00078937.png  00083951.png  
使用的基础代码为  
https://github.com/heshuting555/NAIC_Person_ReID_DMT  
以下所有代码均在NAIC_Person_ReID_DMT路径下运行  
每一轮训练开始时，先运行以下代码，将图片分为normal和green两类。  
python divided_dataset.py --data_dir_query ../data/test/query_a --data_dir_gallery ../data/test/gallery_a --save_dir ../data/test/  
训练阶段进行了2次训练  
第一次训练  
python train.py --config_file configs/a.yml  
模型1.在imagenet预训练模型基础上，在2020+2019数据上训练40个epoch。  
模型2.在imagenet预训练模型基础上，在2020+2019数据上训练50个epoch。  
模型3.在imagenet预训练模型基础上，先对2020训练集2万张无标记图片中随机选取了2792张作为query，剩余图片作为gallery，然后进行无监督训练。然后在2020+2019数据及无监督训练的结果一同进行训练50个epoch。  
测试时使用了Rerank
最终将3个模型对测试集的预测结果进行加权融合。  
