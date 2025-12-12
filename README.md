1.Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.

```
git show aefea
```

<img width="1413" height="365" alt="1" src="https://github.com/user-attachments/assets/4e9aa9c8-17cc-44ce-b077-52cc8a358292" />

---

2.Какому тегу соответствует коммит 85024d3?

```
git describe --tags 85024d3
```
или
```
git log --all --oneline | grep '85024d3'
```

v0.12.23

---

3.Сколько родителей у коммита b8d720? Напишите их хеши.

```
git rev-parse b8d720^@
```
или
```
git show -s --pretty=%P b8d720
```

56cd7859e05c36c06b56d013b55a252d0bb7e158
9ea88f22fc6269854151c571162c5bcf958bee2b

---

4.Перечислите хеши и комментарии всех коммитов, которые были сделаны между тегами v0.12.23 и v0.12.24.

```
git log --oneline v0.12.23..v0.12.24
```

33ff1c03bb (tag: v0.12.24) v0.12.24
b14b74c493 [Website] vmc provider links
3f235065b9 Update CHANGELOG.md
6ae64e247b registry: Fix panic when server is unreachable
5c619ca1ba website: Remove links to the getting started guide's old location
06275647e2 Update CHANGELOG.md
d5f9411f51 command: Fix bug when using terraform login on Windows
4b6d06cc5d Update CHANGELOG.md
dd01a35078 Update CHANGELOG.md
225466bc3e Cleanup after v0.12.23 release

---

5.Найдите коммит, в котором была создана функция func providerSource, её определение в коде выглядит так: func providerSource(...) (вместо троеточия перечислены аргументы).

```
git log -S"func providerSource" --oneline
```

Получаю:
5af1e6234a main: Honor explicit provider_installation CLI config when present
8c928e8358 main: Consult local directories as potential mirrors of providers

Далее:
```
git show 8c928e8358
```
```
git show 5af1e6234a
```

8c928e8358 - коммит от 2 апреля 2020 года, а значит именно тут впервые появилась эта функция.

Можно так же сделать:
```
git show 8c928e8358 | grep -C 5 "func providerSource"
```
```
git show 5af1e6234a | grep -C 5 "func providerSource"
```

<img width="1352" height="736" alt="2" src="https://github.com/user-attachments/assets/305c57d4-8dff-41d6-ba58-4a31f50e7339" />

---

6.Найдите все коммиты, в которых была изменена функция globalPluginDirs.

```
git log -S"func globalPluginDirs" --oneline
```
7c4aeac5f3 stacks: load credentials from config file on startup (#35952)
8364383c35 Push plugin discovery down into command package

Судя по дате:
```
git show -s --format=%ci 8364383c35
```
2017-06-09 14:03:59 -0700
```
git show -s --format=%ci 7c4aeac5f3
```
2024-11-05 16:13:08 +0100

А так же по:
```
git show 8364383c35 | grep -C 5 "globalPluginDirs"
```
```
git show 7c4aeac5f3 | grep -C 5 "globalPluginDirs"
```

То функция globalPluginDirs была впервые добавлена в 8364383c35, а переименована в 7c4aeac5f3.

---

7.Кто автор функции synchronizedWriters?
```
git log -S"func synchronizedWriters" --oneline
```

bdfea50cc8 remove unused
5ac311e2a9 main: synchronize writes to VT100-faker on Windows

```
git show 5ac311e2a9
```

Author: Martin Atkins <mart@degeneration.co.uk>

---
