# 分割数据集
```
python gen_ocr_train_val_test.py --trainValTestRatio 6:2:2 --datasetRootPath C:\Users\devxiny\Downloads\id_card_ybh
```
# 文本检测
## V3 CML
```
python tools/infer_det.py \
    -c configs/det/ch_PP-OCRv3/ch_PP-OCRv3_det_cml.yml \
    -o Global.checkpoints=./output/best_accuracy \
    Global.infer_img=./train_data/det/test 

python tools/infer_det.py \
    -c configs/det/ch_PP-OCRv3/ch_PP-OCRv3_det_cml.yml \
    -o Global.checkpoints=./ch_PP-OCRv3_det_distill_train/best_accuracy \
    Global.infer_img=./train_data/det/test 

python tools/train.py -c configs/det/ch_PP-OCRv3/ch_PP-OCRv3_det_cml.yml \
    -o Global.pretrained_model=./ch_PP-OCRv3_det_distill_train/best_accuracy \
       Global.save_model_dir=./output/

python tools/train.py -c configs/det/ch_PP-OCRv3/ch_PP-OCRv3_det_cml.yml \
    -o Global.checkpoints=./output/best_accuracy \
       Global.save_model_dir=./output/
```
## V4 CML
```
python tools/infer_det.py \
    -c configs/det/ch_PP-OCRv4/ch_PP-OCRv4_det_cml.yml \
    -o Global.infer_img=./train_data/IDCARD/test
```
## V3 DML
```
python3 tools/train.py \
    -c configs/det/ch_PP-OCRv3/ch_PP-OCRv3_det_dml.yml \
    -o Architecture.Models.Student.pretrained=./teacher \
    Architecture.Models.Student2.pretrained=./teacher \
    Global.save_model_dir=./output/

python tools/train.py -c configs/det/ch_PP-OCRv3/ch_PP-OCRv3_det_student.yml \
    -o Global.pretrained_model=./student \
    Global.save_model_dir=./output/

python tools/infer_det.py \
    -c configs/det/ch_PP-OCRv3/ch_PP-OCRv3_det_dml.yml \
    -o Global.checkpoints=./output/best_model_1730250591.6664228/model \
    Global.infer_img=./train_data/id_card/test 

python3 tools/infer/predict_det.py \
    -c configs/det/ch_PP-OCRv3/ch_PP-OCRv3_det_dml.yml \
    --det_model_dir=./output/inference/Student \
    --image_dir=./train_data/id_card/test  \
    --use_gpu=True
```
# 关键信息提取
```
// 训练
python tools/train.py -c configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml

// 预测，指定DET模型
python tools/infer_kie_token_ser.py \
    -c configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml \
    -o Architecture.Backbone.checkpoints=./output/ser_vi_layoutxlm_xfund_zh/best_accuracy \
    Global.infer_img=./train_data/id_card/test \
    Global.kie_det_model_dir=./output/inference/Student

// 预测，使用默认DET模型
python tools/infer_kie_token_ser.py \
    -c configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml \
    -o Architecture.Backbone.checkpoints=./output/ser_vi_layoutxlm_xfund_zh/best_accuracy \
    Global.infer_img=./train_data/IDCARD/test/CgEFiWazSUiADc8YAAL3ovjbKn844.jpeg
```
# 导出模型
```
// 导出DET模型
python3 tools/export_model.py -c configs/det/ch_PP-OCRv3/ch_PP-OCRv3_det_dml.yml -o Global.pretrained_model=./output/best_model_1730250269.39546/model

// 导出KIE模型
python3 tools/export_model.py -c configs/kie/vi_layoutxlm/ser_vi_layoutxlm_xfund_zh.yml -o Architecture.Backbone.checkpoints=./output/ser_vi_layoutxlm_xfund_zh/best_accuracy
```