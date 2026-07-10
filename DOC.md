# ImpactLens: пользовательская документация

ImpactLens - расширение для VS Code и совместимых IDE на базе VS Code, которое
показывает влияние локальных изменений или pull request на проект: затронутые
файлы, маршруты FastAPI, функции, классы, методы и тесты, которые стоит
запустить.

Главная идея: перед ревью или мерджем быстро понять, что может сломаться, какие
проверки релевантны и где нужен дополнительный ручной контроль.

## Что умеет ImpactLens

- Анализирует текущие Git-изменения в рабочей области.
- Анализирует pull request, если доступна информация о PR и ветках.
- Показывает список измененных файлов.
- Строит граф влияния между файлами и символами.
- Находит затронутые FastAPI routes.
- Показывает затронутые Python-символы: функции, классы, методы.
- Предлагает релевантные тесты.
- Позволяет запускать предложенные тесты прямо из отчета.
- Показывает прогресс анализа и позволяет отменить анализ.
- Поддерживает уровни глубины анализа.
- Поддерживает исключения для больших или сгенерированных директорий.
- Дает AI-summary, AI-test-plan и AI-fix-plan через OpenAI-compatible provider.
- Работает локально: основной статический анализ не использует LLM.

## Поддерживаемые языки

Самая глубокая поддержка сейчас у Python и FastAPI.

MVP-level поддержка есть для:

- Python;
- TypeScript;
- JavaScript;
- Go;
- Java;
- C#;
- C/C++.

Для Python ImpactLens умеет извлекать импорты, классы, функции, методы,
pytest-тесты и FastAPI routes. Для остальных языков поддержка проще: анализ
импортов, include/use-связей, файлов тестов и базовых символов.

## Установка из VSIX

1. Получите файл `impactlens-vscode-0.1.0.vsix`.
2. Откройте VS Code или Kodik.
3. Откройте `Extensions`.
4. Нажмите `...`.
5. Выберите `Install from VSIX...`.
6. Укажите файл `impactlens-vscode-0.1.0.vsix`.
7. Перезапустите IDE, если это потребуется.

Альтернативно из PowerShell:

```powershell
code --install-extension .\impactlens-vscode-0.1.0.vsix --force
```

Для Kodik путь к команде может отличаться. Если `kodik` есть в PATH:

```powershell
kodik --install-extension .\impactlens-vscode-0.1.0.vsix --force
```

## Первый запуск

1. Откройте проект в VS Code или Kodik.
2. Нажмите на иконку ImpactLens в Activity Bar слева.
3. Нажмите кнопку `Getting Started` в верхней части панели ImpactLens.
4. Нажмите `Check setup`.
5. Проверьте, что расширение видит:
   - рабочую область;
   - Python;
   - встроенный analyzer bundle;
   - Git repository;
   - PR support, если нужен анализ PR;
   - AI provider, если нужны AI-рекомендации.

Если Python не найден, укажите путь в настройке `impactlens.pythonPath`.

## Основные элементы интерфейса

### Activity Bar

После установки слева появляется иконка ImpactLens. Она открывает боковую
панель расширения.

### Боковая панель ImpactLens

В панели доступны:

- `Getting Started` - быстрый старт и проверка окружения.
- `Current Changes` - текущие локальные Git-изменения.
- список измененных файлов;
- `Pull Requests` - открытые PR, если доступна GitHub-интеграция;
- кнопка обновления;
- кнопка настроек.

Клик по элементу не запускает анализ сразу. ImpactLens сначала показывает меню
действий, чтобы пользователь явно выбрал, что сделать.

### Контекстное меню

Можно нажать правой кнопкой мыши на:

- `Current Changes`;
- отдельный измененный файл;
- pull request.

В контекстном меню доступны действия анализа, открытия, копирования и просмотра.

## Анализ текущих изменений

1. Откройте проект с Git-изменениями.
2. Откройте панель ImpactLens.
3. В разделе `Current Changes` выберите нужное действие.
4. Выберите профиль анализа:
   - `Fast check`;
   - `Normal analysis`;
   - `Deep analysis`.
5. Дождитесь завершения анализа.
6. Изучите отчет.

### Fast, Normal и Deep

`Fast check` анализирует минимальный радиус влияния и подходит для быстрого
первого взгляда.

`Normal analysis` использует глубину из настройки
`impactlens.analysis.defaultDepth`.

`Deep analysis` проходит все доступные уровни зависимостей. На больших проектах
он может работать заметно дольше и возвращать много результатов.

## Глубина анализа

Глубина определяет, насколько далеко ImpactLens проходит по графу зависимостей.

Доступные значения:

- `1` - только напрямую затронутые файлы;
- `2` - прямые зависимости и один уровень косвенного влияния;
- `3` - три уровня влияния;
- `all` - все найденные уровни.

Для больших проектов обычно разумно начинать с `1` или `2`.

Настройка:

```json
{
  "impactlens.analysis.defaultDepth": "2"
}
```

## Отчет анализа

Отчет открывается в отдельной вкладке и содержит несколько блоков.

### Summary

Краткая сводка:

- сколько файлов изменено;
- сколько файлов затронуто;
- сколько routes найдено;
- сколько символов затронуто;
- сколько тестов предложено;
- какой уровень риска и уверенности получился.

### Analysis quality

Показывает ограничения результата:

- были ли пропущенные файлы;
- есть ли unsupported files;
- сработали ли лимиты на символы или тесты;
- стоит ли уменьшить глубину или добавить exclude-паттерны.

### Modified files

Список файлов, которые входят в анализируемый change set или PR diff.

### Affected routes

Список FastAPI routes, которые могут быть затронуты изменением.

Обычно строка содержит:

- HTTP method;
- route path;
- handler;
- файл и строку;
- причину попадания в отчет;
- кнопку открытия файла.

### Affected symbols

Список затронутых символов:

- функции;
- классы;
- методы.

Для каждого символа показывается имя, файл, строка и причина.

### Suggested tests

Список тестов, которые ImpactLens считает релевантными для текущего изменения.

Доступные действия:

- `Open` - открыть тестовый файл;
- `Run` - запустить отдельный тест;
- `Run all suggested tests` - запустить все предложенные тесты.

## Запуск тестов из отчета

ImpactLens может запускать предложенные тесты прямо из отчета.

Для одного теста:

1. Найдите тест в `Suggested tests`.
2. Нажмите `Run`.
3. Дождитесь результата.

Для всех тестов:

1. Нажмите `Run all suggested tests`.
2. Дождитесь завершения.
3. Изучите статусы и вывод.

Результаты запуска используются не только для пользователя, но и для AI fix
plan: без `Run all suggested tests` кнопка `Fix failed tests` не будет
генерировать рекомендации по починке.

## AI Suggestions

AI-функции необязательны. Основной impact analysis работает без AI.

LLM используется только для человекочитаемых объяснений и рекомендаций. В AI
отправляются компактные факты анализа, а не весь исходный код проекта.

В блоке `AI Suggestions` доступны:

- `Generate review summary`;
- `Generate test plan`;
- `Fix failed tests`;
- кнопка настройки AI provider.

### Generate review summary

Генерирует краткое ревью по найденным фактам:

- summary;
- risks;
- manual checks;
- test suggestions.

Эта функция полезна для подготовки PR-описания или ревью-комментария.

### Generate test plan

Генерирует план проверки изменения на основе статического анализа:

- какие области проверить;
- какие тесты запустить;
- какие ручные проверки стоит сделать.

### Fix failed tests

Генерирует план починки упавших тестов.

Важно: кнопка работает только после нажатия `Run all suggested tests`.

Если тесты еще не запускались, ImpactLens покажет сообщение:

```text
Run all suggested tests before generating failed test fix recommendations.
```

После запуска всех тестов ImpactLens отправляет в LLM:

- факты статического анализа;
- список предложенных тестов;
- команду запуска каждого теста;
- статус теста;
- exit code;
- обрезанный stdout/stderr.

LLM возвращает:

- `Fix plan` - что исправить;
- `Likely causes` - вероятные причины падения;
- `Validation` - чем подтвердить исправление;
- `Why this plan` - почему план связан с изменением.

Если все тесты прошли, LLM должна объяснить, что падений нет, и предложить
дополнительную валидацию только при необходимости.

## Настройка AI provider

ImpactLens поддерживает OpenAI-compatible chat completions.

Минимальные настройки:

```json
{
  "impactlens.ai.baseUrl": "https://api.openai.com/v1",
  "impactlens.ai.model": "gpt-4.1-mini"
}
```

Ключ можно указать напрямую:

```json
{
  "impactlens.ai.apiKey": "sk-..."
}
```

Или через переменную окружения:

```json
{
  "impactlens.ai.apiKey": "",
  "impactlens.ai.apiKeyEnvVar": "OPENAI_API_KEY"
}
```

После изменения системных переменных окружения перезапустите VS Code или Kodik.
Редактор получает переменные окружения при старте процесса.

### Проверка переменной окружения

В PowerShell:

```powershell
$env:OPENAI_API_KEY
```

Если команда ничего не выводит, текущий процесс PowerShell не видит переменную.

Для временной установки в текущей PowerShell-сессии:

```powershell
$env:OPENAI_API_KEY = "your-api-key"
```

Для постоянной пользовательской переменной Windows:

```powershell
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "your-api-key", "User")
```

После этого перезапустите IDE.

## Kodik и AI

ImpactLens может работать в Kodik как VS Code-compatible расширение.

На данный момент надежный путь для AI - OpenAI-compatible provider через
настройки ImpactLens.

Экспериментальная настройка:

```json
{
  "impactlens.ai.experimentalKodikBridge": true
}
```

Она предназначена только для случаев, когда в текущем Extension Host доступна
подходящая внутренняя команда Kodik. Если команда недоступна, используйте
обычный OpenAI-compatible provider.

## Анализ pull request

ImpactLens умеет анализировать PR без установки GitHub App.

Источники данных:

- VS Code GitHub authentication;
- GitHub CLI (`gh`);
- локальные Git refs и ветки.

Если вы уже вошли в GitHub внутри VS Code, расширение может использовать
встроенную авторизацию VS Code. Если нет, оно попробует использовать `gh`.

Проверка GitHub CLI:

```powershell
gh auth status
gh pr list
```

Если PR branch недоступна локально, ImpactLens может предложить fetch PR branch
и продолжить анализ.

### Base branch

ImpactLens пытается определить base branch автоматически:

- из PR metadata;
- из `origin/HEAD`;
- из распространенных имен веток вроде `main`, `master`, `develop`, `dev`.

Если автоопределение ошибается, задайте явно:

```json
{
  "impactlens.git.baseBranch": "develop"
}
```

## Настройки

Открыть настройки можно:

- через боковую панель ImpactLens;
- через `ImpactLens: Settings`;
- через стандартные настройки VS Code.

### Runtime

```json
{
  "impactlens.pythonPath": "C:\\Path\\To\\python.exe"
}
```

Если поле пустое, ImpactLens ищет Python автоматически:

1. `impactlens.pythonPath`;
2. `.venv` в workspace;
3. `py -3`;
4. `python`;
5. `python3`.

### Git & Pull Requests

```json
{
  "impactlens.git.baseBranch": "develop"
}
```

Оставьте пустым, если автоопределение работает корректно.

### Analysis

```json
{
  "impactlens.analysis.defaultDepth": "2",
  "impactlens.analysis.exclude": [
    "generated/**",
    "**/migrations/**",
    "**/*_ui.py"
  ],
  "impactlens.analysis.maxAffectedSymbols": 1000,
  "impactlens.analysis.maxSuggestedTests": 100
}
```

`exclude` полезен для больших проектов, generated-файлов, UI-generated Python
файлов, миграций и директорий, которые не стоит анализировать.

`maxAffectedSymbols` и `maxSuggestedTests` ограничивают размер отчета, но не
скрывают общий счетчик найденных результатов.

### AI Provider

```json
{
  "impactlens.ai.baseUrl": "https://api.openai.com/v1",
  "impactlens.ai.apiKey": "",
  "impactlens.ai.apiKeyEnvVar": "OPENAI_API_KEY",
  "impactlens.ai.model": "gpt-4.1-mini",
  "impactlens.ai.experimentalKodikBridge": false
}
```

Если `apiKey` пустой, ImpactLens попробует взять ключ из переменной окружения,
имя которой указано в `apiKeyEnvVar`.

## Рекомендуемый рабочий процесс

### Для локальной правки

1. Сделайте изменение в коде.
2. Откройте ImpactLens.
3. Выберите `Current Changes`.
4. Запустите `Fast check`.
5. Если результат слишком узкий, запустите `Normal analysis`.
6. Посмотрите affected routes, symbols и suggested tests.
7. Запустите `Run all suggested tests`.
8. Если тесты упали, нажмите `Fix failed tests`.
9. Внесите правки вручную или передайте fix plan AI-агенту.
10. Повторно запустите suggested tests.
11. Используйте AI review summary как основу для PR-описания или ревью.

### Для PR

1. Откройте workspace с репозиторием.
2. Убедитесь, что GitHub auth или `gh` доступны.
3. Откройте ImpactLens.
4. Найдите PR в списке.
5. Выберите preview, если хотите сначала увидеть файлы.
6. Запустите анализ PR.
7. Изучите отчет.
8. Запустите suggested tests.
9. При необходимости сгенерируйте AI summary или fix plan.

## Как читать результат

ImpactLens не утверждает, что нашел абсолютно все последствия изменения. Это
локальный статический анализатор MVP-уровня. Его задача - быстро подсветить
вероятные зоны риска и полезные проверки.

Сильный сигнал:

- изменен shared model/schema;
- затронут API route;
- найден связанный тест;
- affected file находится в runtime path;
- review gate показывает высокий риск.

Слабый или неполный сигнал:

- много generated-файлов;
- есть unsupported language files;
- проект использует динамические импорты;
- зависимости создаются через reflection/metaprogramming;
- тесты называются нестандартно.

## Производительность

Если анализ идет долго:

1. Используйте `Fast check`.
2. Уменьшите `impactlens.analysis.defaultDepth` до `1`.
3. Добавьте generated-директории в `impactlens.analysis.exclude`.
4. Уменьшите `maxAffectedSymbols` и `maxSuggestedTests`.
5. Избегайте `Deep analysis` на больших проектах без необходимости.

Пример exclude:

```json
{
  "impactlens.analysis.exclude": [
    "**/.pytest_cache/**",
    "**/__pycache__/**",
    "**/node_modules/**",
    "**/dist/**",
    "**/build/**",
    "**/migrations/**",
    "**/*_ui.py"
  ]
}
```

## Типовые проблемы

### Python не найден

Откройте `ImpactLens: Check Setup`.

Если Python не найден, задайте:

```json
{
  "impactlens.pythonPath": "C:\\Users\\you\\project\\.venv\\Scripts\\python.exe"
}
```

### PR support: not configured

Это означает, что ImpactLens не получил доступ к GitHub PR metadata.

Проверьте:

```powershell
gh auth status
gh pr list
```

Или войдите в GitHub через VS Code accounts.

### AI поля пустые, но хочется использовать AI

Заполните:

- `impactlens.ai.baseUrl`;
- `impactlens.ai.model`;
- `impactlens.ai.apiKey` или `impactlens.ai.apiKeyEnvVar`.

Если ключ хранится в переменной окружения, убедитесь, что IDE была запущена
после создания переменной.

### AI пишет, что provider недоступен

Проверьте:

- base URL;
- model;
- API key;
- доступность сети;
- совместимость provider с OpenAI chat completions.

### `Fix failed tests` не работает

Сначала нажмите `Run all suggested tests`.

Кнопка специально заблокирована до полного запуска suggested tests, потому что
LLM нужен реальный вывод тестов, а не догадка.

### Отчет пустой

Проверьте:

- есть ли Git changes;
- поддерживается ли язык файла;
- не исключен ли файл через `impactlens.analysis.exclude`;
- не является ли изменение whitespace-only;
- правильно ли выбран PR/base branch.

Попробуйте `Normal analysis` или `Deep analysis`.

### Слишком много affected files

Уменьшите depth и добавьте exclude-паттерны.

На больших проектах нормальная стратегия - сначала depth `1`, затем `2`, и
только потом `all`, если нужен полный радиус.

## Демонстрационный сценарий

Хороший демо-поток:

1. Открыть проект.
2. Изменить модель или схему, например добавить поле в `Event`.
3. Показать `Current Changes`.
4. Запустить `Analyze Impact`.
5. Показать affected route, affected symbols и suggested tests.
6. Сгенерировать `Generate review summary`.
7. Нажать `Run all suggested tests`.
8. Если тест упал, нажать `Fix failed tests`.
9. По fix plan внести минимальную правку.
10. Повторно запустить suggested tests.
11. Показать, что релевантные тесты проходят.

Ключевой тезис демо:

```text
ImpactLens превращает diff в список рисков, проверок и следующих действий.
```

## Ограничения MVP

ImpactLens не является production-grade платформой и намеренно не включает:

- авторизацию пользователей;
- биллинг;
- GitHub App;
- реальные PR comments;
- базу данных;
- Celery/Kafka;
- Kubernetes;
- multi-user workspaces.

Расширение работает локально и ориентировано на быстрый инженерный workflow
внутри IDE.

## Для разработчиков проекта

Основной код расширения находится в `extension/`.

Сборка VSIX:

```powershell
cd extension
pnpm install
pnpm run package:vsix
```

Проверки:

```powershell
cd extension
pnpm run build
```

Analyzer и backend:

```powershell
pytest
```

Legacy frontend:

```powershell
cd frontend
npm run build
```

## Короткая памятка

Для обычного использования:

1. Откройте проект.
2. Откройте ImpactLens.
3. Нажмите `Check setup`.
4. Запустите анализ `Current Changes` или PR.
5. Посмотрите affected routes/symbols/tests.
6. Запустите `Run all suggested tests`.
7. Используйте AI suggestions при необходимости.
8. Если тесты упали, нажмите `Fix failed tests`.

