Руководство по миграции 3.0
###########################

Эта страница обобщает изменения относительно CakePHP 2.x, что поможет вам
безболезненно перевести ваши проекты на версию 3.0, а также руководство по
изменениям, внесенным в ядро в сравнении с веткой CakePHP 2.x. Убедительно
просим прочитать остальные сраницы в этом руководстве для ознакомления со всеми
нововведениями и изменениями в API.

Системные требования
====================

- CakePHP 3.x поддерживает PHP версии 5.4.16 и выше.
- CakePHP 3.x требует наличия расширения mbstring.
- CakePHP 3.x требует наличия расширения intl.

.. warning::

    CakePHP 3.0 не будет работать если вышеприведенные условия не будут соблюдены.

Инструментарий обновления
=========================

В то время, как этот документ охватывает все радикальные изменения и улучшения
сделанные в CakePHP 3.0, мы также создали консольное приложение для того,
чтобы помочь вам совершить некоторые рутинные операции при обновлении.
Вы можете `получить инструментарий обновления с github
<https://github.com/cakephp/upgrade>`_.

Структура папок приложения
==========================

Структура папок приложения притерпела изменения и теперь соответствует
стандарту `PSR-4 <http://www.php-fig.org/psr/psr-4/>`_. Вы должны использовать
в качестве примера проект
`каркаса приложения <https://github.com/cakephp/app>`_ при обновлении вашего
приложения.

CakePHP должен устанавливаться с помощью Composer
=================================================

С того момента, как CakePHP больше не может устанавливаться через PEAR или в
общую папку, данные опции больше не поддерживаются. Вместо этого вы должны
использовать `Composer <http://getcomposer.org>`_ для установки  CakePHP.

Пространства имен
=================

Все классы ядра CakePHP сейчас используют пространства имен и следуют
спецификациям автозагрузки PSR-4. К примеру **src/Cache/Cache.php** доступен
как ``Cake\Cache\Cache``. Глобальные константы и методы-хелперы, такие как
:php:meth:`__()` и :php:meth:`debug()` не используют пространства имен,
чтобы ими было удобнее пользоваться.

Удаленные константы
===================

Приведенные ниже устаревшие константы были удалены:

* ``IMAGES``
* ``CSS``
* ``JS``
* ``IMAGES_URL``
* ``JS_URL``
* ``CSS_URL``
* ``DEFAULT_LANGUAGE``

Конфигурация
============

Конфигурация в CakePHP 3.0 значительно отличается от предыдущих версий.
Вы должны прочитать раздел :doc:`Конфигурация </development/configuration>`,
чтобы более детально изучить все изменения в версии 3.0.

Вы больше не можете использовать ``App::build()`` для указания дополнительных
путей для используемых классов. Вместо этого вы должны прописывать их,
используя автозагрузчик вашего приложения. Посмотрите раздел
:ref:`дополнительные пути для классов <additional-class-paths>` для
получения более подробной информации.

Три новые конфигурационные переменные предоставляют настройки путей для
файлов плагинов, видов и локалей. Вы можете добавить множество путей в
переменные ``App.paths.templates``, ``App.paths.plugins``,
``App.paths.locales`` для настройки множества путей для шаблонов, плагинов
и локалей соответственно.

Ключ конфигурации ``www_root`` был заменен на ``wwwRoot`` для лучшей
согласованности. Пожалуйста внесите все необходимые правки в ваш файл
**app.php**, а также везде, где используется вызов данного параметра
``Configure::read('App.wwwRoot')``.

Новая ORM
=========

CakePHP 3.0 представляет вам новую ORM, которая была перестроена с нуля. Новая
ORM значительно отличается от предшествующей и несовместима с ней. Обновление
до новой ORM потребует обширных правок в любом из обновляемых приложений.
Смотрите новую документацию по :doc:`Доступу к Базе Данных и ORM </orm>` для
получения информации по использованию новой ORM.

Основы
======

* ``LogError()`` был удален, так как не предоставлял никакой выгоды и либо
  использовался редко, либо не использовался вообще.
* Следующие глобальные функции были удалены: ``config()``, ``cache()``,
  ``clearCache()``, ``convertSlashes()``, ``am()``, ``fileExistsInPath()``,
  ``sortByKey()``.

Отладка
=======

* ``Configure::write('debug', $bool)`` больше не поддерживает значения 0/1/2.
  Вместо этого теперь используется простое булево значение, чтобы просто
  включать/выключать режим отладки.

Настройки/конфигурация объекта
==============================

* Объекты, используемые в CakePHP теперь поддерживают цепочки вызова методов
  экземпляра класса. К примеру код ранее использовавший свойство
  ``$object->settings`` теперь должен быть обновлен для использования метода
  ``$object->config()``.

Кеш
===

* Движок``Memcache`` был удален, используйте вместо него :php:class:`Cake\\Cache\\Cache\\Engine\\Memcached`.
* Движки кеширования теперь загружаются при первом использовании с помощью ленивой загрузки.
* Добавлен метод :php:meth:`Cake\\Cache\\Cache::engine()`.
* Добавлен метод :php:meth:`Cake\\Cache\\Cache::enabled()`. Он заменил
  опцию ``Cache.disable``.
* Добавлен метод :php:meth:`Cake\\Cache\\Cache::enable()`.
* Добавлен метод :php:meth:`Cake\\Cache\\Cache::disable()`.
* Конфигурации кеша теперь неизменяемы. Если вам потребуется изменить их,
  вы должны сначала уничожить конфигурацию, затем воссоздать ее. Это предотвращает
  проблемы синхронизации  с опциями конфигурации.
* Метод ``Cache::set()`` удален. Рекомендуется, чтобы вы создавали множество
  конфигураций кеша вместо хаков во время исполнения возможных ранее
  при использовании ``Cache::set()``.
* Все подклассы ``CacheEngine`` сейчас содержат метод ``config()``.
* Были добавлены методы :php:meth:`Cake\\Cache\\Cache::readMany()`, :php:meth:`Cake\\Cache\\Cache::deleteMany()`,
  и :php:meth:`Cake\\Cache\\Cache::writeMany()`.

Все методы :php:class:`Cake\\Cache\\Cache\\CacheEngine` теперь отвечают за
обработку настроенного префикса ключа. Метод
:php:meth:`Cake\\Cache\\CacheEngine::write()` больше не позволяет устанавливать
продолжительность жизни кеша при записи - продолжительность берется из параметров
движков кеширования, применяемых во время исполнения. Вызов метода кеширования
с пустым ключом теперь выбросит исключение :php:class:`InvalidArgumentException`,
вместо того, чтобы вернуть ``false``.

Ядро
====

App
---

- Метод ``App::pluginPath()`` удален. Используйте ``CakePlugin::path()``.
- Метод ``App::build()`` удален.
- Метод ``App::location()`` удален.
- Метод ``App::paths()`` удален.
- Метод ``App::load()`` удален.
- Метод ``App::objects()`` удален.
- Константа ``App::RESET`` удалена.
- Константа ``App::APPEND`` удалена.
- Константа ``App::PREPEND`` удалена.
- Константа ``App::REGISTER`` удалена.

Plugin
------

- :php:meth:`Cake\\Core\\Plugin::load()` не использует автозагрузку классов
  пока вы не установите опцию ``autoload`` в ``true``.
- При загрузке плагинов вы больше не можете предоставить вызываемый метод.
- При загрузке плагинов вы больше не можете предоставить массив файлов
  конфигурации для загрузки.

Configure
---------

- ``Cake\Configure\PhpReader`` переименован в
  :php:class:`Cake\\Core\\Configure\\Engine\PhpConfig`
- ``Cake\Configure\IniReader`` переименован в
  :php:class:`Cake\\Core\\Configure\\Engine\IniConfig`
- ``Cake\Configure\ConfigReaderInterface`` переименован в
  :php:class:`Cake\\Core\\Configure\\ConfigEngineInterface`
- Добавлен метод :php:meth:`Cake\\Core\\Configure::consume()`.
- Метод :php:meth:`Cake\\Core\\Configure::load()` теперь ожидает имя файла без
  расширения, так как оно может быть получено из движка. К примеру используя
  PhpConfig указывайте ``app`` для загрузки **app.php**.
- Назначение переменной ``$config`` в файле конфигурации PHP устарело.
  :php:class:`Cake\\Core\\Configure\\Engine\PhpConfig` теперь ожидает, что
  файл конфигурации будет возвращать массив.
- Добавлен новый движок :php:class:`Cake\\Core\\Configure\\Engine\JsonConfig`.

Object
------

Класс ``Object`` был удален. Ранее он содержал кучу различных методов,
использовавшихся в разных местах фреймворка. Наиболее востребованные из этих
методов были выделены в трейты. Вы можете использовать трейт
:php:trait:`Cake\\Log\\LogTrait` для получения доступа к методу ``log()``.
Трейт :php:trait:`Cake\\Routing\\RequestActionTrait` предоставляет
``requestAction()``.

Консоль
=======

Исполняемый файл ``cake`` был перемещен из папки **app/Console** в папку **bin**
вместе со всей структурой приложения. Теперь вы можете вызывать консоль CakePHP
используя ``bin/cake``.

Заменен класс TaskCollection
----------------------------

Этот класс был переименован в :php:class:`Cake\\Console\\TaskRegistry`.
Смотрите раздел :doc:`/core-libraries/registry-objects` для получения
более полной информации о возможностях данного класса. Вы можете использовать
``cake upgrade rename_collections`` для помощи в обновлении вашего кода.
This class has been renamed to :php:class:`Cake\\Console\\TaskRegistry`.
Задачи больше не имеют доступа к функциям обратного вызова, поскольку никогда
не было таких функций, которые могли бы вызываться подобным образом.

Оболочка (Shell)
----------------

- ``Shell::__construct()`` изменен. Теперь он берет экземпляр класса
  :php:class:`Cake\\Console\\ConsoleIo`.
- Добавлен метод ``Shell::param()`` для более удобного доступа к параметрам.

В дополнение к этому все методы оболочки преобразованы для удобства к
верблюжьему регистру при их вызове. Например, если у вас внутри оболочки был
метод ``hello_world()`` и он вызывался как ``bin/cake my_shell hello_world``,
вам будет нужно переименовать метод в ``helloWorld``. При вызове команд
никаких изменений не потребуется.

ConsoleOptionParser
-------------------

- Добавлен метод ``ConsoleOptionParser::merge()`` для объединения парсеров.

ConsoleInputArgument
--------------------

- Добавлен метод ``ConsoleInputArgument::isEqualTo()`` для сравнения двух
  аргументов.

Shell / Task
============

Оболочки и задачи перемещены из ``Console/Command`` и
``Console/Command/Task`` в ``Shell`` и ``Shell/Task``.

Удалена ApiShell
----------------

ApiShell была удалена, так как не представляла никакой выгоды над
непосредственно самими исходными текстами и онлайн документацией
`онлайн документацией/API <https://api.cakephp.org/>`_.

Удалена SchemaShell
-------------------

SchemaShell была удалена, так как ниогда полностью не реализовывала в полной
мере миграцию баз данных, к тому же появились более мощные инструменты, такие
как `Phinx <https://phinx.org/>`_. Она была заменена на
`Плагин Миграции CakePHP <https://github.com/cakephp/migrations>`_, который
действует в качестве оболочки между CakePHP и `Phinx <https://phinx.org/>`_.

ExtractTask
-----------

- ``bin/cake i18n extract`` больше не включает в себя непереведенные сообщения
  валидации. Если вы хотите, чтобы ваши сообщения валидации переводились на
  другие языки, вы должны обертывать их в фунцию `__()`, как и другой контент.

BakeShell / TemplateTask
------------------------

- Bake больше не является частью ядра и заменен на плагин
  `CakePHP Bake <https://github.com/cakephp/bake>`_
- Шаблоны Bake перемещены в **src/Template/Bake**.
- Синтаксис шаблонов Bake теперь использует erb-стилизованные теги
  (``<% %>``) для обозначения логики шаблонов, позволяя php-коду
  обрабвтываться как простой текст.
- Команда ``bake view`` переименована в ``bake template``.

Событие
=======

Метод ``getEventManager()`` был удален из всех объектов, в которых он
присутствовал.  Метод ``eventManager()`` сейчас предоставляется с помощью
``EventManagerTrait``. Он содержит логику создания экземпляров класса и
сохраняет связь с локальным менеджером событий.

Подсистема Event лишилась ряда опциональных возможностей. При
управляя событиями вы больше не можете пользоваться следующими опциями:

* Опция ``passParams`` теперь включена постоянно. Вы не можете отключить
  ее.
* Опция ``break`` удалена. Теперь вы должны останавливать события.
* Опция ``breakOn`` удалена. Теперь вы должны останавливать события.

Логи (журналирование)
=====================

* Конфигурации логов теперь неизменяемые. Если вам нужно внести изменения в
  конфигурацию, вы должны ее и создать заново. Это предотвратит проблемы
  синхронизации для параметров конфигурации.
* Движки журналирования теперь используют лениво загружаются при первой записи в логи.
* Добавлен метод :php:meth:`Cake\\Log\\Log::engine()`.
* Следующие методы удалены из :php:class:`Cake\\Log\\Log` ::
  ``defaultLevels()``, ``enabled()``, ``enable()``, ``disable()``.
* Вы больше не можете создавать пользовательские уровни, используя ``Log::levels()``.
* При конфигурировании логгеров вы должны использовать ``'levels'`` вместо ``'types'``.
* Вы больше не можете определять пользовательские уровни.  Вы должны использовать
  предустановленный набор уровней журналирования. Вы должны использовать области
  видимости журналирования для создания пользовательских лог-файлов или
  для определенной обработки различных частей вашего приложения. Использование
  нестандартных уровней журналирования теперь выбросит исключение.
* Добавлен :php:trait:`Cake\\Log\\LogTrait`. Вы можете использовать этот трейт в
  своих классах для добавления метода ``log()``.
* Области видимости, передаваемые :php:meth:`Cake\\Log\\Log::write()` теперь
  пробрасываются методу ``write()`` движков журналирования с целью предоставления
  лучшего контекста движкам.
* Движки журналирования теперь обязаны наследовать интерфейс ``Psr\Log\LogInterface``
  вместо собственного интерфейса Cake ``LogInterface``. В общем, если вы расширяли
  :php:class:`Cake\\Log\\Engine\\BaseEngine` вам просто нужно переименовать метод
  ``write()`` в ``log()``.
* :php:meth:`Cake\\Log\\Engine\\FileLog` теперь пишет файлы в ``ROOT/logs`` вместо ``ROOT/tmp/logs``.

Роутинг (маршрутизация)
=======================

Именованные параметры
---------------------

Именованные параметы были удпразднены в версии 3.0. Они были добавлены в версии
1.2.0 как 'милая' версия параметров строки запроса. В то время как визуальные
преимущества были весомыми, проблемы, возникающие из-за них того не стоили.

Именованные параметры требовали особой обработки в CakePHP так же как и в
любой PHP- или JavaScript-библиотеке, взаимодействующей с ними, так как они не
распознавались никакой библиотекой, *за исключением* CakePHP. Дополнительное
усложнение структуры кода, необходимое для поддержки именованных параметров
не оправдывало их существование, и они были упразднены. Вместо них вы должны
использовать стандартные параметры строки запроса или же передаваемые аргументы.
По умолчанию ``Router`` будет рассматривать любые дополнительные параметры
метода ``Router::url()`` как параметры строки запроса.

Поскольку многие приложения все еще будут вынуждены обрабатывать входящие
URL-адреса, содержащие именованные параметры, был добавлен метод
:php:meth:`Cake\\Routing\\Router::parseNamedParams()` для обеспечения обратной
совместимости с существующими URL.

RequestActionTrait
------------------

- В методе :php:meth:`Cake\\Routing\\RequestActionTrait::requestAction()`
  изменены некоторые дополнительные параметры:

  - ``options[url]`` заменен на ``options[query]``.
  - ``options[data]`` заменен на ``options[post]``.
  - Именованные параметры больше не поддерживаются.

Router
------

* Именованные параметры удалены, более полную информацию смотрите выше.
* Опция ``full_base`` заменена опцией ``_full``.
* Опция ``ext`` заменена опцией ``_ext``.
* Добавлены опции ``_scheme``, ``_port``, ``_host``, ``_base``, ``_full``, ``_ext``.
* Строки URL больше не изменяются добавлением имен плагина/контроллера/префикса.
* Стандартная обработка отсутствующих маршрутов была упразднена.  Если нет
  маршрутов подходящих под параметры - будет возвращен путь ``/``.
* Классы маршрутов ответственны за *всё* генерирование URL включая параметры строки
  запроса. Это делает маршруты намного более мощными и гибкими.
* Устойчивые параметры были удалены. Они были заменены методом
  :php:meth:`Cake\\Routing\\Router::urlFilter()`, который предоставляет
  более гибкий способ видоизменять URL при обратной маршрутизации.
* Удален метод ``Router::parseExtensions()``.
  Используйте вместо этого :php:meth:`Cake\\Routing\\Router::extensions()`. Этот метод
  **должен** быть вызван перед подключением маршрутов. Он не затронет уже существующие
  маршруты.
* Удален метод ``Router::setExtensions()``.
  Используйте вместо этого :php:meth:`Cake\\Routing\\Router::extensions()`.
* Удален метод ``Router::resourceMap()``.
* Опция ``[метод]`` переименована в ``_метод``.
* Возможность совпадения произвольных заголовков с параметрамив стиле ``[]`` была
  упразднена. If you need to parse/match on arbitrary conditions consider using
  custom route classes.
* Удален метод ``Router::promote()``.
* ``Router::parse()`` теперь выбрасывает исключение, когда URL не может быть
  обработан каким-либо из маршрутов.
* ``Router::url()`` теперь выбрасывает исключение, когда ни один маршрут не
  совпадает с набором параметров.
* Добавлены области видимости маршрутов (routing scopes). Они позволяют вам
  соблюдать принцип DRY (не повторяйся) в ваших файлах маршрутов и дают Роутеру
  подсказки, как оптимизировать парсинг и обратную маршрутизацию URL.
