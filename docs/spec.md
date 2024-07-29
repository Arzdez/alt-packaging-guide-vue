### Что такое SPEC-файл?

Spec-файл можно рассматривать как "инструкцию", который утилита ``rpmbuild`` использует для фактической сборки RPM-пакета. Он сообщает системе сборки, что делать, определяя инструкции в серии разделов. Разделы определены в __Преамбуле__ и в __Основной части__. __Преамбула__ содержит ряд элементов метаданных, которые используются в __Основной части__. Тело содержит основную часть инструкций.

### Пример .spec-файла

Данный пример взять из [ALT Linux Wiki](https://www.altlinux.org/SampleSpecs/program).

```bash
Name: sampleprog
Version: 1.0
Release: alt1

Summary: Sample program specfile
Summary(ru_RU.UTF-8): Пример спек-файла для программы

License: GPLv2+
Group: Development/Other
Url: http://www.altlinux.org/SampleSpecs/program

Packager: Sample Packager <sample@altlinux.org>

Source: %name-%version.tar
Patch0: %name-1.0-alt-makefile-fixes.patch

%description
This specfile is provided as sample specfile for packages with programs.
It contains most of usual tags and constructions used in such specfiles.

%description -l ru_RU.UTF-8
Этот спек-файл является примером спек-файла для пакета с программой. Он содержит
основные теги и конструкции, используемые в подобных спек-файлах.

%prep
%setup
%patch0 -p1

%build
%configure
%make_build

%install
%makeinstall_std
%find_lang %name

%files -f %name.lang
%doc AUTHORS ChangeLog NEWS README THANKS TODO contrib/ manual/
%_bindir/*
%_man1dir/*

%changelog
* Sat Sep 33 3001 Sample Packager <sample@altlinux.org> 1.0-alt1
- initial build

```

#### Пункты преамбулы

В этой таблице перечислены элементы, используемые в разделе преамбулы файла спецификации RPM:
| SPEC Директива    | Определение       |
|:-------------     |:----------------- |
| ``Name``          | Базовое имя пакета, которое должно совпадать с именем spec-файла.
| ``Version``       | Версия upstream-кода. |
| ``Release``       | Релиз пакета используется для указания номера сборки пакета при данной версии upstream-кода. Как правило, установите начальное `alt1`  и увеличивайте его с каждым новым выпуском пакета, например: alt1, alt2, alt3 и т.д. Сбросьте значение до alt1 при создании новой версии программного обеспечения. |
| ``Summary``       | Краткое, в одну строку, описание пакета. |
| ``License``       | Лицензия на собираемое программное обеспечение. |
| ``Group``         | Используется для указания категории, к которой относится пакет. Указанная группа должна находиться в списке групп, известном RPM. Этот список располагается в файле /usr/lib/rpm/GROUPS, идущим вместе с пакетом rpm. |
| ``URL``           | Полный URL-адрес для получения дополнительной информации о программе. Чаще всего это ссылка на **GitHub** upstream-проекта для собираемого программного обеспечения. |
| ``Source0``       | Путь или URL-адрес к сжатому архиву исходного кода (не исправленный, исправления обрабатываются в другом месте). Этот раздел должен указывать на доступное и надежное хранилище архива, например, на upstream-страницу, а не на локальное хранилище сборщика. При необходимости можно добавить дополнительные исходные директивы, каждый раз увеличивая их количество, например: Source1, Source2, Source3 и так далее. |
| ``Patch0``        | Название первого патча, который при необходимости будет применен к исходному коду. При необходимости можно добавить дополнительные директивы PatchX, увеличивая их количество каждый раз, например: Patch1, Patch2, Patch3 и так далее. |
| ``BuildArch``     | Если пакет не зависит от архитектуры, например, если он полностью написан на интерпретируемом языке программирования, установите для этого значение ``BuildArch: noarch``. Если этот параметр не задан, пакет автоматически наследует архитектуру компьютера, на котором он собран, например ``x86_64``. |
| ``BuildRequires`` | Разделённый запятыми или пробелами список пакетов, необходимых для сборки программы. Может быть несколько записей ``BuildRequires``, каждая в отдельной строке в SPEC файле. |
| ``Requires``      | Разделённый запятыми или пробелами список пакетов, необходимых программному обеспечению для запуска после установки. Это его **зависимости** Может быть несколько записей ``Requires``, каждая в отдельной строке в SPEC файле. |
| ``ExcludeArch``   | Если часть программного обеспечения не может работать на определенной архитектуре процессора, Вы можете исключить эту архитектуру здесь. |

Директивы ``Name``, ``Version`` и ``Release`` содержат имя RPM-пакета. Эти три директивы часто называют ***N-V-R*** или ***NVR***, поскольку имена RPM-пакета имеют формат
``NAME-VERSION-RELEASE``.

Вы можете получить пример ``NAME-VERSION-RELEASE``, выполнив запрос с использованием ``rpm`` для конкретного пакета:

```bash
$ rpm -q rpmdevtools
rpmdevtools-8.10-alt2.noarch

```

Здесь ``rpmdevtools`` - это имя пакета, ``8.10`` - версия, а ``alt2`` - релиз. Последний маркер ``noarch`` - сведения об архитектуре.
В отличие от NVR, маркер архитектуры не находится под прямым управлением сборщика, а определяется средой сборки ``rpmbuild``. Исключением из этого правила является архитектурно-независимый пакет ``noarch``.

#### Составляющие основной части

В этой таблице перечислены элементы, используемые в теле файла спецификации RPM-пакета:

| SPEC Директива    | Определение       |
|:-------------     |:----------------- |
| ``%description`` | Полное описание программного обеспечения, входящего в комплект поставки RPM. Это описание может занимать несколько строк и может быть разбито на абзацы. |
| ``%prep``        | Команда или серия команд для подготовки программного обеспечения к сборке, например, распаковка архива из Source0. Эта директива может содержать сценарий оболочки (shell скрипт). |
| ``%build``       | Команда или серия команд для фактической сборки программного обеспечения в машинный код (для скомпилированных языков) или байт-код (для некоторых интерпретируемых языков). |
| ``%install``     | Раздел, который во время сборки пакета эмулирует конечные пути установки файлов в систему. Команда или серия команд для копирования требуемых артефактов сборки из ``%builddir`` (где происходит сборка) в``%buildroot`` каталог (который содержит структуру каталогов с файлами, подлежащими сборке). Обычно это означает копирование файлов из ``~/rpmbuild/BUILD`` в ``~/rpmbuild/BUILDROOT`` и создание необходимых каталогов ``~/rpmbuild/BUILDROOT``.  Это выполняется только при создании пакета, а не при установке пакета конечным пользователем. Подробности см. в разделе [Работа со SPEC файлом](#working-with-spec-files). |
| ``%check``       | Команда или серия команд для тестирования программного обеспечения. Обычно включает в себя такие вещи, как модульные тесты. |
| ``%files``       | Список файлов, которые будут установлены в системе конечного пользователя.
| ``%changelog``   | Запись изменений, произошедших с пакетом между различными ``Version`` или ``Release`` сборками. |

**📌 NOTE**\
Конструкция `%setup` в Sisyphus RPM использует флаг `-q` (quiet) по умолчанию. Запись %setup -q и %setup - полностью идентичны. Если использовать конструкцию с флагом `-v`, то будет выведена дополнительная информация в логах сборки  

## Проверка RPMs на корректность

После создания упаковки хорошо бы проверить её качество. Качество пакета, а не программного обеспечения, поставляемого в нём. Основным инструментом для этого является
[rpmlint](https://github.com/rpm-software-management/rpmlint). Это улучшает редактируемость RPM и обеспечивает проверку работоспособности и ошибок путем выполнения статического анализа  RPM. Эта утилита может проверять бинарные RPM, исходные RPM (SRPMs) и spec файлы, поэтому она полезна на всех этапах упаковки, как показано в следующих примерах.

Обратите внимание, что ``rpmlint`` имеет очень строгие правила, и иногда допустимо и необходимо пропустить некоторые из его ошибок и предупреждений, как показано в следующих примерах.

**📌 NOTE**\
В примерах мы запускаем ``rpmlint`` без каких-либо опций, что приводит к невербальному выводу. Для получения подробных объяснений каждой ошибки или предупреждения вместо этого запустите 
``rpmlint -i`` instead.

### Проверка SPEC файла bello

Это результат выполнения  ``rpmlint`` в SPEC файле ``bello``:

```bash
$ rpmlint bello.spec
bello.spec: W: invalid-url Source0: https://www.example.com/bello/releases/bello-0.1.tar.gz HTTP Error 404: Not Found
0 packages and 1 specfiles checked; 0 errors, 1 warnings.

```

Наблюдения:

* Для ``bello.spec`` есть только одно предупреждение. В нем говорится, что URL-адрес, указанный в директиве
``Source0`` недоступен. Это ожидаемо, поскольку указанный ``example.com`` URL-адрес не существует. Предполагая, что мы ожидаем, что этот URL-адрес будет работать в будущем, мы можем проигнорировать это предупреждение

Это результат выполнения ``rpmlint`` на SRPM для ``bello``:

```bash
$ rpmlint ~/rpmbuild/SRPMS/bello-0.1-1.el7.src.rpm
bello.src: W: invalid-url URL: https://www.example.com/bello HTTP Error 404: Not Found
bello.src: W: invalid-url Source0: https://www.example.com/bello/releases/bello-0.1.tar.gz HTTP Error 404: Not Found
1 packages and 0 specfiles checked; 0 errors, 2 warnings.

```

Наблюдения:

* Для ``bello`` SRPM появилось новое предупреждение, в котором говорится, что URL-адрес, указанный в директиве URL, недоступен. Предполагая, что ссылка будет работать в будущем, мы можем проигнорировать это предупреждение.

### Проверка бинарного RPM bello

При проверке бинарных RPMs, ``rpmlint`` проверяет дополнительные параметры, в том числе:

1. документацию

Это результат выполнения``rpmlint`` на бинарном RPM для ``bello``:

```bash
$ rpmlint ~/rpmbuild/RPMS/noarch/bello-0.1-1.el7.noarch.rpm
bello.noarch: W: invalid-url URL: https://www.example.com/bello HTTP Error 404: Not Found
bello.noarch: W: no-documentation
bello.noarch: W: no-manual-page-for-binary bello
1 packages and 0 specfiles checked; 0 errors, 3 warnings.

```

Наблюдения:

* ``no-documentation`` и``no-manual-page-for-binary`` оворят о том, что в RPM нет документации или страниц руководства, потому что мы их не предоставили.

Помимо вышеприведенных предупреждений, наш RPM проходит проверку ``rpmlint``.

### Проверка SPEC файла pello 

Это результат выполнения ``rpmlint`` на SPEC файле ``pello``:

```bash
$ rpmlint pello.spec
pello.spec:30: E: hardcoded-library-path in %{buildroot}/usr/lib/%{name}
pello.spec:34: E: hardcoded-library-path in /usr/lib/%{name}/%{name}.pyc
pello.spec:39: E: hardcoded-library-path in %{buildroot}/usr/lib/%{name}/
pello.spec:43: E: hardcoded-library-path in /usr/lib/%{name}/
pello.spec:45: E: hardcoded-library-path in /usr/lib/%{name}/%{name}.py*
pello.spec: W: invalid-url Source0: https://www.example.com/pello/releases/pello-0.1.1.tar.gz HTTP Error 404: Not Found
0 packages and 1 specfiles checked; 5 errors, 1 warnings.

```

Наблюдения:

* Предупреждение ``invalid-url Source0`` wговорит о том, что URL-адрес, указанный в директиве Source0 - недоступен. Это ожидаемо, поскольку указанный example.com URL-адрес не существует. Предполагая, что мы ожидаем, что этот URL-адрес будет работать в будущем, мы можем проигнорировать это предупреждение.
* Ошибок много, потому что мы намеренно написали этот файл спецификации, чтобы он был простым и показывал, о каких ошибках может сообщать ``rpmlint``.
* Ошибки ``hardcoded-library-path`` предполагают использование макроса  ``%{_libdir}`` вместо жесткого кодирования пути к библиотеке. Ради этого примера мы игнорируем эти ошибки, но для пакетов, запущенных в производство, Вам нужна веская причина для игнорирования этой ошибки.

Это результат выполнения ``rpmlint`` на SRPM  ``pello``:

```bash
$ rpmlint ~/rpmbuild/SRPMS/pello-0.1.1-1.el7.src.rpm
pello.src: W: invalid-url URL: https://www.example.com/pello HTTP Error 404: Not Found
pello.src:30: E: hardcoded-library-path in %{buildroot}/usr/lib/%{name}
pello.src:34: E: hardcoded-library-path in /usr/lib/%{name}/%{name}.pyc
pello.src:39: E: hardcoded-library-path in %{buildroot}/usr/lib/%{name}/
pello.src:43: E: hardcoded-library-path in /usr/lib/%{name}/
pello.src:45: E: hardcoded-library-path in /usr/lib/%{name}/%{name}.py*
pello.src: W: invalid-url Source0: https://www.example.com/pello/releases/pello-0.1.1.tar.gz HTTP Error 404: Not Found
1 packages and 0 specfiles checked; 5 errors, 2 warnings.

```

Наблюдения:

* Новая ошибка``invalid-url URL`` здесь связана с директивой ``URL``, которая недоступна.  Предполагая, что мы ожидаем, что URL-адрес станет действительным в будущем, мы можем игнорировать эту ошибку.

### Проверка бинарного RPM pello 

При проверке бинарного RPMs, ``rpmlint`` проверяет дополнительные параметры, в том числе:

1. документацию
2. последовательное использование

Это результат выполнения ``rpmlint`` на бинарном RPM для ``pello``:

```bash
$ rpmlint ~/rpmbuild/RPMS/noarch/pello-0.1.1-1.el7.noarch.rpm
pello.noarch: W: invalid-url URL: https://www.example.com/pello HTTP Error 404: Not Found
pello.noarch: W: only-non-binary-in-usr-lib
pello.noarch: W: no-documentation
pello.noarch: E: non-executable-script /usr/lib/pello/pello.py 0644L /usr/bin/env
pello.noarch: W: no-manual-page-for-binary pello
1 packages and 0 specfiles checked; 1 errors, 4 warnings.

```

Наблюдения:

* Предупреждения  ``no-documentation`` и ``no-manual-page-for-binary`` говорят о том, что в RPM нет документации или страниц руководства, потому что мы их не предоставили.
* Предупреждение  ``only-non-binary-in-usr-lib`` гласит, что Вы предоставили только бинарные артефакты  ``/usr/lib/``. Этот каталог обычно зарезервирован для общих объектных файлов, которые являются бинарными файлами. Следовательно, ``rpmlint`` eожидает, что по крайней мере один или несколько файлов в ``/usr/lib/`` будут бинарными.

  Это пример проверки ``rpmlint`` на соответствие
  .

  Обычно для обеспечения правильного размещения файлов используются макросы RPM. Ради этого примера мы можем проигнорировать это предупреждение.
* Ошибка ``non-executable-script`` предупреждает о том, что ``/usr/lib/pello/pello.py``
файл не имеет прав на выполнение. Поскольку этот файл содержит
, ``rpmlint`` ожидает, что файл будет исполняемым. Для целей примера оставьте этот файл без разрешений на выполнение и проигнорируйте эту ошибку.

Помимо вышеприведенных предупреждений и ошибок, наш RPM проходит проверку ``rpmlint``.

### Проверка SPEC файла cello 

Это результат выполнения ``rpmlint`` на SPEC файле ``cello``:

```bash
$ rpmlint ~/rpmbuild/SPECS/cello.spec
/home/admiller/rpmbuild/SPECS/cello.spec: W: invalid-url Source0: https://www.example.com/cello/releases/cello-1.0.tar.gz HTTP Error 404: Not Found
0 packages and 1 specfiles checked; 0 errors, 1 warnings.

```

Наблюдения:

* Единственное предупреждение для ``cello.spec`` гласит, что URL-адрес, указанный в директиве 
``Source0``, недоступен. Это ожидаемо, поскольку указанный ``example.com`` URL-адрес не существует. Предполагая, что мы ожидаем, что этот URL-адрес будет работать в будущем, мы можем проигнорировать это предупреждение.

Это результат выполнения ``rpmlint`` в файле SRPM для ``cello``:

```bash
$ rpmlint ~/rpmbuild/SRPMS/cello-1.0-1.el7.src.rpm
cello.src: W: invalid-url URL: https://www.example.com/cello HTTP Error 404: Not Found
cello.src: W: invalid-url Source0: https://www.example.com/cello/releases/cello-1.0.tar.gz HTTP Error 404: Not Found
1 packages and 0 specfiles checked; 0 errors, 2 warnings.

```

Наблюдения:

* Для ``cello`` SRPM появилось новое предупреждение, в котором говорится, что URL-адрес, указанный в директиве ``URL``, недоступен.  Предполагая, что ссылка будет работать в будущем, мы можем проигнорировать это предупреждение.

### Проверка бинарного RPM cello

При проверке бинарных RPMs, ``rpmlint`` проверяет дополнительные параметры, в том числе:

1. документацию
.

Это результат выполнения ``rpmlint`` на бинарном RPM для ``cello``:

```bash
$ rpmlint ~/rpmbuild/RPMS/x86_64/cello-1.0-1.el7.x86_64.rpm
cello.x86_64: W: invalid-url URL: https://www.example.com/cello HTTP Error 404: Not Found
cello.x86_64: W: no-documentation
cello.x86_64: W: no-manual-page-for-binary cello
1 packages and 0 specfiles checked; 0 errors, 3 warnings.
```

Наблюдения:

* Предупреждения ``no-documentation`` и ``no-manual-page-for-binary`` говорят о том, что в RPM нет документации или страниц руководства, потому что мы их не предоставили.
