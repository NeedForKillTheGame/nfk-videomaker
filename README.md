NFK Video Maker
=============

Автоматизирует создание видео из демок для игры [Need For Kill](http://needforkill.ru). 

Набор из трех утилит для развертывания на выделенном сервере (требуется видеокарта для запуска игры). 

Заложена возможность параллельного запуска на разных серверах с передачей согласованных результатов на сайт статистики. Для этого достаточно изменить `appid` в [config.xml](https://github.com/HarpyWar/nfk-demomaker/blob/master/Helper/config.xml).


![](http://i.imgur.com/MZYuFaM.png)

Проект запущен на выделенном сервере и продюсирует видео от последних сыгранных игр на http://www.youtube.com/user/needforkilldemo

ndm2video.exe
--
*Создает видео файл из демки. Консольная программа, может работать отдельно с параметрами запуска через командную строку.*
- в autoexec.cfg добавляются команды в числе которых avi_start
- запускается nfk.exe
- при появлении первой картинки в basenfk, меняется следуемый игрок путем посыла нескольких команд nextplayer
- здесь же запускается утилита ffmpeg с захватом видео из окна игры
- по окончании проигрывания демки (время задается в параметрах запуска) nfk.exe убивается, а за ним и ffmpeg закрывается сам с готовым видео файлом
- запускается снова ffmpeg для соединения видео файла заставки с созданным видео демки.
- после через ffmpeg запускается кодирование окончательного варианта с оптимальным качеством для закачки на youtube

ndmscheduler.exe
--
*Запрашивает информацию о демке из API сайта статистики, скачивает файл демки запускает `ndm2video.exe` для создания из нее видео файла. При запуске не создает окон, предназначена для запуска по планировщику (раз в минуту с запретом дублирующего процесса).*
- скачивает демку с сайта статистики, для которой еще не создано видео
- запускает ndm2video с необходимыми параметрами столько раз, сколько игроков в демке - по одному видео от первого лица для каждого игрока 
- при успешном создании всех видео для всех игроков создает json файл с информацией о демке

ndmuploader.exe
--
*Работает отдельно, загружает видео на Youtube. При запуске не создает окон, предназначена для запуска по планировщику (раз в минуту с запретом дублирующего процесса). Возможно добавить несколько заданий в планировщике, переименовав в разные файлы nfkuploader1.exe, nfkuploader2.exe, и т.д.  &mdash; это позволит загружать видео файлы в несколько потоков (скорость при загрузке в один поток на Youtube ограничена).*
- мониторит json файлы, если такие есть, читает из них информацию о демке, имена видео файлов для закачки на YouTube
- загружает файл демки на файловый хостинг mega.co.nz
- загружает видео файлы на YouTube, в описание вставляется информация о демке, ссылка на сайт статистики и ссылка на скачивание файла демки с mega.co.nz 

- по окончании загрузки посылает YouTube ссылки на сайт статистики
- по окончании загрузки выполняет команду из опции конфига `YoutubeUploadCompleteExec` (по-умолчанию записывает текст в `message.txt`)

[irccd](https://redmine.malikania.fr/projects/irccd)
--
*Irc bot, запускается отдельно в качестве службы.*
- `message.lua` плагин, в новом потоке проверяет файл `message.txt` на обновления
- при обнаружении изменения файла добавляет в очередь текст из файла, и через 20 минут посылает его на канал `#nfk` (с задержкой &mdash; чтобы видео успелось обработаться на Youtube)
- дополнительно, с запуском раз в 5-10 минут, в планировщик можно добавить Powershell скрипт `planetchecker.ps1`, он будет отправлять сообщение на канал `#nfk` через `irccdctl.exe`, когда на NFK планете в игре не хватает одного игрока

-------
* [Запись видео через ffmpeg](https://github.com/HarpyWar/nfk-videomaker/wiki/Capture-video-ffmpeg)
* [API методы сайта статистики](https://github.com/HarpyWar/nfk-videomaker/wiki/NFK-API-methods)
