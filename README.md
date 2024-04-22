# Potholes_Detection
![](https://github.com/PiroJOJO/Potholes_Detection/blob/main/images/cat.png)

---

<img src = "https://img.shields.io/badge/Python 3.9-006C6B?style=for-the-badge&color=FABB22&labelColor=%96CEB4&logo=python&logoColor=96CEB4">  <img src ='https://img.shields.io/github/repo-size/PiroJOJO/Potholes_Detection?style=for-the-badge&color=FABB22&labelColor=%96CEB4&logo=weightsandbiases&logoColor=96CEB4'>


## Задача

---

Детекция повреждения дорожного покрытия с камеры дрона.  
В качестве решения использовалась модель YoloV8m, которая обучалась на предварительно обработанных 27 000 изображениях.

## Управление проектом

---

Рекомендованная версия Python 3.9.0
+ Клонируйте репозиторий нижеприведенной командой:
```
git clone https://github.com/PiroJOJO/Potholes_Detection
```
+ Откройте папку и установите необходимые библиотеки командой ниже:
```
pip install -r requirements.txt
```

## Датасеты

---

Ниже приведены ссылки для скачивания датасета, который использовался для обучения и тестирования данной модели

+ Полный датасет (собранный из разных источников) : [data](https://drive.google.com/file/d/1l3PUKvHmJQxTsKZiMGdZcjbRBJ568W5J/view?usp=sharing)
+ Ручной датасет, собранный и размеченный(CVAT) для тестирования модели : [data](https://drive.google.com/file/d/1ScHGTILAy5qDzHz9A6-iQJxBpGHUTWRi/view?usp=sharing)

## Вспомогательные скрипты 
1. Для подготовки датасета
 - [xml_to_yolo](https://github.com/PiroJOJO/Potholes_Detection/blob/main/scripts/xml_to_yolo.ipynb) - переводит label из формата xml в yolo формат
 - [duplicates](https://github.com/PiroJOJO/Potholes_Detection/blob/main/scripts/duplicates.ipynb) - находит/удаляет дубликаты в датасете, основываясь на пороговом значении
 - [delete_labels](https://github.com/PiroJOJO/Potholes_Detection/blob/main/scripts/delete_labels.ipynb) - удаляет label при отсутствии его изображения
 - [cutting_of_videos](https://github.com/PiroJOJO/Potholes_Detection/blob/main/scripts/cutting_of_videos.ipynb) - получение определенного количества кадров с видео
2. Для работы с моделью
 - [test](https://github.com/PiroJOJO/Potholes_Detection/blob/main/scripts/test.ipynb) - получение метрик с тестовых изображений
 - [inference](https://github.com/PiroJOJO/Potholes_Detection/blob/main/scripts/inference.ipynb) - получение результата работы модели на тестовых изображениях
 - [video_inference](https://github.com/PiroJOJO/Potholes_Detection/blob/main/scripts/video_inference.ipynb) - получение результата работы модели на видео

## Обучение модели 

---

 + CLI
```
yolo detect train data=path_to_dataset/data.yaml model=yolov8m.yaml pretrained=yolov8m.pt epochs=200 imgsz=640 batch=64 patience=50 project=path_to_results
```
+ Python
```Python
from ultralytics import YOLO

model = YOLO('yolov8m.pt') 
results = model.train(data='path_to_dataset/data.yaml', epochs=200, imgsz=640, batch=64, patience=50, project=path_to_results)
```

## Валидация модели
+ CLI
```
yolo detect val model=path_to_weights/best.pt imgsz=640 batch=16 conf=0.4 iou=0.3 device='0'
```
+ Python
```Python
from ultralytics import YOLO

model = YOLO('path_to_weights/best.pt')
validation_results = model.val(data='path_to_test/data.yaml',
                               imgsz=640,
                               batch=16,
                               conf=0.4,
                               iou=0.3,
                               device='0')
```
## Тестирование модели
+ CLI
```
yolo detect predict model=path_to_weights/best.pt source='path_to_test/images'imgsz=640 conf=0.4 iou=0.3
```
+ Python
```Python
from ultralytics import YOLO

model = YOLO('path_to_weights/best.pt')
model.predict('path_to_test/images', 
              save=True, 
              imgsz=640, 
              conf=0.4,
              iou=0.3,
              visualize=True)
```

## Результат обучения модели 

---

<img src="https://github.com/PiroJOJO/Potholes_Detection/blob/main/images/results.png"  alt="1" width = 700px height = 360px > 


## Результат работы модели (на тестовых данных)

---

| Model        | Resolution | Images | Instances | Box(P) | R      | mAP50 | mAP50-95 |
|--------------|------------|--------|-----------|--------|--------|-------|----------|
| YoloV8-m     | 640px      | 742    | 1167      | 0.711  | 0.326  | 0.489 | 0.308    |


## Визуализация работы модели

---

<img src="https://github.com/PiroJOJO/Potholes_Detection/blob/main/images/car.gif"  alt="1" width = 700px height = 360px > 

## Оценка работы модели 

---

<img src="https://github.com/PiroJOJO/Potholes_Detection/blob/main/images/labels.jpg"  alt="1" width = 700px height = 560px > 

По результатам обучения и метрикам, полученных с помощью тестовых изображений, можно сделать вывод о полученном датасете.
+ В тренировочном датасете очень много именно маленьких боксов, поэтому модель плохо реагирует на большие объекты.
+ Также заметно меньшее количество "лежащих" боксов в целом.  

Следоавтельно, для дальнейшей доработки проекта необходимо найти больше данных именно с ямами, а не трещинами (чтобы уравновесить с числом вытянутых боксов) + донобрать при необходимости изображений (если ТЗ допускает возможность доваольно близкую съемку), где требуемые объекты имеют большие боксы .
Поиграться с агментациями (ресайзить, кропать), чтобы донобрать интересующих кадров.
