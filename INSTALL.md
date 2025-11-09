# Сборка и установка

Обычно рекомендуется использовать диспетчер пакетов вашего дистрибутива для установки
Notcurses; он [доступен](https://repology.org/project/notcurses/versions)
в виде готовых пакетов для многих дистрибутивов. Если вы хотите собрать из исходного кода, читайте дальше.

## Требования для сборки

Получите текущий исходный код с помощью команды

`git clone https://github.com/dankamongmen/notcurses.git`

Подмодули отсутствуют. Зависимости довольно минимальны.

### APT

Установите зависимости сборки:

`apt-get install build-essential cmake doctest-dev libavdevice-dev libdeflate-dev libgpm-dev libncurses-dev libqrcodegen-dev libswscale-dev libunistring-dev pandoc pkg-config`

Если вы хотите создать только ядро Notcurses (без поддержки мультимедиа), вы
можете убрать `libavdevice-dev` из этого списка. `zlib1g-dev` можно использовать вместо
`libdeflate-dev`; в этом случае сборку следует выполнять с опцией `-DUSE_DEFLATE=off`.
Если вы не планируете генерировать QR-коды, можно убрать 'libqrcodegen-dev'.

Если вы хотите собрать обёртки для Python, вам также понадобятся:

`apt-get install python3-cffi python3-dev python3-pypandoc python3-setuptools`

### RPM

Установите зависимости сборки:

`dnf install cmake doctest-devel libdeflate-devel ncurses-devel gpm-devel libqrcodegen-devel libunistring-devel OpenImageIO-devel pandoc`

Если вы хотите создать только ядро Notcurses (без поддержки мультимедиа), вы
можете убрать `OpenImageIO-devel`. Если вы собираете вне Fedora Core (например, с RPM Fusion),
возможно, стоит использовать FFmpeg вместо OpenImageIO. Если вы не планируете генерировать
QR-коды, можно убрать 'libqrcodegen-devel'. `zlib-devel` можно использовать вместо
`libdeflate-devel`; в этом случае сборку следует выполнять с опцией `-DUSE_DEFLATE=off`.

### FreeBSD / DragonFly BSD

Установите зависимости сборки:

`pkg install archivers/libdeflate devel/ncurses multimedia/ffmpeg graphics/qr-code-generator devel/libunistring`

Если вы хотите создать только ядро Notcurses (без поддержки мультимедиа), вы
можете убрать `multimedia/ffmpeg`. Если вы не хотите сжимать графику Kitty,
можно убрать 'archivers/libdeflate'; в этом случае сборку следует выполнять с опцией `-DUSE_DEFLATE=off`.
Если вы не планируете генерировать QR-коды, можно убрать 'graphics/qr-code-generator'.

### Microsoft Windows

Сборка в Windows требует [MSYS2](https://www.msys2.org/) в его 64-битной версии
Universal C Runtime (UCRT). Это позволяет создавать нативные DLL и EXE для Windows,
хотя и не использует Visual Studio. Установите зависимости сборки:

`pacman -S mingw-w64-ucrt-x86_64-cmake mingw-w64-ucrt-x86_64-libdeflate mingw-w64-ucrt-x86_64-libunistring mingw-w64-ucrt-x86_64-ncurses mingw-w64-ucrt-x86_64-ninja mingw-w64-ucrt-x86_64-openimageio mingw-w64-ucrt-x86_64-toolchain`

Обратите внимание, что в Windows (на данный момент) рекомендуется использовать OpenImageIO, а не FFmpeg.

Если вы хотите создать только ядро Notcurses (без поддержки мультимедиа), вы
можете убрать `mingw-w64-ucrt-x86_64-openimageio`. Если вы не хотите сжимать графику Kitty,
можно убрать 'mingw-w64-ucrt-x86_64-libdeflate'; в этом случае сборку следует выполнять с опцией
`-DUSE_DEFLATE=off`.

Следует добавить `-DUSE_DOCTEST=off -DUSE_PANDOC=off` к `cmake`.
`notcurses-tester` в данный момент не работает на Windows, и, вероятно,
вы не захотите собирать документацию в стиле UNIX.

## Сборка

* Создайте подкаталог, традиционно называемый `build` (это не обязательно,
  но позволяет держать дерево исходников чистым). Перейдите в этот каталог.
* `cmake ..`
  * Вы можете установить, например, `CMAKE_BUILD_TYPE`. Используйте `-DVAR=val`.
  * Чтобы собрать без поддержки мультимедиа, используйте `-DUSE_MULTIMEDIA=none`.
* `make`
* `make test`
* `make install`
* `sudo ldconfig`

По умолчанию мультимедийным движком является FFmpeg. Вы можете выбрать другой движок
с помощью `USE_MULTIMEDIA`. Допустимые значения: `ffmpeg`, `oiio` (для OpenImageIO),
или `none`. Без мультимедийного движка Notcurses не сможет декодировать
изображения и видео.

Чтобы получить события мыши в консоли Linux, вам понадобится запущенный демон GPM,
и вам нужно будет запустить `cmake` c `-DUSE_GPM=on`.

Запустите модульные тесты с помощью `make test` после успешной сборки. Если какие-либо тесты
не проходят, *пожалуйста*, сообщите об ошибке, приложив вывод команды

`./notcurses-tester -p ../data`

(`make test` также запускает `notcurses-tester`, но скрывает важный вывод).

Чтобы посмотреть крутую демонстрацию, запустите `make demo` (или `./notcurses-demo -p ../data`).
Более подробную информацию можно найти на man странице `notcurses-demo(1)`.

Установите с помощью `make install` после успешной сборки. Это установит ядро C
библиотеки, заголовочные файлы C, C++ библиотеку и заголовочные файлы C++ (обратите внимание,
что заголовочные файлы на C безопасны для C++). Python-обёртки при этом не устанавливаются. Чтобы
установить Python-обёртки (после установки ядра библиотеки), выполните:

```
cd cffi
python setup.py build
python setup.py install
```

Python обёртки также доступны на [PyPi](https://pypi.org/project/notcurses/).

### Параметры сборки

Чтобы задать компилятор C, экспортируйте `CC`. Чтобы задать компилятор C++, экспортируйте `CXX`.
Переменная CMake `CMAKE_BUILD_TYPE` CMake может быть установлена в любое из стандартных значений,
но для использования `USE_COVERAGE` она должна быть `Debug`.

* `DFSG_BUILD`: исключить весь контент, считающийся несвободным по Debian Free Software Guidelines (по умолчанию `off`)
* `BUILD_TESTING`: собирать тестовые цели (по умолчанию `on`)
* `BUILD_EXECUTABLES`: собирать исполняемые файлы (в дополнение к библиотекам) (по умолчанию `on`)
* `BUILD_FFI_LIBRARY`: собирать ffi библиотеку (содержащую все символы, объявленные как static inline) (по умолчанию `on`)
* `USE_ASAN`: сборка с AddressSanitizer (по умолчанию `off`)
* `USE_CXX`: собирать C++ код (требует C++ компилятор) (по умолчанию `on`)
* `USE_COVERAGE`: собирать поддержку coverage (разработчиков, требует использования Clang) (по умолчанию `off`)
* `USE_DOCTEST`: собирать `notcurses-tester` с Doctest, требует `BUILD_TESTING` и `USE_CXX` (по умолчанию `on`)
* `USE_DOXYGEN`: собирать взаимосвязанную HTML документацию с помощью Doxygen (по умолчанию `off`)
* `USE_GPM`: собирать поддержку мыши в консоли GPM через libgpm (по умолчанию `off`)
* `USE_MULTIMEDIA`: `ffmpeg` для FFmpeg, `oiio` для OpenImageIO, `none` без мультимедиа (по умолчанию `ffmpeg`)
  * `oiio` не может использоваться при `USE_CXX=off`
* `USE_PANDOC`: собирать man страницы с помощью pandoc (по умолчанию `on`)
* `USE_POC`: build small, uninstalled proof-of-concept binaries (default `on`)
* `USE_QRCODEGEN`: собирать поддержку qr-кодов через libqrcodegen (default `off`)
* `USE_STATIC`: собирать статические библиотеки (в дополнение к shared) (default `on`)
