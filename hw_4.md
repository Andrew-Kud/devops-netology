Оркестрация группой Docker контейнеров на примере Docker Compose



---
Задача 1:
Сценарий выполнения задачи:

    Установите docker и docker compose plugin на свою linux рабочую станцию или ВМ.

    Если dockerhub недоступен создайте файл /etc/docker/daemon.json с содержимым: {"registry-mirrors": ["https://mirror.gcr.io", "https://daocloud.io", "https://c.163.com/", "https://registry.docker-cn.com"]}

    Зарегистрируйтесь и создайте публичный репозиторий с именем "custom-nginx" на https://hub.docker.com (ТОЛЬКО ЕСЛИ У ВАС ЕСТЬ ДОСТУП);

    скачайте образ nginx:1.29.0;

    Создайте Dockerfile и реализуйте в нем замену дефолтной индекс-страницы(/usr/share/nginx/html/index.html), на файл index.html с содержимым:

```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I will be DevOps Engineer!</h1>
</body>
</html>
```

    Соберите и отправьте созданный образ в свой dockerhub-репозитории c tag 1.0.0 (ТОЛЬКО ЕСЛИ ЕСТЬ ДОСТУП).

    Предоставьте ответ в виде ссылки на https://hub.docker.com/<username_repo>/custom-nginx/general .



1.
```
sudo apt update && sudo apt upgrade -y
```

2.
```
sudo apt install ca-certificates curl
```

3.
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg
```
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
```
sudo apt update && sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

4.
```
sudo systemctl enable docker --now
```

5.
```
sudo usermod -aG docker $USER
newgrp docker
```

6.
```
docker --version
docker compose version
```

7.
```
mkdir -p ~/docker-nginx && cd ~/docker-nginx
```

8.
```
cat > index.html << 'EOF'
<html>
<head>
<title>Hey, Netology</title>
</head>
<body>
<h1>I will be DevOps Engineer!</h1>
</body>
</html>
EOF
```

9.
```
cat > Dockerfile << 'EOF'
FROM nginx:1.29.0
COPY index.html /usr/share/nginx/html/index.html
EOF
```

10.
```
docker pull nginx:1.29.0
```

11.
```
export USERNAME=ПОЛЬЗОВАТЕЛЬ_с_hub.docker.com
docker build -t $USERNAME/custom-nginx:1.0.0 .
```
```
docker images | grep custom-nginx
```
```
docker history koddrew/custom-nginx:1.0.0
```

12.
```
docker run -d --name test-nginx -p 8080:80 $USERNAME/custom-nginx:1.0.0
```
```
docker ps
```
```
curl localhost:8080
```
Если всё Ок:
```
docker rm -f test-nginx
```

13.
```
docker login
```
```
docker push $USERNAME/custom-nginx:1.0.0
```

тест:
```
docker pull koddrew/custom-nginx:1.0.0
```
```
docker run -d --name test-nginx2 -p 8081:80 koddrew/custom-nginx:1.0.0
```
```
curl http://localhost:8081
```

Answer:
https://hub.docker.com/r/koddrew/custom-nginx


---
Задача 2:
    Запустите ваш образ custom-nginx:1.0.0 командой docker run в соответвии с требованиями:

    имя контейнера "ФИО-custom-nginx-t2"
    контейнер работает в фоне
    контейнер опубликован на порту хост системы 127.0.0.1:8080

    Не удаляя, переименуйте контейнер в "custom-nginx-t2"
    Выполните команду date +"%d-%m-%Y %T.%N %Z" ; sleep 0.150 ; docker ps ; ss -tlpn | grep 127.0.0.1:8080  ; docker logs custom-nginx-t2 -n1 ; docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html
    Убедитесь с помощью curl или веб браузера, что индекс-страница доступна.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.



1.
```
cd ~/docker-nginx
```

2.
```
docker run -d --name AndreyKudryashov-custom-nginx-t2 -p 127.0.0.1:8080:80 koddrew/custom-nginx:1.0.0
```
```
docker ps
```

3.
```
docker rename AndreyKudryashov-custom-nginx-t2 custom-nginx-t2
```
```
docker ps
```

4.
```
date +"%d-%m-%Y %T.%N %Z" ; sleep 0.150 ; docker ps ; ss -tlpn | grep 127.0.0.1:8080  ; docker logs custom-nginx-t2 -n1 ; docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html
```

5.
```
curl http://127.0.0.1:8080
```



---
Задача 3:
    Воспользуйтесь docker help или google, чтобы узнать как подключиться к стандартному потоку ввода/вывода/ошибок контейнера "custom-nginx-t2".

    Подключитесь к контейнеру и нажмите комбинацию Ctrl-C.

    Выполните docker ps -a и объясните своими словами почему контейнер остановился.

    Перезапустите контейнер

    Зайдите в интерактивный терминал контейнера "custom-nginx-t2" с оболочкой bash.

    Установите любимый текстовый редактор(vim, nano итд) с помощью apt-get.

    Отредактируйте файл "/etc/nginx/conf.d/default.conf", заменив порт "listen 80" на "listen 81".

    Запомните(!) и выполните команду nginx -s reload, а затем внутри контейнера curl http://127.0.0.1:80 ; curl http://127.0.0.1:81.

    Выйдите из контейнера, набрав в консоли exit или Ctrl-D.

    Проверьте вывод команд: ss -tlpn | grep 127.0.0.1:8080 , docker port custom-nginx-t2, curl http://127.0.0.1:8080. Кратко объясните суть возникшей проблемы.

        Это дополнительное, необязательное задание. Попробуйте самостоятельно исправить конфигурацию контейнера, используя доступные источники в интернете. Не изменяйте конфигурацию nginx и не удаляйте контейнер. Останавливать контейнер можно. пример источника

    Удалите запущенный контейнер "custom-nginx-t2", не останавливая его.(воспользуйтесь --help или google)

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.



1.
```
docker attach custom-nginx-t2
```
`После чего нажми Ctrl + c`

2.
```
docker ps -a
```
Причина в docker attach, он подключился к главному процессу контенера, т.е nginx. Ctrl+C послал сигнал завершения, nginx завершился, а когда главный процесс контейнера заканчивается, то конетйнер считаестя остановленным.

3.
```
docker start custom-nginx-t2
```
```
docker ps
```

4.
```
docker exec -it custom-nginx-t2 bash
```

5.
```
apt update && apt install nano
```

6.
```
nano /etc/nginx/conf.d/default.conf
```
```
nginx -s reload
```
```
curl http://127.0.0.1:80
```
```
curl http://127.0.0.1:81
```
```
exit
```

7.
```
ss -tlpn | grep 127.0.0.1:8080
```
```
docker port custom-nginx-t2
```
```
curl http://127.0.0.1:8080
```
Докер пробрасывает 80 порт контейнера на 8080 хоста, но внутри контейнера nginx слушает не 80, а теперь 81 порт. Контейнер жив, но curl выдаёт пустой ответ. Т.е 8080 ни к чему не привязан внутри контейнера.

8.
```
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' custom-nginx-t2
```
```
sudo iptables -t nat -A OUTPUT -p tcp -d 127.0.0.1 --dport 8081 -j DNAT --to-destination 172.17.0.2:81
```
```
sudo iptables -t nat -A POSTROUTING -p tcp -d 172.17.0.2 --dport 81 -j MASQUERADE
```
`Я попытался перенаправить порт без изменения конфигурации nginx и без удаления контейнера, добавив правила iptables на хосте (dnat/redirect/masquarade 8080/8081 на ip контейнера и порт81). Выяснил, что docker сам управляет цепочками iptables и его правила обрабатываются раньше моих, поэтому переназначить порт надёжно и предсказуемо не удалось. В документации и на сайте (https://www.baeldung.com/ops/assign-port-docker-container) сказано: официально изменить port-mapping у существующего контейнера нельзя - коректное решение в реальной среде заключается в пересоздании контейнера с правильными параметрами -p.`

В статье только 3 варианта решения задачи:
1 - Пересоздать с нужным портом.
```
docker run -d -p 8080:81 --name httpd-container httpd
```

2 - docker commit + новый контейнер.
```
docker stop httpd-container
docker commit httpd-container httpd-image
docker rm httpd-container
docker run -d -p 8080:81 --name httpd-container httpd-image
```

3 - Правка конфигов Docker (hostconfig.json / config.v2.json).
```
docker stop httpd-container
systemctl stop docker
```
В /var/lib/docker/containers/<ID>/hostconfig.json руками прописать PortBindings.
В config.v2.json прописать ExposedPorts.
```
systemctl start docker
docker start httpd-container
```
Сами авторы считают этот вариант рискованным, но он сохраняет тот же container ID.


По моему скромному мнению, 2 и 3 варинты - откровенные костыли, которых можно и не ставить, воспользовавшись 1 вариантом.


9.
```
docker rm -f custom-nginx-t2
```
```
docker ps -a
```



---
Задача 4:
    Запустите первый контейнер из образа centos c любым тегом в фоновом режиме, подключив папку текущий рабочий каталог $(pwd) на хостовой машине в /data контейнера, используя ключ -v.

    Запустите второй контейнер из образа debian в фоновом режиме, подключив текущий рабочий каталог $(pwd) в /data контейнера.
    Подключитесь к первому контейнеру с помощью docker exec и создайте текстовый файл любого содержания в /data.

    Добавьте ещё один файл в текущий каталог $(pwd) на хостовой машине.

    Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /data контейнера.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.



1.
```
pwd
```
```
ls -la
```

2.
```
docker run -d --name centos-data -v "$(pwd)":/data centos:7 sleep infinity
```
```
docker ps -a
```

3.
```
docker run -d --name debian-data -v "$(pwd)":/data debian:12-slim sleep infinity
```
```
docker ps -a
```

4.
```
docker exec -it centos-data bash
```
```
cd /data
```
```
echo "test-file for centos container" > centos.txt
```
```
ls
cat centos.txt
```
```
exit
```
```
ls -la
```
```
cat centos.txt
```

5.
```
echo "file from host" > host.txt
```
```
ls
cat host.txt
```

6.
```
docker exec -it debian-data bash
```
```
cd /data
```
```
ls
cat centos.txt
cat host.txt
```
```
exit
```


---
Задача 5:
Создайте отдельную директорию(например /tmp/netology/docker/task5) и 2 файла внутри него. "compose.yaml" с содержимым:
```
version: "3"
services:
  portainer:
    network_mode: host
    image: portainer/portainer-ce:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

"docker-compose.yaml" с содержимым:
```
version: "3"
services:
  registry:
    image: registry:2

    ports:
    - "5000:5000"
```

И выполните команду "docker compose up -d".
Какой из файлов был запущен и почему? (подсказка: https://docs.docker.com/compose/compose-application-model/#the-compose-file )

    Отредактируйте файл compose.yaml так, чтобы были запущенны оба файла. (подсказка: https://docs.docker.com/compose/compose-file/14-include/)

    Выполните в консоли вашей хостовой ОС необходимые команды чтобы залить образ custom-nginx как custom-nginx:latest в запущенное вами, локальное registry. Дополнительная документация: https://distribution.github.io/distribution/about/deploying/

    Откройте страницу "https://127.0.0.1:9000" и произведите начальную настройку portainer.(логин и пароль адмнистратора)

    Откройте страницу "http://127.0.0.1:9000/#!/home", выберите ваше local окружение. Перейдите на вкладку "stacks" и в "web editor" задеплойте следующий компоуз:
```
version: '3'

services:
  nginx:
    image: 127.0.0.1:5000/custom-nginx
    ports:
      - "9090:80"
```
    ерейдите на страницу "http://127.0.0.1:9000/#!/2/docker/containers", выберите контейнер с nginx и нажмите на кнопку "inspect". В представлении <> Tree разверните поле "Config" и сделайте скриншот от поля "AppArmorProfile" до "Driver".

    Удалите любой из манифестов компоуза(например compose.yaml). Выполните команду "docker compose up -d". Прочитайте warning, объясните суть предупреждения и выполните предложенное действие. Погасите compose-проект ОДНОЙ(обязательно!!) командой.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод, файл compose.yaml , скриншот portainer c задеплоенным компоузом.


1.
```
mkdir -p /tmp/netology/docker/task5
cd /tmp/netology/docker/task5
```

2.
```
cat > compose.yaml << 'EOF'
version: "3"
services:
  portainer:
    network_mode: host
    image: portainer/portainer-ce:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
EOF
```
```
cat > docker-compose.yaml << 'EOF'
version: "3"
services:
  registry:
    image: registry:2
    ports:
      - "5000:5000"
EOF
```

3.
```
docker compose up -d
```
Запустился compose.yaml, а docker-compose.yaml был проигнорирован. Причина в приоритете имён выбора. compose имеет выше приоритет чем docker-compose. А в выоде написано что именно он выбрал:
 - WARN[0000] Found multiple config files with supported names: /tmp/netology/docker/task5/compose.yaml, /tmp/netology/docker/task5/docker-compose.yaml
 - WARN[0000] Using /tmp/netology/docker/task5/compose.yaml

Получается, что запустился только сервис portainer из compose.yaml, а сервис registry из docker-compose.yaml нет.

Полагаю, что compose.yml - это новый и приоритетный формат, который ищется в первую очередь, а docker-compose.yml - устаревший формат, который ищется по остаточному принципу.

4.
```
docker ps
docker compose ps
```

5.
```
cat > compose.yaml << 'EOF'
version: "3"
include:
  - docker-compose.yaml
services:
  portainer:
    network_mode: host
    image: portainer/portainer-ce:latest
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
EOF
```
```
docker compose down
```
```
docker compose up -d
```
```
docker ps
```


6.
```
docker rm -f custom-nginx-t2 2>/dev/null
```
```
docker tag koddrew/custom-nginx:1.0.0 127.0.0.1:5000/custom-nginx:latest
```
```
docker push 127.0.0.1:5000/custom-nginx:latest
```
```
curl http://127.0.0.1:5000/v2/_catalog
```
```
http://192.168.10.210:9000
```

7.
```
rm compose.yaml
docker compose up -d
```
WARN[0000] Found orphan containers ([task5-portainer-1]) for this project. If you removed or renamed this service in your compose file, you can run this command with the --remove-orphans flag to clean it up.

compose.yaml был удалён, этот осиротевший контейнер был создан из этого yaml. теперь остался только docker-compose.yaml и этот осиротевший контейнер в нём не описан, т.е не упрвляется текущей конфигурацией. Композер предлагает удалить его.

```
docker compose up -d --remove-orphans
```

8.
```
docker compose down
```
```
docker ps
```