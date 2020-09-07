Я намеренно делаю максимально тонкий образ с минимумом настроек, чтобы продемонстрировать только процесс сборки и запуска бинаря.

Сборка образа `docker build .` в текущей папке с докерфайлом.

На выходе имеем хеш готового образа в выводе сборки:
Successfully built 735e9b52816b

Проверяем что nginx собран с lua:
❯ docker run -it 735e9b52816b ldd /usr/local/nginx/sbin/nginx
	linux-vdso.so.1 (0x00007ffe4f5fe000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fec32545000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fec32325000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007fec320fc000)
	libluajit-5.1.so.2 => /usr/local/lib/libluajit-5.1.so.2 (0x00007fec31e7d000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fec31afb000)
	libpcre.so.1 => /lib64/libpcre.so.1 (0x00007fec3188a000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fec31673000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fec312b1000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fec32749000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007fec31099000)

Проверяем что nginx рабочий
❯ docker run -it 735e9b52816b /usr/local/nginx/sbin/nginx -t
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful

Далее, готовим дерево конфигов для nginx в папке ./conf, и запускаем с подключением папки conf как тома внутрь контейнера в путь /usr/local/nginx/conf:

❯ docker run -it -v /home/citius/tmp/less4/conf:/usr/local/nginx/conf 735e9b52816b /usr/local/nginx/sbin/nginx -t
nginx: [emerg] getpwnam("nginx") failed in /usr/local/nginx/conf/nginx.conf:1
nginx: configuration file /usr/local/nginx/conf/nginx.conf test failed

Видимо что nginx падает с ошибкой проверки конфига, т.к. в нашем конфиге ему указано работать из под несуществующего в образе пользователя.
Однако задача примаунтить конфиг в контейнер - выполнена.
