# Сайт московской мастерской по изготовлению гуслей Psaltery (psaltery.ru)


## Ссылка на архив
**Ссылка**: [Перейти](https://drive.google.com/drive/folders/1ulDnVM0xy4LrfsDD24jOu8Vo58us4y3o?hl=ru)

**Состав**: 
- файл cdx (234 кб)
- файл с логами (2.5 мб)
- файл warc (520.6 мб)
- файл .db (416 кб)
- 
## 1. Анализ: Archive Ready

<img src="https://github.com/sofiiafedotova/gusli-arch/blob/main/psaltery.ru/psaltery_archready.png" width=40%>

В соответствии с метриками [CLEAR](https://purl.pt/24107/1/iPres2013_PDF/CLEAR%20a%20credible%20method%20to%20evaluate%20website%20archivability.pdf) результат анализа заглавной страницы сайта Psaltery совсем невысок для статического сайта.

Среди проблем: 
- несоответствие стандартам оформления html (312 ошибок!),
- использование скрипта Яндекс Метрики,
- неработающие ссылки (о них подробнее в [ДЗ-3](https://docs.google.com/document/d/1f-kO3AqsL6svDK47ACjDQuDXHlSt5qrehDGTmOIzExs/edit?tab=t.0)),
- встроенный в html javascript,
- отсутствие заголовков кэширования http,
- замедленный отклик сети (1370 ms),
- отсутствие карты сайта и др.

## 2. Анализ: metawarc
1) `metawarc analyze psaltery.ru.warc.gz`
```{
mimes                            files       size         share
-----------------------------  -------  ---------  ------------
video/mp4                           14  326784498   58.8312
image/jpeg                         590  209319730   37.6839
text/html                          125    6306511    1.13536
image/png                           80    6149309    1.10706
image/svg+xml                        6    1921004    0.345839
application/javascript              33     953862    0.171724
text/css                            36     859043    0.154654
application/pdf                      1     617970    0.111253
application/vnd.ms-fontobject        5     525303    0.0945705
application/x-font-ttf               4     516350    0.0929587
application/json                    57     410618    0.0739237
application/rss+xml                 11     367783    0.0662121
application/x-font-woff              4     288135    0.0518731
text/plain                           6     235144    0.0423331
image/gif                           19     103740    0.0186764
text/xml                            29     101264    0.0182306
application/xml                      1       1318    0.00023728
#total                            1021  555461582  100
```
Анализ метаданных показал, что на сайте содержится много разных файлов. Это объясняется тем, что wpull скачал по сути два сайта: первый (psaltery.ru) - статический, очень простой, и второй (на поддомене gusli.psaltery.ru) - с более сложной динамической структурой (создан на конструкторе сайтов WordPress; wpull захватил и его, т.к. на статическом сайте расположена ссылка на новую версию сайта). 58% места в архиве занимают видеозаписи (все они расположены на поддоменном сайте), 38% – фото в разных форматах, на текст приходится 1%.

2) `metawarc index psaltery.ru.warc.gz` -> создается БД (`psaltery_meta.db`)

3) `metawarc stats -m mimes`

| mime                               | size      | count |
|------------------------------------|-----------|-------|
| application/javascript             | 953,862   | 33    |
| application/json; charset=UTF-8    | 410,618   | 57    |
| application/pdf                    | 617,970   | 1     |
| application/rss+xml; charset=UTF-8 | 367,783   | 11    |
| application/vnd.ms-fontobject      | 525,303   | 5     |
| application/x-font-ttf             | 516,350   | 4     |
| application/x-font-woff            | 288,135   | 4     |
| application/xml                    | 1,318     | 1     |
| image/gif                          | 103,740   | 19    |
| image/jpeg                         | 209,319,730 | 590  |
| image/png                          | 6,149,309 | 80    |
| image/svg+xml                      | 1,921,004 | 6     |
| text/css                           | 859,043   | 36    |
| text/html                          | 345,528   | 23    |
| text/html; charset=UTF-8           | 5,958,316 | 96    |
| text/html; charset=iso-8859-1      | 2,667     | 6     |
| text/plain                         | 235,144   | 6     |
| text/xml; charset=UTF-8            | 101,264   | 29    |
| video/mp4                          | 326,784,498 | 14   |


Результаты идентичны, лишь только подробнее указаны характеристики html-текста.

3) ` metawarc export -t headers -o psaltery_headers.jsonl psaltery.ru.warc.gz`

Извлекаем заголовки http, чтобы проверить, действительно ли на сайте отсутствуют заголовки кэширования. Результат сохраняем в файле `psaltery_headers.jsonl`

У статического сайта характерных заголовков `Cache-control` нет вовсе, что подтверждает анализ ArchiveReady. У динамического (gusli.psaltery.ru) директивы ответа кэша выставлены на `no-cache`, т.е. на запрет кэширования содержимого.

4) `metawarc metadata --filetypes gif,jpeg,png,svg+xml --output psaltery_meta.jsonl psaltery.ru.warc.gz`

Результаты: файл `psaltery_meta.jsonl`. Сохранились метаданные всего для 101 изображения.

## 3. Воспроизведение: ReplayWeb.page

<img src="https://github.com/sofiiafedotova/gusli-arch/blob/main/psaltery.ru/psaltery_replay(1).png" width=80%>

Статический сайт скачался целиком.

<img src="https://github.com/sofiiafedotova/gusli-arch/blob/main/psaltery.ru/psaltery_replay(2).png" width=80%>

Удивительно, но и динамический (gusli.psaltery.ru), попавший в коллекцию случайно и скачанный тем же `wpull`-ом (хотя для сайтов на WordPress стоит использовать специальные утилиты, например, `wparc`), тоже собрался почти полностью. Сайт отображается корректно, не потеряны даже "красивые" переходы между страницами.
