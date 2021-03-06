.. Fedora-Faq-Ru (c) 2018 - 2019, EasyCoding Team and contributors
.. 
.. Fedora-Faq-Ru is licensed under a
.. Creative Commons Attribution-ShareAlike 4.0 International License.
.. 
.. You should have received a copy of the license along with this
.. work. If not, see <https://creativecommons.org/licenses/by-sa/4.0/>.
.. _using-system:

*********************************************
Вопросы, связанные с работой в системе
*********************************************

.. index:: autocompletion, bash
.. _autocompletion:

У меня в системе не работает автодополнение команд. Как исправить?
=====================================================================

Необходимо установить пакет sqlite:

.. code-block:: bash

    sudo dnf install sqlite

При определённых условиях он может не быть установлен и из-за этого система автоматического дополнения команд может перестать функционировать.

.. index:: backup
.. _backup-system:

Можно ли делать резервную копию корневого раздела работающей системы?
=========================================================================

Настоятельно не рекомендуется из-за множества работающих виртуальных файловых систем и псевдофайлов в **/sys**, **/dev**, **/proc** и т.д.

.. index:: backup
.. _backup-home:

Как сделать копию домашнего каталога?
=========================================

См. `здесь <https://www.easycoding.org/2017/09/03/avtomatiziruem-rezervnoe-kopirovanie-v-fedora.html>`__.

.. index:: backup
.. _backup-create:

Как лучше всего делать резервную копию корневого раздела?
=============================================================

Необходимо загрузиться с LiveCD или LiveUSB, смонтировать раздел с корневой файловой системой и выполнить:

.. code-block:: bash

    sudo tar --one-file-system --selinux \
    --exclude="$bdir/tmp/*" \
    --exclude="$bdir/var/tmp/*" \
    -cvJpf /путь/к/бэкапу.tar.xz -C /путь/к/корню .

.. index:: initrd, rebuild initrd
.. _initrd-rebuild:

Как мне пересобрать образ initrd?
====================================

Для пересборки образа initrd следует выполнить:

.. code-block:: bash

    sudo dracut -f

.. index:: boot, grub
.. _grub-reinstall:

Как мне переустановить Grub 2?
====================================

См. `здесь <https://fedoraproject.org/wiki/GRUB_2>`__.

.. index:: boot, grub
.. _grub-rebuild:

Как пересобрать конфиг Grub 2?
====================================

Пересборка конфига Grub 2 для legacy конфигураций:

.. code-block:: bash

    sudo grub2-mkconfig -o /boot/grub2/grub.cfg

Пересборка конфигра Grub 2 для UEFI конфигураций:

.. code-block:: bash

    sudo grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg

.. index:: slow shutdown, shutdown
.. _slow-shutdown:

Система медленно завершает работу. Можно ли это ускорить?
============================================================

См. `здесь <https://www.easycoding.org/2016/08/08/uskoryaem-zavershenie-raboty-fedora-24.html>`__.

.. index:: firefox, hardware acceleration
.. _firefox-hwaccel:

Как активировать аппаратное ускорение в браузере Firefox?
=============================================================

Для активации аппаратного ускорения рендеринга страниц в Mozilla Firefox на поддерживаемых драйверах необходимо открыть модуль конфигурации **about:config** и исправить значения следующих переменных (при отсутствии создать):

.. code-block:: text

    layers.acceleration.force-enabled = true
    layers.offmainthreadcomposition.enabled = true
    webgl.force-enabled = true
    gfx.xrender.enabled = true

Изменения вступят в силу при следующем запуске браузера.

Внимание! Это не затрагивает аппаратное декодирование мультимедиа средствами видеоускорителя.

.. index:: firefox, chromium, chrome, hardware acceleration, vaapi
.. _browser-hwaccel:

Как активировать аппаратное ускорение декодирования мультимедиа в браузерах?
===============================================================================

В настоящее время аппаратное ускорение декодирования мультимедиа "из коробки" в GNU/Linux не поддерживается ни в одном браузере.

В Mozilla Firefox оно вообще не реализовано: `MZBZ#563206 <https://bugzilla.mozilla.org/show_bug.cgi?id=563206>`__ и `MZBZ#1210727 <https://bugzilla.mozilla.org/show_bug.cgi?id=1210727>`__.

В Google Chrome и Chromium частично реализовано, но отключено на этапе компиляции и без особых VA-API патчей недоступно. Репозиторий :ref:`RPM Fusion <rpmfusion>` предоставляет такую сборку Chromium. Для её установки необходимо подключить его и установить пакет **chromium-vaapi**:

.. code-block:: bash

    sudo dnf install chromium-vaapi

Далее необходимо запустить его, зайти в **chrome://flags** и установить пункт **Hardware decoding** в значение **Enabled**, после чего перезапустить браузер.

.. index:: mpv, video player, hardware acceleration, vaapi, vdpau
.. _video-hwaccel:

В каких проигрывателях реализовано аппаратное ускорение декодирования мультимедиа?
=====================================================================================

Полная поддержка аппаратного декодирования мультимедиа средствами VA-API (Intel, AMD) или VPDAU (NVIDIA) реализована в проигрывателях VLC и mpv.

Для активации данной функции необходимо в качестве графического бэкэнда вывода изображения указать **vaapi** или **vdpau**, после чего перезапустить плеер.

.. index:: gdb, debugging, segfault, segmentation fault
.. _debug-application:

Приложение падает. Как мне его отладить?
===========================================

Для начала рекомендуется (хотя и не обязательно) установить отладочную информацию для данного пакета:

.. code-block:: bash

    sudo dnf debuginfo-install foo-bar

После завершения процесса отладки символы можно снова удалить.

Чтобы получить бэктрейс падения, нужно выполнить в терминале:

.. code-block:: bash

    gdb /usr/bin/foo-bar 2>&1 | tee ~/backtrace.log

Далее в интерактивной консоли отладчика ввести: **handle SIGPIPE nostop noprint** и затем **run**, дождаться сегфолта и выполнить **bt full** для получения бэктрейса. Теперь можно прописать **quit** для выхода из режима отладки.

Далее получившийся файл **~/backtrace.log** следует загрузить на любой сервис размещения текстовых файлов.

Также рекомендуется ещё сделать трассировку приложения до момента падения:

.. code-block:: bash

    strace -o ~/trace.log /usr/bin/foo-bar

Полученный файл **~/trace.log** также следует загрузить на сервис.

.. index:: converting multiple files, convert
.. _convert-multiple-files:

Как конвертировать множество файлов в mp3 из текущего каталога?
===================================================================

Конвертируем все файлы с маской \*.ogg в mp3 в текущем каталоге:

.. code-block:: bash

    find . -maxdepth 1 -type f -name "*.ogg" -exec ffmpeg -i "{}" -acodec mp3 -ab 192k "$(basename {}).mp3" \;

.. index:: kde, gtk, double-click
.. _double-click-speed:

Я использую KDE. Как мне настроить скорость двойного клика в GTK приложениях?
==================================================================================

Для настройки GTK 2 приложений необходимо открыть файл **~/.gtkrc-2.0** в любом текстовом редакторе (если он отсутствует — создать), затем прописать в самом конце:

.. code-block:: text

    gtk-double-click-time=1000

Для GTK 3 нужно редактировать **~/.config/gtk-3.0/settings.ini**. В нём следует прописать то же самое:

.. code-block:: text

    gtk-double-click-time=1000

Здесь 1000 — время в миллисекундах до активации двойного клика. Документация с подробным описанием всех переменных данных файлов конфигурации `здесь <https://developer.gnome.org/gtk3/stable/GtkSettings.html>`__.

.. index:: console, lock screen, lock session
.. _block-screen:

Возможно ли заблокировать экран из командной строки?
=======================================================

Да:

.. code-block:: bash

    loginctl lock-session

.. index:: bash
.. _bash-shell:

Можно ли изменить приветствие Bash по умолчанию?
===================================================

Да, необходимо в пользовательский файл **~/.bashrc** добавить строку вида:

.. code-block:: text

    export PS1="\[\e[33m\][\[\e[36m\]\u\[\e[0m\]@\[\e[31m\]\h\[\e[0m\] \[\e[32m\]\W\[\e[33m\]]\[\e[35m\]\$\[\e[0m\] "

Существует удобный онлайн генератор таких строк `здесь <http://bashrcgenerator.com/>`__.

.. index:: bash, title, console
.. _bash-title:

Можно ли из shell скрипта менять название терминала?
=======================================================

Да, при помощи `управляющих последовательностей <https://ru.wikipedia.org/wiki/%D0%A3%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D1%8F%D1%8E%D1%89%D0%B8%D0%B5_%D0%BF%D0%BE%D1%81%D0%BB%D0%B5%D0%B4%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D1%8C%D0%BD%D0%BE%D1%81%D1%82%D0%B8_ANSI>`__. Ими же можно менять цвет текста вывода и многое другое.

.. index:: time, synchronization, ntp, network
.. _configure-ntp:

Как настроить синхронизацию времени?
=======================================

В Fedora для этой цели используется chronyd, который установлен и запущен по умолчанию.

Чтобы узнать включена ли синхронизация времени с NTP серверами, можно использовать утилиту **timedatectl**.

Если синхронизация отключена, нужно убедиться, что сервис chronyd активирован:

.. code-block:: bash

    sudo systemctl enable chronyd.service

Получить список NTP серверов, с которыми осуществляется синхронизация, можно так:

.. code-block:: bash

    chronyc sources

.. index:: systemd, boot, speed
.. _systemd-analyze:

Как узнать какой сервис замедляет загрузку системы?
======================================================

.. code-block:: bash

    systemd-analyze blame

.. index:: multimedia, encoding, nvidia
.. _nvidia-encoding:

Как ускорить кодирование видео с использованием видеокарт NVIDIA?
====================================================================

Для этого нужно установить ffmpeg, а также проприетарные драйверы NVIDIA из репозиториев :ref:`RPM Fusion <rpmfusion>`.

Использование NVENC:

.. code-block:: bash

    ffmpeg -i input.mp4 -acodec aac -ac 2 -ab 128k -vcodec h264_nvenc -profile high444p -pixel_format yuv444p -preset default output.mp4

Использование CUDA/CUVID:

.. code-block:: bash

    ffmpeg -c:v h264_cuvid -i input.mp4 -c:v h264_nvenc -preset slow output.mkv

Здесь **input.mp4** — имя оригинального файла, который требуется перекодировать, а в **output.mp4** будет сохранён результат.

Больше информации можно найти `здесь <https://trac.ffmpeg.org/wiki/HWAccelIntro>`__.

.. index:: file system, selection, fs
.. _fs-selection:

Какую файловую систему рекомендуется использовать на Fedora?
================================================================

По умолчанию применяется `ext4 <https://ru.wikipedia.org/wiki/Ext4>`__. На наш взгляд, это самая стабильная и популярная файловая система в настоящее время.

Для хранения больших объёмов данных можно использовать `XFS <https://ru.wikipedia.org/wiki/XFS>`__.

.. index:: file system, fs, btrfs
.. _fs-btrfs:

Что вы скажете о BTRFS?
===========================

Мы настоятельно не рекомендуем её использовать. Данная ФС очень нестабильна и часто приводит к полной потере всех данных на устройстве без возможности восстановления даже в идеальных условиях (было множество случаев у пользователей нашего канала).

.. index:: window, borders, kde plasma, kde
.. _window-borders:

Как убрать рамки внутри окон в KDE Plasma 5?
===============================================

Для этого следует открыть **Меню KDE** - **Компьютер** - **Параметры системы** - **Оформление приложений** - страница **Стиль интерфейса** - кнопка **Настроить** - вкладка **Рамки**, **убрать все флажки** из чекбоксов на данной странице и нажать кнопку **OK**.

.. index:: window, gnome, scaling, scaling factor, hidpi, qt
.. _window-hidpi-qt:

У меня в Gnome не работает масштабирование окон Qt приложений. Что делать?
=============================================================================

Для активации автоматического масштабирования достаточно прописать в файле **~/.bashrc** следующие строки:

.. code-block:: text

    export QT_AUTO_SCREEN_SCALE_FACTOR=1
    export QT_SCALE_FACTOR=2

Переменная **QT_AUTO_SCREEN_SCALE_FACTOR** имеет тип boolean (значения **1** (включено) или **0** (выключено)) и управляет автоматическим масштабированием в зависимости от разрешения экрана.

Переменная **QT_SCALE_FACTOR** задаёт коэффициент масштабирования:

 * **1.5** - 150%;
 * **1.75** - 175%;
 * **2** - 200%;
 * **2.5** - 250%;
 * **3** - 300%.

Более подробную информацию можно найти в `документации Qt <https://doc.qt.io/qt-5/highdpi.html>`__.

.. index:: telegram, im
.. _telegram-fedora:

Как лучше установить Telegram Desktop в Fedora?
===================================================

Мы настоятельно рекомендуем устанавливать данный мессенджер исключительно из :ref:`RPM Fusion <rpmfusion>`:

.. code-block:: bash

    sudo dnf install telegram-desktop

Данная версия собрана и динамически слинкована с использованием исключительно штатных системных библиотек, доступных в репозиториях Fedora, а не давно устаревших и уязвимых версий из комплекта Ubuntu 14.04, как официальная.

Сборка Fedora поддерживает системные настройки тем, правильное сглаживание шрифтов (за счёт использование общесистемных настроек) и не имеет проблем со скоростью запуска.

.. index:: telegram, cleanup, im
.. _telegram-cleanup:

Ранее я устанавливал официальную версию Telegram Desktop. Как мне очистить её остатки?
=========================================================================================

Официальная версия с сайта создаёт ярлыки запуска и копирует ряд загруженных бинарных файлов в пользовательский домашний каталог. Избавимся от этого:

 1. удалим старый бинарник и модуль обновления официального клиента, а также их копии из **~/.local/share/TelegramDesktop** и **~/.local/share/TelegramDesktop/tdata**;
 2. удалим ярлыки из **~/.local/share/applications**.

Теперь можно установить :ref:`версию <telegram-fedora>` из :ref:`RPM Fusion <rpmfusion>`.

.. index:: sddm, dm, disable virtual keyboard, keyboard
.. _sddm-disable-vkb:

Как отключить виртуальную клавиатуру в SDDM?
=================================================

Чтобы отключить поддержку ввода с виртуальной экранной клавиатуры в менеджере входа в систему SDDM, откроем в текстовом редакторе файл **/etc/sddm.conf**, а затем найдём и удалим следующую строку:

.. code-block:: text

    InputMethod=qtvirtualkeyboard

Если она отсутствует, создадим в блоке **[General]**:

.. code-block:: text

    InputMethod=

Изменения вступят в силу при следующей загрузке системы.

.. index:: file system, fs, exfat, fuse
.. _fedora-exfat:

Почему я не могу использовать файловую систему exFAT в Fedora?
===================================================================

Файловая система exFAT защищена множеством патентов Microsoft, поэтому она не может быть включена в ядро Linux и соответственно быть доступной в Fedora по умолчанию.

Для того, чтобы использовать её, необходимо установить пакет **fuse-exfat** из :ref:`репозитория <3rd-repositories>` :ref:`RPM Fusion <rpmfusion>`:

.. code-block:: bash

    sudo dnf install fuse fuse-exfat

.. index:: latex, editor
.. _latex-editor:

В репозиториях есть полнофункциональные редакторы LaTeX?
===========================================================

Да. Для работы с документами в формате LaTeX рекомендуется использовать **texmaker**:

.. code-block:: bash

    sudo dnf install texmaker

.. index:: latex, texlive, cyrillic, fonts
.. _latex-cyrillic:

Как установить поддержку кириллических шрифтов для LaTeX?
=============================================================

Наборы кириллических шрифтов доступны в виде коллекции:

.. code-block:: bash

    sudo dnf install texlive-collection-langcyrillic texlive-cyrillic texlive-russ texlive-babel-russian

.. index:: fuse, file system, mtp, android, phone
.. _fuze-mtp:

Как подключить смартфон на Android посредством протокола MTP?
================================================================

Для простой и удобной работы с файловой системой смартфона вне зависимости от используемых приложений, рабочей среды и файлового менеджера, мы рекомендуем использовать основанную на FUSE реализацию.

Установим пакет **jmtpfs**:

.. code-block:: bash

    sudo dnf install jmtpfs fuse

Создадим каталог, в который будет смонтирована ФС смартфона:

.. code-block:: bash

    mkdir -p ~/myphone

Подключим устройство к компьютеру или ноутбуку по USB, разблокируем его и выберем режим MTP, после чего выполним:

.. code-block:: bash

    jmtpfs ~/myphone

По окончании работы обязательно завершим MTP сессию:

.. code-block:: bash

    fusermount -u ~/myphone

.. index:: systemd, failed to start modules, kernel, virtualbox
.. _failed-to-start:

При загрузке системы появляется ошибка Failed to start Load Kernel Modules. Как исправить?
==============================================================================================

Это известная проблема системы виртуализации :ref:`VirtualBox <virtualbox>`, использующей out-of-tree модули ядра, но может также проявляться и у пользователей проприетарных :ref:`драйверов Broadcom <broadcom-drivers>`.

Для исправления необходимо **после каждого обновления ядра** выполнять пересборку initrd:

.. code-block:: bash

    sudo dracut -f

Для вступления изменений в силу требуется перезагрузка:

.. code-block:: bash

    sudo systemctl reboot

.. index:: keyring, kwallet, wallet
.. _kwallet-pam:

Как настроить автоматическую разблокировку связки ключей KWallet при входе в систему?
=========================================================================================

KDE предоставляет особый PAM модуль для автоматической разблокировки связки паролей KDE Wallet при входе в систему. Установим его:

.. code-block:: bash

    sudo dnf install pam-kwallet

Запустим менеджер KWallet (**Параметры системы** - группа **Предпочтения пользователя** - **Учётная запись** - страница **Бумажник** - кнопка **Запустить управление бумажниками**), нажмём кнопку **Сменить пароль** и укажем тот же самый пароль, который используется для текущей учётной записи.

Сохраняем изменения и повторно входим в систему.

.. index:: video, youtube, download
.. _youtube-download:

Как скачать видео с Youtube?
=================================

Скачать любое интересующее видео с Youtube, а также ряда других хостингов, можно посредством утилиты **youtube-dl**, доступной в основном репозитории Fedora:

.. code-block:: bash

    sudo dnf install youtube-dl

Скачивание видео с настройками по умолчанию в наилучшем качестве:

.. code-block:: bash

    youtube-dl -f bestvideo https://www.youtube.com/watch?v=XXXXXXXXXX

Иногда при скачивании видео в разрешении 4K с ключом **-f bestvideo** может не работать аппаратное ускорение при воспроизведении из-за того, что кодек vp9.2 не поддерживается аппаратными кодировщиками. В таких случаях необходимо явно указывать кодек (**-f bestvideo[vcodec=vp9]**).

Чтобы гарантировано скачать видео с указанным кодеком со звуком требуется дополнительно установить пакет **ffmpeg** из репозиториев :ref:`RPM Fusion <rpmfusion>`:

.. code-block:: bash

    sudo dnf install ffmpeg

В качестве примера скачаем видео в наилучшем качестве, сжатое кодеком VP9 (с возможностью аппаратного ускорения) и звуком:

.. code-block:: bash

    youtube-dl -f bestvideo[vcodec=vp9]+bestaudio https://www.youtube.com/watch?v=XXXXXXXXXX

Данная утилита имеет множество параметров командной строки, справку по которым можно найти в её странице man:

.. code-block:: bash

    man youtube-dl

Для выхода из окна просмотра справки достаточно нажать **Q**.

.. index:: iso, write iso, image
.. _fedora-winiso:

Как из Fedora записать образ с MS Windows на флешку?
========================================================

К сожалению, :ref:`штатный способ <usb-flash>` записи посредством использования утилиты dd не сработает в случае ISO образов MS Windows, поэтому для этого следует применять утилиту WoeUSB:

.. code-block:: bash

    sudo dnf install WoeUSB

.. index:: xdg, directories
.. _xdg-reallocate:

Как переместить стандартные каталоги для документов, загрузок и т.д.?
==========================================================================

Откроем файл **~/.config/user-dirs.dirs** в любом текстовом редакторе и внесём свои правки.

Стандартные настройки:

.. code-block:: ini

    XDG_DESKTOP_DIR="$HOME/Рабочий стол"
    XDG_DOCUMENTS_DIR="$HOME/Документы"
    XDG_DOWNLOAD_DIR="$HOME/Загрузки"
    XDG_MUSIC_DIR="$HOME/Музыка"
    XDG_PICTURES_DIR="$HOME/Изображения"
    XDG_PUBLICSHARE_DIR="$HOME/Общедоступные"
    XDG_TEMPLATES_DIR="$HOME/Шаблоны"
    XDG_VIDEOS_DIR="$HOME/Видео"

Применим изменения:

.. code-block:: bash

    xdg-user-dirs-update

Убедитесь, что перед применением изменений данные каталоги существуют, иначе будет выполнен сброс на стандартное значение.

.. index:: sddm, hidpi, scaling
.. _sddm-hidpi:

У меня HiDPI дисплей и в SDDM всё отображается очень мелко. Как настроить?
==============================================================================

Откроем файл **/etc/sddm.conf**:

.. code-block:: bash

    sudoedit /etc/sddm.conf

Добавим в самый конец следующие строки:

.. code-block:: ini

    [Wayland]
    EnableHiDPI=true
    
    [X11]
    EnableHiDPI=true

Сохраним изменения и перезапустим систему.

.. index:: sddm, avatar
.. _sddm-avatars:

Как отключить отображение пользовательских аватаров в SDDM?
===============================================================

Пользовательские аватары представляют собой файл **~/.face.icon**. При запуске SDDM пытается прочитать его для каждого существующего пользователя.

Для отключения данной функции откроем файл **/etc/sddm.conf**:

.. code-block:: bash

    sudoedit /etc/sddm.conf

Добавим в самый конец следующие строки:

.. code-block:: ini

    [Theme]
    EnableAvatars=false

Сохраним изменения и перезапустим систему.

.. index:: powertop, top, power
.. _power-usage:

Как узнать какие процессы больше всего разряжают аккумулятор ноутбука?
===========================================================================

Установим утилиту **powertop**:

.. code-block:: bash

    sudo dnf install powertop

Запустим её с правами суперпользователя:

.. code-block:: bash

    sudo powertop

Процессы, которые больше всех влияют на скорость разряда аккумуляторных батарей, будут отображаться в верхней части.

.. index:: system information, info
.. _system-info:

Как собрать информацию о системе?
=====================================

Установим утилиту **inxi**:

.. code-block:: bash

    sudo dnf install inxi

Соберём информацию о системе и выгрузим на fpaste:

.. code-block:: bash

    inxi -F | fpaste

На выходе будет сгенерирована уникальная ссылка, которую можно передать на :ref:`форум, в чат <get-help>` и т.д.

.. index:: networking, vpn, l2tp, ipsec
.. _nm-l2tp:

Мой провайдер использует L2TP. Как мне добавить его поддержку?
==================================================================

Плагин L2TP для Network Manager должен присутствовать в Workstation и всех spin live образах по умолчанию, но если его по какой-то причине нет (например была выборана минимальная установка netinstall), то добавить его можно самостоятельно.

Для Gnome/XFCE и других, основанных на GTK:

.. code-block:: bash

    sudo dnf install NetworkManager-l2tp-gnome

Для KDE:

.. code-block:: bash

    sudo dnf install plasma-nm-l2tp

После установки необходимо запустить модуль настройки Network Manager (графический или консольный), добавить новое VPN подключение с типом L2TP и указать настройки, выданные провайдером.

Однако следует помнить, что у некоторых провайдеров используется L2TP со специальными патчами Microsoft (т.н. win реализация), что может вызывать нестабильность и сбои при подключении. В таком случае рекомендуется приобрести любой недорогой роутер с поддержкой L2TP (можно б/у) и использовать его в качестве клиента для подключения к сети провайдера.

.. index:: text file, encoding, converting, iconv
.. _iconv-convert:

Как конвертировать текстовый файл из одной кодировки в другую?
==================================================================

Для быстрой перекодировки текстовых файлов из одной кодировки в другую можно использовать утилиту iconv.

Пример перекодировки файла из cp1251 (Windows-1251) в юникод (UTF-8):

.. code-block:: bash

    iconv -f cp1251 -t utf8 test.txt > result.txt

Здесь **test.txt** - исходный файл с неправильной кодировкой, а **result.txt** используется для записи результата преобразования.

.. index:: networking, network manager, nmcli, console, wi-fi
.. _nm-wificon:

Как подключиться к Wi-Fi из консоли?
========================================

Если ранее уже были созданы Wi-Fi подключения, то выведем их список:

.. code-block:: bash

    nmcli connection | grep wifi

Теперь запустим выбранное соединение:

.. code-block:: bash

    nmcli connection up Connection_Name

.. index:: networking, network manager, nmcli, console, wi-fi
.. _nm-wificli:

Как подключиться к Wi-Fi из консоли при отсутствии соединений?
==================================================================

Если :ref:`готовых соединений <nm-wificon>` для Wi-Fi нет, но известны SSID и пароль, то можно осуществить подключение напрямую:

.. code-block:: bash

    nmcli device wifi connect MY_NETWORK password XXXXXXXXXX

Здесь **MY_NETWORK** - название SSID точки доступа, к которой мы планируем подключиться, а **XXXXXXXXXX** - её пароль.

.. index:: kde connect, smartphone, kde
.. _kde-connect:

Как лучше работать со смартфоном посредством компьютера или ноутбука?
==========================================================================

Для простой и эффективной работы со смартфоном на базе ОС Android пользователи рабочей среды KDE Plasma 5 могут использовать KDE Connect:

.. code-block:: bash

    sudo dnf install kde-connect

Сначала установим клиент KDE Connect на смартфон:

 * `Google Play <https://play.google.com/store/apps/details?id=org.kde.kdeconnect_tp>`__;
 * `F-Droid <https://f-droid.org/packages/org.kde.kdeconnect_tp/>`__;

Запустим плазмоид KDE Connect и выполним сопряжение.

.. index:: kde connect, firewalld
.. _kde-connect-firewalld:

KDE Connect не видит мой смартфон. Как исправить?
======================================================

Добавим правило, разрешающее входящие соединения к сервису kdeconnectd посредством :ref:`Firewalld <firewalld-about>`:

.. code-block:: bash

    sudo firewall-cmd --add-service=kde-connect --permanent

Применим новые правила:

.. code-block:: bash

    sudo firewall-cmd --reload

.. index:: text, editor, text editor, console
.. _editor-selection:

Как выбрать предпочитаемый текстовый редактор в консольном режиме?
=======================================================================

Для выбора предпочитаемого текстового редактора следует применять переменные окружения, прописав их в личном файле **~/.bashrc**:

.. code-block:: bash

    export VISUAL=vim
    export EDITOR=vim
    export SUDO_EDITOR=vim

**VISUAL** - предпочитаемый текстовый редактор с графическим интерфейсом пользователя, **EDITOR** - текстовый, а **SUDO_EDITOR** используется в :ref:`sudoedit <sudo-edit-config>`.

.. index:: text, editor, git, text editor
.. _editor-git:

Как выбрать предпочитаемый текстовый редактор для Git?
===========================================================

Хотя Git подчиняется настройкам :ref:`редактора по умолчанию <editor-selection>`, допустимо его указать явно в файле конфигурации:

.. code-block:: bash

    git config --global core.editor vim

.. index:: iso, image, mount
.. _iso-mount:

Как смонтировать ISO образ в Fedora?
========================================

Создадим точку монтирования:

.. code-block:: bash

    sudo mkdir /mnt/iso

Смонтируем файл образа:

.. code-block:: bash

    sudo mount -o loop /path/to/image.iso /mnt/iso

По окончании произведём размонтирование:

.. code-block:: bash

    sudo umount /mnt/iso

.. index:: iso, image
.. _iso-create:

Как считать содержимое CD/DVD диска в файл ISO образа?
==========================================================

Для этого можно воспользоваться утилитой **dd**:

.. code-block:: bash

    sudo dd if=/dev/sr0 of=/path/to/image.iso bs=4M status=progress

Здесь **/dev/sr0** имя устройства привода для чтения оптических дисков, а **/path/to/image.iso** - файл образа, в котором будет сохранён результат.

.. index:: dd, disk, drive, image
.. _dd-mount:

Как смонтировать посекторный образ раздела?
================================================

Монтирование raw образа раздела, созданного посредством утилиты **dd**:

.. code-block:: bash

    sudo mount -o ro,loop /path/to/image.raw /mnt/dd-image

Размонтирование:

.. code-block:: bash

    sudo umount /mnt/dd-image

Здесь **/path/to/image.iso** - файл образа на диске.

.. index:: dd, disk, drive, image
.. _dd-fullraw:

Как смонтировать посекторный образ диска целиком?
======================================================

Смонтировать образ диска целиком напрямую не получится, поэтому сначала придётся определить смещения разделов относительно его начала.

Запустим утилиту **fdisk** и попытаемся найти внутри образа разделы:

.. code-block:: bash

    sudo fdisk -l /path/to/image.raw

Из вывода нам необходимо узнать значение **Sector size**, а также **Start** всех необходимых разделов.

Вычислим смещение относительно начала образа для каждого раздела по формуле **Start * Sector size**. К примеру если у первого Start равно 2048, а Sector size диска 512, то получим 2048 * 512 == 1048576.

Произведём монтирование раздела по смещению 1048576:

.. code-block:: bash

    sudo mount -o ro,loop,offset=1048576 /path/to/image.raw /mnt/dd-image

Повторим операции для всех остальных разделов, обнаруженных внутри образа. По окончании работы выполним размонтирование:

.. code-block:: bash

    sudo umount /mnt/dd-image

Здесь **/path/to/image.iso** - файл образа на диске.

.. index:: timezone
.. _set-timezone:

Как изменить часовой пояс?
==============================

Изменить часовой пояс можно посредством утилиты **timedatectl**:

.. code-block:: bash

    sudo timedatectl set-timezone Europe/Moscow

.. index:: keyboard, layout, gui
.. _set-keyboard-gui:

Как изменить список доступных раскладок клавиатуры и настроить их переключение в графическом режиме?
========================================================================================================

Настройка переключения по **Alt + Shift**, раскладки EN и RU:

.. code-block:: bash

    sudo localectl set-x11-keymap us,ru pc105 "" grp:alt_shift_toggle

Настройка переключения по **Ctrl + Shift**, раскладки EN и RU:

.. code-block:: bash

    sudo localectl set-x11-keymap us,ru pc105 "" grp:ctrl_shift_toggle

.. index:: keyboard, layout, console, text mode
.. _set-keyboard-console:

Как изменить список доступных раскладок клавиатуры и настроить их переключение в текстовом режиме?
======================================================================================================

Установка русской раскладки и режимов переключения по умолчанию (**Alt + Shift**):

.. code-block:: bash

    sudo localectl set-keymap ru

Установка русской раскладки и режима переключения **Alt + Shift**:

.. code-block:: bash

    sudo localectl set-keymap ruwin_alt_sh-UTF-8

Установка русской раскладки и режима переключения **Ctrl + Shift**:

.. code-block:: bash

    sudo localectl set-keymap ruwin_ct_sh-UTF-8

.. index:: kde, plasma, gtk, styles
.. _gtk-plasma-style:

Можно ли заставить GTK приложения выглядеть нативно в KDE?
==============================================================

Установим пакет с темой Breeze для GTK2 и GTK3:

.. code-block:: bash

    sudo dnf install breeze-gtk

Зайдём в **Параметры системы** - **Внешний вид** - **Оформление приложений** - **Стиль программ GNOME (GTK+)**.

Выберем **Breeze** (при использовании тёмной темы в KDE - **Breeze Dark**) в качестве темы GTK2 и GTK3, а также укажем шрифт, который будет использовать при отображении диалоговых окон.

Также установим **Breeze** для курсоров мыши и темы значков. Применим изменения и перезапустим все GTK приложения.
