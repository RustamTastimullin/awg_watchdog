# awg_watchdog

<br>Скрипт предназначен для перезапуска контейнера с AWG в случае потери соединения. </br>
<br>Принцип работы: </br>
- скрипт запускается на сервере, где развернут docker-контейнер с впн
- по CRON проверяет "живой ли" handshake у awg c любым из клиентов
- если работа корректна, то ничего не происходит
- иначе запускается цикл рестартов с сохранением логов и отправкой в ТГ сообщения

<br>Инструкция по добавлению скрипта awg_watchdog_script. </br>

1. Вход на сервер.

2. Убедимся в наличии контейнеров (и правильности их наименований для скрипта):
<br>проверка контейнеров: </br>
<code>docker ps --filter "name=amnezia-awg"</code>
<code>docker ps --filter "name=amnezia-awg2" (если установлен протокол 2.0)</code>
<br>проверка HS (хэндшейк'ов): </br>
<code>docker exec amnezia-awg /usr/bin/wg show all latest-handshakes</code>
<code>docker exec amnezia-awg2 /usr/bin/wg show all latest-handshakes</code>
<br>проверка фильтрации (по скрипту): </br>
<code>docker exec amnezia-awg2 /usr/bin/wg show all latest-handshakes | awk '$3 > 0 {print $3}' | sort -nr | head -n 1</code>

3. Добавление файла скрипта: </br>
<code>sudo nano /usr/local/bin/awg_watchdog.sh</code>
<br>проверить что создался скрипт: </br>
<code>ls -l /usr/local/bin/awg_watchdog.sh</code>
<br>быстрый просмотр содержимого (без редактирования): </br>
<code>cat /usr/local/bin/awg_watchdog.sh</code>

4. Делаем скрипт исполняемым: </br>
<code>sudo chmod +x /usr/local/bin/awg_watchdog.sh</code>

5. Подготовка логов: </br>
<code>sudo touch /var/log/awg_watchdog.log</code>
<code>sudo chmod 666 /var/log/awg_watchdog.log</code>
<br>проверка что файл лог создался: </br>
<code>ls -l /var/log/awg_watchdog.log</code>

6. Ручной запуск (для отладки перед тем как вешать процесс по крон): </br>
<code>sudo /usr/local/bin/awg_watchdog.sh</code>

7. Настройка автозапуска скрипта (с блоком процесса чтобы избежать гонок): </br>
<code>sudo crontab -e (nano)</code>
<code>*/3 * * * * /usr/bin/flock -n /tmp/awg_watchdog.lock /usr/local/bin/awg_watchdog.sh > /dev/null 2>&1</code>

Дополнительно: </br>
просмотр лога: </br>
<code>cat /var/log/awg_watchdog.log</code>
<br>просмотр в реальном времени: </br>
<code>tail -f /var/log/awg_watchdog.log</code>
