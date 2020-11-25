# Нумерация версий Electron

> Детализированный взгляд на политику версионирования и ее реализацию.

Начиная с версии 2.0.0, Electron следует [semver](#semver). Следующая команда установит последнюю стабильную сборку Electron:

```sh
npm install --save-dev electron
```

Обновление до последней стабильной версии в существующем проекте:

```sh
npm install --save-dev electron@latest
```

## Версия 1.x

Electron versions *< 2.0* did not conform to the [semver](https://semver.org) spec: major versions corresponded to end-user API changes, minor versions corresponded to Chromium major releases, and patch versions corresponded to new features and bug fixes. Удобно для разработчиков при объединении возможностей, но это создает проблемы для разработчиков пользовательских приложений. Циклы тестирования QA основных приложений, таких как Slack, Stride, Teams, Skype, VS Code, Атомные и настольные компьютеры могут быть длительными, а стабильность является весьма желаемым результатом. Существует большой риск применения новых функций при использовании исправлений ошибок.

Вот пример стратегии 1.x:

![](../images/versioning-sketch-0.png)

Приложение разработанное с `1.8.1` не может взять `1. .3` исправление ошибок без поглощения `1. .2` особенность, или backporting the fix and maintenance of new release line.

## Версия 2.0 и выше

Ниже приводится несколько важных изменений в нашей стратегии 1.x. Каждое изменение предназначено для удовлетворения потребностей и приоритетов разработчиков/сопровождающих и разработчиков приложений.

1. Строгое использование семвера
2. Введение в теги `-beta`
3. Введение [обычных сообщений о коммитах](https://conventionalcommits.org/)
4. Хорошо определенные ветви стабилизации
5. Версия ветки `master` недоступна. Только стабилизирующие ветви содержат информацию о версии

Мы подробно рассмотрим как работает ветка git, как работает npm тег, что разработчики должны видеть и как можно изменить backport.

# semver

С 2.0, Electron будет следовать за семестром.

Ниже приведена таблица явного отображения типов изменений к соответствующей категории полутора (например, Майора, Малого или Патча).

| Основные версии                    | Незначительные увеличения версии      | Увеличить версию патча          |
| ---------------------------------- | ------------------------------------- | ------------------------------- |
| Electron разрыв API изменения      | Изменения в Electron небезопасном API | Исправления ошибок Electron     |
| Обновление основных версий Node.js | Node.js обновления младшей версии     | Обновления версии патча Node.js |
| Обновления версии Chromium         |                                       | патчи с хромовым фиксированием  |

Обратите внимание, что большинство обновлений Chromium будет считаться перерывом. Исправления, которые могут быть обратно будут выбраны в качестве патчей.

# Стабильные ветки

Стабилизационные ветви - это ветки, которые управляют параллельно с освоением, принимая только вишневые коммиты, связанные с безопасностью или стабильностью. Эти ветки никогда не сливаются с мастером.

![](../images/versioning-sketch-1.png)

Начиная с Electron 8, ветки стабилизации всегда **основные версии** линий, и названы в соответствии с следующим шаблоном `$MAJOR-x-y` e. . `8-x-y`.  До этого мы использовали **мелкие** строк версии и назвали их `$MAJOR-$MINOR-x` , например `2-0-x`

Мы позволяем многократным стабилизационным ветвям существовать одновременно, и намерена поддерживать как минимум два параллельных в любое время, при необходимости резервные исправления безопасности. ![](../images/versioning-sketch-2.png)

Более старые линии не будут поддерживаться GitHub, но другие группы могут самостоятельно брать на себя права собственности и backport стабильности и исправления безопасности. Мы не поощряем это, но понимаем, что это упрощает жизнь для многих разработчиков приложений.

# Бета-релизы и исправление багов

Developers want to know which releases are _safe_ to use. Даже невинные характеристики могут привести к регрессии в сложных приложениях. В то же время, блокировка исправленной версии опасна, так как вы игнорируете патчи безопасности и исправления ошибок, которые могут появиться с момента окончания вашей версии. Наша цель - разрешить следующие стандартные диапазоны semver в `package.json`:

* Используйте `~2.0.0` для допущения исправления только стабильности или связанных с безопасностью исправлений к вашему релизу `2.0.0`.
* Используйте `^2.0.0` для принятия неспокойной _разумно стабильной работы_ , а также исправлений безопасности и ошибок.

Во втором случае приложения, использующие `^` , все еще должны иметь возможность ожидать приемлемого уровня стабильности. Для этого semver позволяет _предварительный идентификатор_ указывать конкретную версию еще не _безопасная_ или _стабильная_.

Независимо от того, что вы выбрали, вам периодически придется загружать версию в `package.json` , так как нарушение изменений является фактом жизни Chromium.

Этот процесс является следующим:

1. Все новые строки основных и мелких релизов начинаются с бета-версии, обозначенной тегами пререлизов `бета-версии.`, например `2.0.0-beta.1`. After the first beta, subsequent beta releases must meet all of the following conditions:
    1. Изменение обратного API-совместимо (допускается устаревание)
    2. Риск соблюдения сроков стабильности должен быть низким.
2. Если допустимые изменения необходимо внести после выпуска бета-версии, то они применяются и увеличивается тэг prerelease, e. . `2.0.0-beta.2`.
3. If a particular beta release is _generally regarded_ as stable, it will be re-released as a stable build, changing only the version information. например, `2.0.0`. После первой стабильности, все изменения должны быть обратно совместимыми с ошибками или исправлениями безопасности.
4. If future bug fixes or security patches need to be made once a release is stable, they are applied and the _patch_ version is incremented e.g. `2.0.1`.

В частности, вышеуказанное означает:

1. Внесение не-breaking-API изменений перед 3-й неделей в бета-цикле нормально, даже если эти изменения могут вызвать умеренные боковые эффекты
2. Внесение изменений в функционал, которые в противном случае не изменяют существующие пути кода, в большинстве точек в бета-цикле нормально. Пользователи могут явно включать эти флаги в своих приложениях.
3. Добавляются возможности любого рода после 3-й недели в бета-цикле 👎 без очень хорошей причины.

По каждому главному и второстепенному шару, вы должны ожидать что-то вроде следующих:

```plaintext
2.0.0-beta.1
2.0.0-beta.2
2.0.0-beta.3
2.0.0
2.0.1
2.0.2
```

Пример жизненного цикла изображений:

* Создана новая ветка релиза, включающая в себя последний набор функций. Он опубликован как `2.0.0-beta.1`. ![](../images/versioning-sketch-3.png)
* Исправление ошибки входит в мастер, который может быть обращен в ветку выпуска. Патч применяется, и новая бета-версия опубликована как `2.0.0-beta.2`. ![](../images/versioning-sketch-4.png)
* The beta is considered _generally stable_ and it is published again as a non-beta under `2.0.0`. ![](../images/versioning-sketch-5.png)
* Позднее обнаруживается нулевой эксплойт и к мастеру применяется фиксация. Мы возвращаем исправление на линию `2-0-x` и релиз `2.0.1`. ![](../images/versioning-sketch-6.png)

Несколько примеров того, как различные семантические диапазоны собирают новые релизы:

![](../images/versioning-sketch-7.png)

# Отсутствующие возможности: Альпийский

Наша стратегия имеет несколько компромиссов, которые на сегодняшний день мы считаем подходящими. Самое важное, что новые возможности в master могут занять некоторое время до достижения стабильной линии выпуска. Если вы хотите сразу же попробовать новую функцию, вам придется создать Electron самостоятельно.

В качестве будущего рассмотрения мы можем представить одно или оба из нижеследующих:

* alpha releases that have looser stability constraints to betas; for example it would be allowable to admit new features while a stability channel is in _alpha_

# Функциональные флаги

Флаги свойств являются общей практикой в Chromium, и хорошо зарекомендовали себя в экосистеме веб-разработки. In the context of Electron, a feature flag or **soft branch** must have the following properties:

* включено/отключено либо во время выполнения, либо во время сборки; мы не поддерживаем концепцию флага функции с охватом запроса
* it completely segments new and old code paths; refactoring old code to support a new feature _violates_ the feature-flag contract
* флаги фиксации в конце концов удаляются после выхода функции

# Семантические коммиты

Мы стремимся к повышению четкости на всех уровнях процесса обновления и выпуска. Начиная с `2.0.0` , нам потребуется придерживаться спецификации [Обычные Коммиты](https://conventionalcommits.org/) для pull-запросов, которая может быть обобщена следующим образом:

* Commits that would result in a semver **major** bump must start their body with `BREAKING CHANGE:`.
* Commits that would result in a semver **minor** bump must start with `feat:`.
* Commits that would result in a semver **patch** bump must start with `fix:`.

* Мы разрешаем размывать коммиты при условии, что это сообщение приближается к указанному выше формату сообщения.
* Допустимо наличие некоторых фиксаций в pull-запросе, чтобы он не включал семантический префикс, до тех пор, пока заголовок Pull Request содержит значимое охватывающее семантическое сообщение.

# Версия `master`

- Ветка `master` всегда будет содержать следующую основную версию `X.0.0-nightly.DATE` в `package.json`
- Отпустить ветки никогда не сливаются с master
- Release branches _do_ contain the correct version in their `package.json`
- Как только выпускная ветка будет перерезана на основной, мастер должен быть доставлен к следующему основному.  I.e. `master` is always versioned as the next theoretical release branch