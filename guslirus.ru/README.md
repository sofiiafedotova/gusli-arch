# Обучающий сайт музыканта и педагога Натальи Федоровой (СПб) "Гусли звончатые от А до Я" (guslirus.ru)


## Ссылка на архив
**Ссылка**: [Перейти](https://drive.google.com/drive/folders/1RbxKzEfS9QS1QK8z8a8iejnUgOsdPVLy?usp=drive_link)

**Состав**: 
- файл cdx (99 кб)
- файл с логами (1.2 мб)
- файл warc (280.7 мб)
- файл .db (148 кб) 

## 1. Анализ: Archive Ready

<img src="https://github.com/sofiiafedotova/gusli-arch/blob/main/guslirus.ru/guslirus_archready.png" width=40%>

В соответствии с метриками [CLEAR](https://purl.pt/24107/1/iPres2013_PDF/CLEAR%20a%20credible%20method%20to%20evaluate%20website%20archivability.pdf) результат анализа заглавной страницы сайта “Гусли звончатые от А до Я” - самый высокий среди выбранных мною сайтов. 

Среди проблем: 
- несоответствие стандартам оформления html и css
- неработающие ссылки,
- встроенный в html javascript,
- отсутствие заголовков кэширования http,
- замедленный отклик сети (1170 ms)
и др.

## 2. Анализ: metawarc
1) `metawarc analyze guslirus.ru.warc.gz`
```{
mimes              files       size          share
---------------  -------  ---------  -------------
audio/mpeg            71  230436357   75.8732
application/pdf       64   46583449   15.338
image/jpeg           123   19468427    6.41015
text/html            227    5775127    1.90151
image/png              8     784228    0.258214
image/gif             38     627197    0.20651
text/xml               2      25992    0.00855809
text/css               2      10805    0.00355764
text/plain             1       1060    0.000349014
#total               536  303712642  100
```
76% данных в архиве сайта занимают аудиозаписи, 15% - pdf-файлы (например, с нотами), почти 7% приходятся на изображения в разных форматах и всего 2% - на текст.

2) `metawarc index guslirus.ru.warc.gz` -> создается БД (`guslirus_meta.db`)

3) `metawarc stats -m mimes`

| mime                          | size      | count |
|-------------------------------|-----------|-------|
| application/pdf                | 46,583,449 | 64    |
| audio/mpeg                     | 230,436,357 | 71   |
| image/gif                      | 627,197   | 38    |
| image/jpeg                     | 19,468,427 | 123  |
| image/png                      | 784,228   | 8     |
| text/css                       | 10,805    | 2     |
| text/html                      | 1,013,393 | 123  |
| text/html; charset=iso-8859-1  | 46,650    | 89    |
| text/html; charset=utf-8       | 4,715,084 | 15    |
| text/plain                     | 1,060     | 1     |
| text/xml                       | 25,992    | 2     |

Результаты идентичны, лишь только подробнее указаны характеристики html-текста.

3) ` metawarc export -t headers -o guslirus_headers.jsonl guslirus.ru.warc.gz`

Извлекаем заголовки http, чтобы проверить, действительно ли на сайте отсутствуют заголовки кэширования. Результат сохраняем в файле `guslirus_headers.jsonl`

Под характерными заголовками `Cache-control` директивы ответа кэша выставлены на `max-age=604800` (означает, что ресурс можно кэшировать в течение 604800 секунд, т.е. 7 дней). Мы выяснили, что ArchiveReady слишком сурово отнесся к этому пункту и даже занизил оценку архивируемости сайта. 

4) `metawarc metadata --output guslirus_meta.jsonl guslirus.ru.warc.gz`

Результаты: файл `guslirus_meta.jsonl` и файл `guslirus_errors.txt`. Метаданные для изображений сохранились, но не всегда успешно.

## 3. Воспроизведение: ReplayWeb.page

<img src="https://github.com/sofiiafedotova/gusli-arch/blob/main/guslirus.ru/guslirus_replay.png" width=80%>

Просмотр warc-файла через ReplayWebpage показал, что сайт скачался целиком, в т.ч. успешно загрузились те ссылки, которые ArchiveToday обозначил как "неработающие".
