# YOLOとは
[https://docs.ultralytics.com/ja](https://docs.ultralytics.com/ja)
- 「You Only Look Once」という英文の頭文字を取って作られ、「一度見るだけで良い」という意味で、人間のように一目見ただけで物体検出ができることを指しています
  - YOLOv8では物体検出の他に、セグメンテーション、イメージ分類、姿勢測定などを実行できます
    - 詳細は [こちら](https://docs.ultralytics.com/tasks/)
- 自動運転、セキュリティ、医療等さまざまな分野で使用されています

### インストール
- インストール
```sh
$ pip install ultralytics
```

- YOLOの学習済みモデルをダウンロード
    - 以下のファイル `yolo_sample.py` を作成して実行すると、`yolov8n.pt` というモデルがPython実行フォルダにダウンロードされます。



```python
from ultralytics import YOLO

model = YOLO('yolov8n.pt')
print(model.names)
```

:::{card} topic
YOLOの学習済みモデルは複数あり、以下右にいくほどサイズは大きくなります。

- yolov8n.pt < yolov8s.pt < yolov8m.pt < yolov8l.pt < yolov8x.pt

今回はサイズの小さい  yolov8n.ptを使用します。
モデルのサイズの小さいほど計算速度は早いですが、精度は低くなります。

参考：[パフォーマンス指標](https://docs.ultralytics.com/ja/models/yolov8/#performance-metrics)
:::


### YOLOを使って画像ファイルの物体検出をしよう

- 物体を検出して表示する
```python
# yolo_sample.py

from ultralytics import YOLO
import cv2


model = YOLO('yolov8n.pt')
print(model.names)  # YOLOが検出できるclass names のリスト

img = cv2.imread('image/apple.jpg')
results = model(img)  # class 'ultralytics.engine.results.Results のリストが返される

# https://docs.ultralytics.com/reference/engine/results/#ultralytics.engine.results.Results
annotated_image = results[0].plot()

cv2.imshow('annotated image', annotated_image)
cv2.waitKey(0)
cv2.destroyAllWindows()  # qで終了
```

- クラスを指定して物体を検出する

```python
model = YOLO('yolov8n.pt')
img = cv2.imread('image/apple.jpg')
results = model(img, classes=47)  #  47: 'apple' 「print(model.names)で出力した値を参照」

class_list = results[0].boxes.cls.tolist()  # [47.0, 47.0]
count = len(class_list)

print(f'appleの数: {count}')
```




