yaml

---
title: "Gitflow и семантическое версионирование"
subtitle: "Изучаем профессиональный подход к работе с Git"
date: 2026-03-13
lastmod: 2026-03-13
authors:
  - "Ксения Борисова"
tags:
  - "git"
  - "gitflow"
  - "semver"
  - "devops"
categories:
  - "учебное"
summary: "Разбираюсь с Gitflow, Conventional Commits и SemVer на практике"
---

## Что такое Gitflow?

Gitflow — это популярная модель ветвления для Git, которая помогает организовать работу над проектом. Основная идея — использовать несколько постоянных веток и дополнительные временные для новых функций и исправлений.

### Основные ветки в Gitflow

В Gitflow используются две основные ветки, которые живут вечно:

- **master** — хранит официальную историю релизов. Каждому коммиту в master присваивается тег с номером версии.
- **develop** — ветка для разработки, куда сливаются все новые функции.

### Вспомогательные ветки

Кроме основных, есть временные ветки:

- **feature/*** — для разработки новых функций. Создаются от develop и сливаются обратно в develop.
- **release/*** — для подготовки релиза. Создаются от develop, сливаются в master и develop.
- **hotfix/*** — для срочных исправлений. Создаются от master, сливаются в master и develop.

## Установка git-flow на Fedora

```bash
# Подключаем репозиторий
sudo dnf copr enable elegos/gitflow

# Устанавливаем git-flow
sudo dnf install gitflow

Инициализация git-flow в проекте
bash

# В корне репозитория
git flow init

При инициализации префикс для тегов версий рекомендую установить в v.
Семантическое версионирование (SemVer)

SemVer — это стандарт версионирования, который помогает понять, насколько серьёзные изменения произошли в новой версии.

Версия задаётся в формате MAJOR.MINOR.PATCH:

    MAJOR — увеличиваем, когда сделаны несовместимые изменения API (ломаем обратную совместимость).

    MINOR — увеличиваем, когда добавляем новую функциональность, не ломая совместимость.

    PATCH — увеличиваем, когда делаем исправления багов, совместимые с предыдущими версиями.

Conventional Commits (Общепринятые коммиты)

Чтобы автоматически определять, какую версию увеличивать, нужны стандартизированные коммиты.
Структура коммита
text

<тип>(<область>): <описание>

[необязательное тело]

[необязательный нижний колонтитул]

Основные типы коммитов
Тип	Описание	Соответствие SemVer
feat	Новая функциональность	MINOR
fix	Исправление ошибки	PATCH
BREAKING CHANGE	Несовместимые изменения	MAJOR
docs	Изменения в документации	—
style	Форматирование, отступы	—
refactor	Рефакторинг кода	—
test	Добавление тестов	—
chore	Обновление вспомогательных инструментов	—
Установка инструментов для Conventional Commits
bash

# Устанавливаем Node.js (если не установлен)
sudo dnf install nodejs

# Устанавливаем pnpm
sudo dnf install pnpm

# Настраиваем pnpm
pnpm setup
source ~/.bashrc

# Устанавливаем commitizen (помощник для коммитов)
pnpm add -g commitizen

# Устанавливаем standard-changelog (генератор логов)
pnpm add -g standard-changelog

Настройка package.json

Создаём файл package.json в корне проекта:
json

{
    "name": "git-extended",
    "version": "1.0.0",
    "description": "Git repo for educational purposes",
    "main": "index.js",
    "repository": "git@github.com:kmborisova/git-extended.git",
    "author": "Kseniia Borisova <kmborisova@example.com>",
    "license": "CC-BY-4.0",
    "config": {
        "commitizen": {
            "path": "cz-conventional-changelog"
        }
    }    
}

Рабочий процесс с git-flow
1. Создание релиза 1.0.0
bash

# Создаём релизную ветку
git flow release start 1.0.0

# Генерируем changelog
standard-changelog --first-release

# Добавляем changelog в коммит
git add CHANGELOG.md
git cz   # или git commit -am 'chore(site): add changelog'

# Завершаем релиз (сливает в master и develop)
git flow release finish 1.0.0

# Отправляем всё на GitHub
git push --all
git push --tags

2. Разработка новой функциональности
bash

# Создаём ветку для новой функции
git flow feature start feature-branch

# Работаем, делаем коммиты через git cz
git cz

# Завершаем разработку (сливает в develop)
git flow feature finish feature-branch

3. Создание следующего релиза (1.2.3)
bash

# Создаём релизную ветку
git flow release start 1.2.3

# Обновляем версию в package.json (меняем на 1.2.3)

# Генерируем changelog
standard-changelog

# Добавляем changelog
git add CHANGELOG.md
git cz   # или git commit -am 'chore(site): update changelog'

# Завершаем релиз
git flow release finish 1.2.3

# Отправляем
git push --all
git push --tags

4. Создание релиза на GitHub
bash

# Устанавливаем GitHub CLI (если не установлен)
sudo dnf install gh

# Создаём релиз с описанием из CHANGELOG.md
gh release create v1.2.3 -F CHANGELOG.md
