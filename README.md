## Laboratory work VI

Данная лабораторная работа посвещена изучению средств пакетирования на примере **CPack**

```ShellSession
$ open https://cmake.org/Wiki/CMake:CPackPackageGenerators
```

## Tasks

- [X] 1. Создать публичный репозиторий с названием **lab06** на сервисе **GitHub**
- [X] 2. Выполнить инструкцию учебного материала
- [X] 3. Ознакомиться со ссылками учебного материала
- [X] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial
Установка переменных
```ShellSession
$ export GITHUB_USERNAME=CrazyOverdose
$ export GITHUB_EMAIL=alenkavh@yandex.ru
$ alias edit=nano
$ alias gsed=sed # for *-nix system
```
Подготовка рабочего пространства
```ShellSession
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .

$ source scripts/activate
```
Клонирование репозитория ЛР5 в ЛР6
```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab06
Клонирование в «projects/lab06»…
remote: Enumerating objects: 48, done.
remote: Counting objects: 100% (48/48), done.
remote: Compressing objects: 100% (29/29), done.
remote: Total 48 (delta 13), reused 44 (delta 12), pack-reused 0
Распаковка объектов: 100% (48/48), готово.
$ cd projects/lab06
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06
```
Редактирование CMakeLists.txt. 
```ShellSession
$ gsed -i '/project(print)/a\   # Дописывание в файл указанной строки
set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION\
  \${PRINT_VERSION_MAJOR}.\${PRINT_VERSION_MINOR}.\${PRINT_VERSION_PATCH}.\${PRINT_VERSION_TWEAK})
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_TWEAK 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_PATCH 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_MINOR 1)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_MAJOR 0)
' CMakeLists.txt
$ git diff
diff --git a/CMakeLists.txt b/CMakeLists.txt
index 89739e7..0f4c943 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -7,6 +7,13 @@ option(BUILD_EXAMPLES "Build examples" OFF)
 option(BUILD_TESTS "Build tests" OFF)
 
 project(print)
+set(PRINT_VERSION_MAJOR 0)
+set(PRINT_VERSION_MINOR 1)
+set(PRINT_VERSION_PATCH 0)
+set(PRINT_VERSION_TWEAK 0)
+set(PRINT_VERSION
+${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
+set(PRINT_VERSION_STRING "v${PRINT_VERSION}")
 
 add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
```
Настройка файлов логирования
```ShellSession
$ touch DESCRIPTION && edit DESCRIPTION    # Создание и редактирование DESCRIPTION
$ touch ChangeLog.md                       # Создание ChangeLog.md
$ export DATE="`LANG=en_US date +'%a %b %d %Y'`"  # Создание переменной DATE
$ cat > ChangeLog.md <<EOF                         # Запись в файл ChangeLog.md
* ${DATE} ${GITHUB_USERNAME} <${GITHUB_EMAIL}> 0.1.0.0
- Initial RPM release
EOF
```
Включаем в проект InstallRequiredSystemLibraries
```ShellSession
$ cat > CPackConfig.cmake <<EOF  # Запись в файл указанной строки
include(InstallRequiredSystemLibraries)
EOF
```
Дописывание в файл конфигурации переменных
```ShellSession
$ cat >> CPackConfig.cmake <<EOF       # Запись в файл ChangeLog.md
set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL})
set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")
EOF
```
Включаем в пакет лицензию и README.md
```ShellSession
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
EOF
```
Добавление в конфигурацию информации о RPM
```ShellSession
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
EOF
```
Добавление информации о DEB
```ShellSession
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
EOF
```
Подключение CPack в CMake конфиг
```ShellSession
$ cat >> CPackConfig.cmake <<EOF

include(CPack)
EOF
```
Подключение дополнительной конфигурации
```ShellSession
$ cat >> CMakeLists.txt <<EOF

include(CPackConfig.cmake)
EOF
```
Изменение README.md
```ShellSession
$ gsed -i 's/lab05/lab06/g' README.md
```
Отправка изменений
```ShellSession
$ git add .                            # Фиксация изменений
$ git commit -m"added cpack config"
[master 19bf855] added cpack config
 5 files changed, 62 insertions(+), 32 deletions(-)
 create mode 100644 CPackConfig.cmake
 create mode 100644 ChangeLog.md
 create mode 100644 DESCRIPTION
$ git tag v0.1.0.0
$ git push origin master --tags          # Отправка изменений
Username for 'https://github.com': hijadelaluuna
Password for 'https://hijadelaluuna@github.com': 
Перечисление объектов: 54, готово.
Подсчет объектов: 100% (54/54), готово.
Сжатие объектов: 100% (47/47), готово.
Запись объектов: 100% (54/54), 29.03 KiB | 4.15 MiB/s, готово.
Всего 54 (изменения 16), повторно использовано 0 (изменения 0)
remote: Resolving deltas: 100% (16/16), done.
To https://github.com/hijadelaluuna/lab06
 * [new branch]      master -> master
 * [new tag]         v0.1.0.0 -> v0.1.0.0
```
Настройка Travis CI
```ShellSession
$ travis login --auto      # Авторизация в Travis CI
Successfully logged in as hijadelaluuna!
$ travis enable                 # Включение обработки в Travis CI
Detected repository as hijadelaluuna/lab06, is this correct? |yes| y
CrazyOverdose/lab06: enabled :)
```
Сборка и компиляция через CMake. Создание пакета через CPack
```ShellSession
$ cmake -H. -B_build  # Сборка
-- The C compiler identification is GNU 8.3.0
-- The CXX compiler identification is GNU 8.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/luna/hijadelaluuna/workspace/projects/lab06/_build
$ cmake --build _build   # Компиляция
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
$ cd _build         # Переход в директорию
$ cpack -G "TGZ"    # Упаковка с использованием CPack
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /home/luna/hijadelaluuna/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
$ cd ..
```
Сборка и создание пакета через CMake
```ShellSession
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"   # Сборка
-- Configuring done
-- Generating done
-- Build files have been written to: /home/luna/hijadelaluuna/workspace/projects/lab06/_build
$ cmake --build _build --target package    # Компиляция
[100%] Built target print
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /home/luna/hijadelaluuna/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
```
Перемещение собранного пакета
```ShellSession  
$ mkdir artifacts     # Создание указанной папки 
$ mv _build/*.tar.gz artifacts  # Перемещение собранного пакета
$ tree artifacts   # Дерево папки
artifacts
└── print-0.1.0.0-Linux.tar.gz

0 directories, 1 file
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=06
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

После того, как вы настроили взаимодействие с системой непрерывной интеграции,</br>
обеспечив автоматическую сборку и тестирование ваших изменений, стоит задуматься</br>
о создание пакетов для измениний, которые помечаются тэгами (см. вкладку [releases](https://github.com/tp-labs/lab06/releases)).</br>
Пакет должен содержать приложение _solver_ из [предыдущего задания](https://github.com/tp-labs/lab03#задание-1)
Таким образом, каждый новый релиз будет состоять из следующих компонентов:
- архивы с файлами исходного кода (`.tar.gz`, `.zip`)
- пакеты с бинарным файлом _solver_ (`.deb`, `.rpm`, `.msi`, `.dmg`)

В качестве подсказки:
```bash
$ cat .travis.yml
os: osx
script:
...
- cpack -G DragNDrop # dmg

$ cat .travis.yml
os: linux
script:
...
- cpack -G DEB # deb

$ cat .travis.yml
os: linux
addons:
  apt:
    packages:
    - rpm
script:
...
- cpack -G RPM # rpm

$ cat appveyor.yml
platform:
- x86
- x64
build_script:
...
- cpack -G WIX # msi
```

Для этого нужно добавить ветвление в конфигурационные файлы для **CI** со следующей логикой:</br>
если **commit** помечен тэгом, то необходимо собрать пакеты (`DEB, RPM, WIX, DragNDrop, ...`) </br>
и разместить их на сервисе **GitHub**. (см. пример для [Travi CI](https://docs.travis-ci.com/user/deployment/releases))</br>

## Links

- [DMG](https://cmake.org/cmake/help/latest/module/CPackDMG.html)
- [DEB](https://cmake.org/cmake/help/latest/module/CPackDeb.html)
- [RPM](https://cmake.org/cmake/help/latest/module/CPackRPM.html)
- [NSIS](https://cmake.org/cmake/help/latest/module/CPackNSIS.html)

```
Copyright (c) 2015-2019 The ISC Authors
```
