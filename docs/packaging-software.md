# Что такое RPM-пакет?

RPM-пакет - это архив, содержащий в себе архив [.cpio](https://en.wikipedia.org/wiki/Cpio) с файлами, а также метаданные - имя пакета, его описание, зависимости и т.д. Менеджер пакетов RPM использует эти метаданные для проверки наличия необходимых пакетов из списка зависимостей, исполнения инструкций по установке файлов и сохранения общей информации о пакете у себя в базе. 

Существует два типа RPM-пакетов:

* SRPM-пакеты (исходники) - архив с расширением `.src.rpm`. SRPM содержит исходный код, при необходимости патчи к нему и spec-файл, в котором описывается, как собрать исходный код в RPM-пакет.
* RPM-пакеты - архив с расширением `.rpm`. RPM содержит исполняемые файлы и библиотеки.

## Подготовка к сборке RPM-пакетов

Пакет ``rpmdevtools``, установленный на этапе [Необходимые пакеты](#prerequisites), предоставляет несколько утилит, упрощающие подготовку к сборке RPM-пакетов. Чтобы перечислить эти утилиты, выполните в консоли следующую команду:

```bash
$ rpm -ql rpmdevtools | grep bin

/usr/bin/rpmdev-bumpspec
/usr/bin/rpmdev-checksig
/usr/bin/rpmdev-cksum
/usr/bin/rpmdev-diff
/usr/bin/rpmdev-extract
/usr/bin/rpmdev-md5
/usr/bin/rpmdev-newinit
/usr/bin/rpmdev-newspec
/usr/bin/rpmdev-packager
/usr/bin/rpmdev-rmdevelrpms
/usr/bin/rpmdev-setuptree
/usr/bin/rpmdev-sha1
/usr/bin/rpmdev-sha224
/usr/bin/rpmdev-sha256
/usr/bin/rpmdev-sha384
/usr/bin/rpmdev-sha512
/usr/bin/rpmdev-sort
/usr/bin/rpmdev-sum
/usr/bin/rpmdev-vercmp
/usr/bin/rpmdev-wipetree
/usr/bin/rpminfo
/usr/bin/rpmls

```

Для получения дополнительной информации о вышеуказанных утилитах см. их страницы руководства или диалоговые окна справки.

## Рабочее пространство для сборки RPM-пакетов
Чтобы создать дерево каталогов, которое является рабочей областью сборки RPM-пакетов, используйте утилиту ``rpmdev-setuptree``, или же создайте каталоги вручную, используя утилиту `mkdir`:

```bash
$ rpmdev-setuptree

$ tree ~/rpmbuild/
/home/user/rpmbuild/
|-- BUILD
|-- RPMS
|-- SOURCES
|-- SPECS
 -- SRPMS

```

Описание созданных каталогов:


| Каталог       | Назначение        |
|:------------- |:----------------- |
| BUILD         | Содержит все файлы, которые появляются при сборке пакета. |
| RPMS          | Здесь формируются собранные RPM-пакеты (`.rpm`) в подкаталогах для разных архитектур, например, в подкаталогах``x86_64`` и ``noarch``. |
| SOURCES       | Здесь находятся архивы исходного кода и патчи. Утилита ``rpmbuild`` ищет их здесь. |
| SPECS         | Здесь хранятся spec-файлы. |
| SRPMS         | Здесь находятся пакеты с исходниками (`.src.rpm`). |

После создания дерева каталогов перейдём в файл `~/home/.rpmmacros`. В нём содержится следующая информация:

* Месторасположение структуры каталогов для сборки;
* Ключ для подписи пакетов;
* Значение поля _Packager_

```
%_topdir<------>%homedir/RPM
#%_tmppath<---->%homedir/tmp

# %packager<--->Joe Hacker <joe@email.address>
# %_gpg_name<-->joe@email.address
```

Раскомментируйте поле _Packager_: Впишите своё имя, фамилию и почту. Поле с ключём для подписи Вы заполните позже. О том, как создавать ключи, Вы узнаете в разделе [Создание SSH и GPG ключей](#JoinKey).

**📌 NOTE**\
Действия, описанные ниже, необходимы для корректного прохождения документации, они обязательны к выполнению! 

Итак, если Вы воспользовались утилитой `rpmdev-setuptree`, а не создали дерево каталогов вручную, обрате внимание на файл `~/home/.rpmmacros`:

```
%_topdir<------>%homedir/RPM
#%_tmppath<---->%homedir/tmp

%packager<--->Joe Hacker <joe@email.address>
# %_gpg_name<-->joe@email.address

%__arch_install_post \
    [ "%{buildarch}" = "noarch" ] || QA_CHECK_RPATHS=1 ; \
    case "${QA_CHECK_RPATHS:-}" in [1yY]*) /usr/lib/rpm/check-rpaths ;; esac \
    /usr/lib/rpm/check-buildroot

```

В файле появилась секция `%__arch_install_post`. Данную секцию необходимо удалить, вернув файл к исходному состоянию, иначе процесс сборки будет завершаться с ошибкой. 

```
%_topdir<------>%homedir/RPM
#%_tmppath<---->%homedir/tmp

%packager<--->Valentin Sokolov <sova@altlinux.org>
# %_gpg_name<-->joe@email.address
```
