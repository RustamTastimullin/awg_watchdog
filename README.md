# awg_watchdog

Инструкция по скрипту awg_watchdog_script.

1. Вход на сервер.

2. Убедимся в наличии контейнеров (и правильности их наименований для скрипта):
проверка контейнеров:
docker ps --filter "name=amnezia-awg"
docker ps --filter "name=amnezia-awg2" (если установлен протокол 2.0)
проверка HS (хэндшейк'ов)
docker exec amnezia-awg /usr/bin/wg show all latest-handshakes
docker exec amnezia-awg2 /usr/bin/wg show all latest-handshakes
проверка фильтрации (по скрипту):
docker exec amnezia-awg2 /usr/bin/wg show all latest-handshakes | awk '$3 > 0 {print $3}' | sort -nr | head -n 1

2. Добавление файла скрипта:
sudo nano /usr/local/bin/awg_watchdog.sh
проверить что создался скрипт:
ls -l /usr/local/bin/awg_watchdog.sh
быстрый просмотр содержимого (без редактирования):
cat /usr/local/bin/awg_watchdog.sh

3. Делаем скрипт исполняемым:
sudo chmod +x /usr/local/bin/awg_watchdog.sh

4. Подготовка логов:
sudo touch /var/log/awg_watchdog.log
sudo chmod 666 /var/log/awg_watchdog.log
проверка что файл лог создался:
ls -l /var/log/awg_watchdog.log

5. Ручной запуск (для отладки перед тем как вешать процесс по крон):
sudo /usr/local/bin/awg_watchdog.sh

6. Настройка автозапуска скрипта:
sudo crontab -e (nano)
добавление скрипта:
*/2 * * * * /usr/local/bin/awg_watchdog.sh > /dev/null 2>&1

Дополнительно:
просмотр лога:
cat /var/log/awg_watchdog.log
просмотр в реальном времени:
tail -f /var/log/awg_watchdog.log
