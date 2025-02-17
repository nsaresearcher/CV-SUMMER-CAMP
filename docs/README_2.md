﻿# Практика 2. Классификация изображений при помощи нейронных сетей


## Цели

__Цель данной работы__ - решить задачу классификации объектов, используя средства модуля [Dnn][opencv_dnn]библиотеки OpenCV.

## Задачи
  
__Основные задачи:__

  1. Скачать обученную сеть `squeezenet1.1`.
  2. Разработать приложение для классификации изображений. 
    
__Дополнительные задачи:__

  1. Добавить возможность классификации по части изображения, заданной пользователем.
  1. Реализовать вывод названия класса (список имен класов содержится в файле `*.labels`), к которому относится данное изображение.
  1. Реализовать вывод  top-3 (top-5, top-10) классов, предложенных классификатором.

## Структура программы

В папке `src` расположен файл `classificator.h`, который содержит объявление абстрактного класса `Classificator`.

Класс имеет следующие чисто виртуальные методы:
  1. `Classify` - метод для классификации изображений при помощи нейронной сети.
  
## Общая последовательность действий
  1. Скачать обученную сеть `squeezenet1.1`.
  1. Разработать класс `DnnClassificator` (наследника класса `Classificator`) для решения задачи классифкации объектов.
  1. Реальзовать загрузку модели нейронной сети `squeezenet1.1` в конструкторе `DnnClassificator`.
  1. Реализовать метод `Classify` класса `DnnClassificator`.
  1. Реализовать чтение изображения (см. [Практика 1][practice1]). 
  1. Выполнить классификацию полученного изображения.
  1. Прочитать список имен клаассов, определить название класса, котоый предсказала нейросеть.
  1. Выполнить [дополнительные задачи][addtasks].
  
## Детальная инструкция по выполнению работы

  1. Клонировать origin-репозиторий к себе на локальную машину (см. [Практика 1][practice1]).		
  1. Собрать проект и проверить его работоспособность, запустив проект `practice2`.
     
            - Воспользоваться инструкцией по сборке и запуску из Практики 1.
            
            - Запустить проект practice2 и удостовериться, что он успешно отработал.

            ```bash
            $ cd CV-SUMMER-CAMP-build/bin
            $ practice2.exe
            ```
  1. Создать рабочую ветку `practice-2`([см. Практика 1][practice1]).
  1. В папке `<openvino>/deployment_tools/tools/model_downloader/`(IntelSWTools) запустить скрипт `downloader.py` с параметрами `--name squeezenet1.1 --output <destination_folder>` 
        ```bash
        $ cd C:\Intel\computer_vision_sdk\deployment_tools\model_downloader
        $ python downloader.py --name squeezenet1.1 --output <destination_folder>
        ```
        Если запустить данный скрипт без параметров, то можно увидеть названия всем моделей, доступных для загрузки через данный скрипт.
  
  1. Объявить класс `DnnClassificator`, наследника абстрактного класса `Classificator`, в файле `сlassification.h`.
  1. В файле `сlassification.сpp` реализовать методы класса `DnnClassificator`.
  1. На вход конструктор класса `DnnClassificator` принимает следующие параметры:
        - путь `<path-to-model>`до файла `*.caffemodel`, содержащий веса модели;
		- путь `<path-to-config>` до файла `*.prototxt` с конфигурацией модели;
		- путь `<path-to-labels>`до файла `*.labels` с именами классов;
		- ширина `inputWidth` входного изображения;
		- высота `inputHeight` входного изображения;
		- вектор `mean` средних значений, которые были использованы при тренировке сети (по умолчанию `Scalar(0,0,0,0)`);
        - признак `swapRB`неоходимости обмена местами `R` и `В` каналов изображения.
	
  1. Конструктор класса `DnnClassificator` реализует следующий функционал:
  		- инициирует поля класса (см. принимаемые параметры).
		- реализует загрузку модели. 
		  - Для работы с моделью используется класс `Net` модуля [dnn][opencv_dnn_net] OpenCV;
		  - Для загрузки модели используется метод `readNet(<path-to-model>, <path-to-config>, string)`;
		  - Для настройки дополнительных параметров модели используются методы `setPreferableBackend(0)` и `setPreferableTarget(0)` класса `Net`.
		  
  1. Реализовать метод `DnnClassificator::Classify`, выполняющий следующую последовательность действий:
        - выполнить предобработку.
	  - cоздать объект `inputTensor` класса `Mat`;
	  - вызвать функцию `blobFromImage(image, inputTensor, scale, Size(inputWidth, inputHeight), mean, swapRB, false)`, конвертирующую изображение в тензор.
        - вызвать метод `setInput(inputTensor)` класса `Net` для установки объекта классификации.
        - запустить вычисления нейронной сети, используя метод `forward()` класса `Net`. Результат работы записать в `Mat`.
        - результаты работы нейронной сети конвертировать в вектор вероятностей принадлежности к классам, используя метод `reshape(1,1)` класса `Mat`; 
        - вернуть вектор вероятностей.
	 
  1. Создать копию файла `<project_source>/samples/practice2.cpp` и назвать ее `<project_source>/samples/practice2_YOUR_NAME.cpp`. Далее работать с файлом `<project_source>/samples/practice2_YOUR_NAME.cpp`. 
  1. В файле `<project_source>/samples/practice2_YOUR_NAME.cpp` реализовать чтение реализовать чтение файла изображения при помощи имени файла, полученного из аргументов командной строки `--i`. 
  1. В файле `<project_source>/samples/practice2_YOUR_NAME.cpp`создать объект класса `DnnClassificator`. Применить метод `Classify` к изображению:
      - путь к весам модели задается через командную строку посредством ключа `--model_path`;
      - путь к конфигурации модели задается через командную строку посредством ключа `--config_path`;
      - путь к меткам задается через командную строку посредством ключа `--label_path`;
      - путь к меткам задается через командную строку посредством ключа `--mean`.
  1. Определить класс с наибольшей вероятностью принадлежности исходного изображения к нему. Вывести номер класса на экран.
  1. Убедиться, что проект успешно собирается и создается новый исполняемый файл `<project_build>/bin/practice2_YOUR_NAME.exe`.
  1. Прислать Pull Request с внесенными изменениями. Пометить в конце названия `(NOT READY)`. По мере готовности решений основных задач Pull Request можно будет переименовать. Полный список принимаемых параметров можно посмотреть запустив исполняемый файл с ключом `--help`. См. документацию к классу [cv::dnn::Net][opencv_dnn_net] (методы readNet`, `setInput` и `forward` ) и пространству имен [cv:dnn][opencv_dnn] (метод blobFromImage). Также полезным будет посмотреть примеры работы с dnn из [официальных семплов OpenCV][opencv_examples]
 
  1. Решить задачи из списка [Дополнительные задачи][addtasks].
 
  
<!-- LINKS -->
  
[practice1]: https://github.com/itlab-vision/CV-SUMMER-CAMP/blob/master/docs/README_1.md
[git-intro]: https://github.com/itlab-vision/CV-SUMMER-CAMP/blob/master/docs/README_1.md#Общие-инструкции-по-работе-с-git
[cmake-msvs]: https://github.com/itlab-vision/CV-SUMMER-CAMP/blob/master/docs/README_1.md#Сборка-проекта-с-помощью-cmake-и-microsoft-visual-studio 
[opencv_dnn]: https://docs.opencv.org/4.1.0/df/d57/namespacecv_1_1dnn.html
[opencv_dnn_net]: https://docs.opencv.org/4.1.0/db/d30/classcv_1_1dnn_1_1Net.html#details
[opencv_examples]: https://docs.opencv.org/4.1.0/examples.html
[opencv_dnn_classification_sample]: https://docs.opencv.org/4.1.0/d9/d8d/samples_2dnn_2classification_8cpp-example.html
[addtasks]: https://github.com/itlab-vision/CV-SUMMER-CAMP/blob/master/docs/README_2.md#Задачи
