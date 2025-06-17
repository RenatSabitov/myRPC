# myRPC
myRPC (Remote Procedure Call) это система удаленного вызова команд, использующая сокеты и JSON для обмена данными. Программа написана на языке C и собирается через makefile. Разработана для использования в дистрибутивах Linux.

### Компоненты системы:
- myRPC-server — сервер демон для обработки клиентских запросов;
- myRPC-client — консольная утилита для отправки команд на сервер;
- libmysyslog — библиотека для логирования действий клиента и сервера.

## Структура проекта
```
myRPC/
├── client/ 			# Исходники и сборка клиентского приложения
│ ├── myRPC-client.c 	# Исходный код клиента
│ └── Makefile 			# Makefile для сборки клиента
├── server/ 			# Исходники и сборка серверного приложения
│ ├── myRPC-server.c 	# Исходный код сервера
│ └── Makefile 			# Makefile для сборки сервера
├── libmysyslog/ 		# Вспомогательная библиотека логирования (аналог syslog)
│ ├── mysyslog.h 		# Заголовочный файл библиотеки
│ ├── mysyslog.c 		# Реализация функций логирования 
│ └── Makefile 			# Makefile для сборки библиотеки
├── myRPC.conf 			# Конфигурационный файл сервера (пример)
├── users.conf 			# Файл со списком разрешённых пользователей (пример)
├── Makefile 			# Общий Makefile для сборки всего проекта и деб-пакета
└── README.md 			# Общий README с описанием проекта
```

## Сборка проекта
Для сборки **всех компонентов** выполните в корне проекта:
```bash
make
```
Или вручную по частям:
```
make -C libmysyslog
make -C client
make -C server
```

## Создание DEB-пакетов
Работа поддерживает сборку .deb-пакетов для установки через пакетный менеджер:
```
make deb
```
**Будут созданы пакеты:**
myrpc-client_<версия>.deb
myrpc-server_<версия>.deb
**Установка**
```
sudo dpkg -i myrpc-client_*.deb
sudo dpkg -i myrpc-server_*.deb
```

После установки:
myRPC-client и myRPC-server будут доступны в /usr/bin

Логи пишутся в /var/log/myrpc-client.log и /var/log/myrpc-server.log

### Конфигурация
Создайте папку и конфигурационные файлы:
```
sudo mkdir -p /etc/myRPC
```

/etc/myRPC/myRPC.conf

## Порт для соединения
port = 8642

## Тип сокета: stream или dgram
socket_type = stream

/etc/myRPC/users.conf

Список разрешённых пользователей (по одному в строке):

root

### Пример использования
Запуск сервера:
```
sudo myRPC-server
```
Запуск клиента:
```
myRPC-client --host 127.0.0.1 --port 8642 --stream --command "ls -l /tmp"
```
Также можно использовать UDP:
```
myRPC-client --host 127.0.0.1 --port 8642 --dgram --command "whoami"
```

### Протокол (JSON)
Запрос клиента:
```
{
  "login": "имя_пользователя",
  "command": "bash-команда"
}
```
Ответ сервера:
```
{
  "code": 0,            // 0 — успех, 1 — ошибка
  "result": "Результат"
}
```
### Логирование
Все действия записываются в лог-файлы через libmysyslog:

/var/log/myrpc-client.log

/var/log/myrpc-server.log

## Требования

libjson-c (установить: sudo apt install libjson-c-dev)

Права на запуск и чтение конфигов (sudo)

GCC, make, dpkg-dev


## Безопасность

Проверка пользователя через файл users.conf

Выполнение команд через /bin/sh -c

Вывод stdout и stderr сохраняется во временные файлы (/tmp/myRPC_XXXXXX.*)


## Аргументы клиента

Аргумент + Назначение

**--host** IP-адрес сервера


**--port** Порт сервера


**--command** Команда bash для выполнения


**--stream** Использовать TCP


**--dgram** Использовать UDP


**--help** Показать справку



## Очистка
```
make clean
```
Удалит все .o, бинарники, временные и сборочные файлы.

## Дополнительные модули
Каждый компонент проекта сопровождается своим README.md:

client/README.md
server/README.md
libmysyslog/README.md








