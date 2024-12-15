# Сайт Центра музыкальных древностей В.И. Поветкина (centrpovetkina.ru)

## Ссылка на архив
[Перейти](https://drive.google.com/drive/folders/114blDcTalaqMrqOGl_gRItc-gCHSzAlR?usp=drive_link)

## 1. Анализ: Archive Ready

<img src="https://github.com/sofiiafedotova/gusli-arch/blob/main/centrpovetkina.ru/centrpovetkina_archready.png" width=40%>

В соответствии с метриками [CLEAR](https://purl.pt/24107/1/iPres2013_PDF/CLEAR%20a%20credible%20method%20to%20evaluate%20website%20archivability.pdf) результат анализа главной страницы сайта Центра Поветкина невысок. 
Среди проблем: 
- несоответствие стандартам оформления html,
- неработающие ссылки,
- встроенный в html javascript (динамический контент),
- отсутствие заголовков кэширования http,
- замедленный отклик сети (10850 ms),
- отсутствие карты сайта (sitemap.xml)
и др.

## 2. Анализ: metawarc
1) `metawarc analyze centrpovetkina.ru.warc.gz`
```{
mimes              files       size          share
---------------  -------  ---------  -------------
image/jpeg           536  147211484   95.7105
text/html            513    3952374    2.56966
image/png             30    2243642    1.45872
text/javascript       14     319632    0.207811
image/gif             18      41456    0.0269529
text/css              10      40204    0.0261389
text/plain             1        331    0.000215202
#total              1122  153809123  100
```
97% данных в архиве занимают изображения в формате jpeg и png, доля в 2.5% приходится на html-текст.

2) `metawarc index centrpovetkina.ru.warc.gz` -> создается БД (`centrpovetkina_meta.db`)

3) `metawarc stats -m mimes`

| mime                        | size      | count |
|-----------------------------|-----------|-------|
| image/gif                   | 82912     | 36    |
| image/jpeg                  | 294422968 | 1072  |
| image/png                   | 4487284   | 60    |
| text/css                    | 80408     | 20    |
| text/html; charset=UTF-8    | 3163028   | 538   |
| text/html; charset=utf-8    | 4741720   | 488   |
| text/javascript             | 639264    | 28    |
| text/plain; charset=utf-8   | 662       | 2     |

3) ` metawarc export -t headers -o centrpovetkina_headers.jsonl centrpovetkina.ru.warc.gz`

Извлекаем заголовки http, чтобы проверить, действительно ли на сайте отсутствуют заголовки кэширования. Результат сохраняем в файле `centrpovetkina_headers.jsonl`

Действительно, везде, где встречаются характерные заголовки `Cache-control` директивы ответа кэша выставлены на `no-store, no-cache`: явно указывает на то, что сервер запрещает кэширование содержимого.

4) `metawarc metadata --output centrpovetkina_meta.jsonl centrpovetkina.ru.warc.gz`

Результаты: файл `centrpovetkina_meta.jsonl` и файл `centrpovetkina_errors.txt`. Метаданные для изображений сохранились, но не всегда успешно.

## 3. Воспроизведение: ReplayWeb.page

<img src="https://github.com/sofiiafedotova/gusli-arch/blob/main/centrpovetkina.ru/centrpovetkina_replay.png" width=80%>

Сайт архивировался удачно. Для примера: главная страница. Мы видим, что потеряно одно изображение (оно указано как картинка с внешнего ресурса среди проблем на ArchiveReady) -- но это логотип TripAdvisor, которым не жалко пожертвовать.
