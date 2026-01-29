Оркестрация кластером Docker контейнеров на примере Docker Swarm

### Задача 1

Создайте ваш первый Docker Swarm-кластер в Яндекс Облаке. Документация swarm: https://docs.docker.com/engine/reference/commandline/swarm_init/
    Создайте 3 облачные виртуальные машины в одной сети.
    Установите docker на каждую ВМ.
    Создайте swarm-кластер из 1 мастера и 2-х рабочих нод.
    Проверьте список нод командой:
```
docker node ls
```

### Решение:

1.
Создай ВМ (3 шт, 1 мастер и 2 воркера, это минимальная конфигурация для высокой доступности).


2.
Установи Докер:
```
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
newgrp docker
```


3.
Все ВМ-Воркеры должны видеть ВМ-Мастера по сети.


4.
Проверка, что всё установилось нормально:
- docker:
```
docker --version
```
```
sudo systemctl status docker
```
```
docker info
```

- docker swarm:
```
docker node ls
```
```
docker info | grep Swarm
```

- docker compose:
```
docker compose version
```

---

5.
Узнай локальный ip своих будущих ВМ-Мастера и ВМ-Воркеров.
так:
```
yc compute instance list
```
получишь:
+----------------------+------+---------------+---------+----------------+---------------+
|          ID          | NAME |    ZONE ID    | STATUS  |  EXTERNAL IP   |  INTERNAL IP  |
+----------------------+------+---------------+---------+----------------+---------------+
| epd258qkj3417n1fb2oh | vm-0 | ru-central1-b | RUNNING | 158.160.69.145 | 192.168.10.8  |
| epdd7k25fmvp3de4t2gt | vm-2 | ru-central1-b | RUNNING | 158.160.83.238 | 192.168.10.10 |
| epdn5ggq8t8m75c2e03b | vm-1 | ru-central1-b | RUNNING | 158.160.82.43  | 192.168.10.32 |
+----------------------+------+---------------+---------+----------------+---------------+
`тебя интересует INTERNAL IP`

- или через ВЕБ интерфейс Yandex.
- или через `ip a` на самих ВМ.


6.
Инициализация ВМ-мастера:
```
docker swarm init --advertise-addr 192.168.10.8 --listen-addr 0.0.0.0:2377
```


7.
Инициализация ВМ-вокреров:

После того, как ты ввёл команду из п.2, ты получишь ответ в терминал:
```
docker swarm init --advertise-addr 192.168.10.8 --listen-addr 0.0.0.0:2377

Swarm initialized: current node (e1oozy4d4ybmd4dskb887iypt) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3jf535krvbpffic70q1f8jv1alim4hg7o58saqo82ua2njh5r5-215vu0y3pezqmd3sepu9kawsb 192.168.10.8:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

тебе нужно взять вот эту команду:
```
docker swarm join --token SWMTKN-1-3jf535krvbpffic70q1f8jv1alim4hg7o58saqo82ua2njh5r5-215vu0y3pezqmd3sepu9kawsb 192.168.10.8:2377
```
и ввести её на всех своих ВМ-Воркерах.


8.
Проверка:
```
docker node ls
```

ты получишь примерно такой же ответ:
```
docker node ls
ID                            HOSTNAME               STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
e1oozy4d4ybmd4dskb887iypt *   epd258qkj3417n1fb2oh   Ready     Active         Leader           29.2.0
9lbvogx9lwrzm8v5zwtfdf539     epdd7k25fmvp3de4t2gt   Ready     Active                          29.2.0
r3n36r1knv7dm9pqy7mbpl5n7     epdn5ggq8t8m75c2e03b   Ready     Active                          29.2.0
```

---

### Задача 2:
    Задеплойте ваш python-fork из предыдущего ДЗ(05-virt-04-docker-in-practice) в получившийся кластер.
    Удалите стенд.

.
На Мастер-ВМ скопируй свой проект по `scp` через `git clone` или `rsync`

- git
```
git clone https://github.com/Andrew-Kud/shvirtd-example-python.git
```


- scp (c rsa ключом) - Имей ввиду, если есть симлинки, то попадёшь в рекурсию!
```
scp -r -i ~/.ssh/id_rsa "/home/adm1/Загрузки/devops-netology3/shvirtd-example-python" yc-user@158.160.69.145:/home/yc-user/
```

или вот с login - password вводом:
```
scp -r "/home/adm1/Загрузки/devops-netology3/shvirtd-example-python" yc-user@158.160.69.145:/home/yc-user/
```


- rsync
```
rsync -avz --progress "/home/adm1/Загрузки/devops-netology3/shvirtd-example-python/" yc-user@158.160.69.145:/home/yc-user/shvirtd-example-python
```



2.
Запуск приложения:

```
cd ~/shvirtd-example-python
```

```
docker stack deploy -c compose.yaml shvirtd
```


---

имей ввиду, что для swarm синтаксис чуть отличается и просто скопировать из compose не получится, нужно править.

Просто для примера, вот `compose.yaml`:
```
include:

- path: proxy.yaml



services:

web:

build:

context: .

dockerfile: Dockerfile.python

networks:

backend:

ipv4_address: 172.20.0.5

restart: always

environment:

DB_HOST: db

DB_USER: app

DB_PASSWORD: ${MYSQL_PASSWORD}

DB_NAME: ${MYSQL_DATABASE}

DB_TABLE: requests_test

depends_on:

db:

condition: service_healthy



db:

image: mysql:8

networks:

backend:

ipv4_address: 172.20.0.10

restart: always

environment:

MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}

MYSQL_DATABASE: ${MYSQL_DATABASE}

MYSQL_USER: app

MYSQL_PASSWORD: ${MYSQL_PASSWORD}

volumes:

- mysql_data:/var/lib/mysql

healthcheck:

test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]

timeout: 20s

retries: 10




volumes:

mysql_data:
```

а вот `compose-swarm.yaml`':
```
version: '3.8'
services:
  web:
    image: shvirtd-web:latest
    networks:
      - backend
    environment:
      DB_HOST: db
      DB_USER: app
      DB_PASSWORD: QwErTy1234
      DB_NAME: virtd
      DB_TABLE: requests_test
    deploy:
      replicas: 2
    depends_on:
      - db

  db:
    image: mysql:8
    networks:
      - backend
    environment:
      MYSQL_ROOT_PASSWORD: YtReWq4321
      MYSQL_DATABASE: virtd
      MYSQL_USER: app
      MYSQL_PASSWORD: QwErTy1234
    volumes:
      - mysql_data:/var/lib/mysql

networks:
  backend:
    driver: overlay

volumes:
  mysql_data:

```

далее сам деплой:
```
docker stack deploy -c compose-swarm.yaml shvirtd
```

---

если ошибка с образами или ты накосячил с билдом ранее, то удали старые image:
```
docker rmi shvirtd-example-python-web 2>/dev/null || true
```

собери новый image:
```
docker build -f Dockerfile.python -t shvirtd-web .
```

и уже потом депрой:
```
docker stack deploy -c compose-swarm.yaml shvirtd
```

3.
Удаление

```
yc compute instance list
```


Одной ВМ:
```
yc compute instance delete vm-0
```


Последовательно по имени:
```
yc compute instance delete vm-0 vm-1 vm-2
```


Асинхронное удаление:
```
yc compute instance delete vm-0 vm-1 vm-2 --async
```


---
---
