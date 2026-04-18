# 📢 Telegram PR Notifications

[🇷🇺 Русский](#русский) | [🇬🇧 English](#english)

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

<a name="english"></a>

## 🇬🇧 English

A simple, fast, and pure-Bash GitHub Action to send flexible Telegram notifications for Pull Request events. **Zero dependencies, no Node.js required!**

### ✨ Features

- **Zero dependencies:** Runs instantly using pure Bash and `curl`.
- **Smart Mentioning:** Maps GitHub usernames to Telegram usernames.
- **Bot Ignoring:** Automatically ignores PRs and comments from bots (e.g., dependabot).
- **Thread Support:** Can send messages to specific topics in Telegram Supergroups.
- **i18n:** Supports English and Russian notification languages.
- **Safe:** Uses URL-encoding to prevent bash injections or format-breaking from PR titles.

### 🚀 Supported Events

- `opened` / `ready_for_review` (Draft PRs are ignored)
- `closed` (Distinguishes between _Merged_ and _Closed without merge_)
- `review_requested`
- `pull_request_review` (Approved, Changes Requested, Commented)

### 🛠 Usage

Create a workflow file in your repository, e.g., `.github/workflows/tg-notify.yml`:

```yaml
name: Telegram PR Notifier

on:
  pull_request:
    types: [opened, ready_for_review, closed, review_requested]
  pull_request_review:
    types: [submitted]

# Prevents race conditions and spam for the same action
concurrency:
  group: notify-${{ github.event.pull_request.number }}-${{ github.event.action }}-${{ github.event.requested_reviewer.login || github.event.review.id || github.run_id }}
  cancel-in-progress: false

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Send Notification
        uses: Val-d-emar/telegram-pr-notifications@main
        with:
          bot_token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat_id: ${{ secrets.TELEGRAM_CHAT_ID }}
          # message_thread_id: '12345' # Optional: for topic groups
          user_map: ${{ secrets.TELEGRAM_USER_MAP }}
          ignore_bots: "true"
          language: "en"
```

### ⚙️ Inputs

| Input               | Required | Default | Description                                        |
| ------------------- | -------- | ------- | -------------------------------------------------- |
| `bot_token`         | **Yes**  |         | Your Telegram Bot Token.                           |
| `chat_id`           | **Yes**  |         | Target chat ID (Group, Channel, or User).          |
| `message_thread_id` | No       |         | Target Thread ID (if your group uses Topics).      |
| `user_map`          | No       |         | Mapping of GitHub usernames to Telegram usernames. |
| `ignore_bots`       | No       | `true`  | Ignore actions triggered by `[bot]` accounts.      |
| `language`          | No       | `ru`    | Notification language (`en` or `ru`).              |

### 👥 User Mapping (`user_map`)

To ping correct Telegram users, create a GitHub Secret (e.g., `TELEGRAM_USER_MAP`) with the following format:

```text
github_username1=telegram_username1
github_username2=@telegram_username2
```

_Note: The `@` symbol for Telegram usernames is optional, the action will add it automatically if missing._

### License

## This project is licensed under the[GNU General Public License v3.0 (GPL-3.0)](LICENSE).

---

<a name="русский"></a>

## 🇷🇺 Русский

Простой и быстрый GitHub Action на чистом Bash для отправки гибких уведомлений о событиях в Pull Request'ах прямо в Telegram. **Никаких зависимостей и сборок на Node.js!**

### ✨ Особенности

- **Zero dependencies:** Работает мгновенно, использует только Bash и `curl`.
- **Умные упоминания:** Связывает никнеймы GitHub с аккаунтами в Telegram.
- **Игнорирование ботов:** Не спамит в чат от лица `dependabot` или других ботов.
- **Поддержка топиков (Threads):** Умеет отправлять сообщения в конкретные ветки супергрупп.
- **i18n:** Поддерживает русский и английский языки.
- **Безопасность:** Использует URL-encoding, поэтому эмодзи, кавычки и переносы строк в названии PR не сломают отправку сообщений.

### 🚀 Поддерживаемые события

- `opened` / `ready_for_review` (Черновики / Draft PR игнорируются при открытии)
- `closed` (Отличает успешный _Merge_ от обычного закрытия)
- `review_requested` (Запрос ревью)
- `pull_request_review` (Апрув, Запрос правок, Обычный комментарий)

### 🛠 Использование

Создайте файл в вашем репозитории, например `.github/workflows/tg-notify.yml`:

```yaml
name: Telegram PR Notifier

on:
  pull_request: types:[opened, ready_for_review, closed, review_requested]
  pull_request_review:
    types: [submitted]

# Защита от спама и "гонок" при дублирующихся событиях
concurrency:
  group: notify-${{ github.event.pull_request.number }}-${{ github.event.action }}-${{ github.event.requested_reviewer.login || github.event.review.id || github.run_id }}
  cancel-in-progress: false

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Send Notification
        uses: Val-d-emar/telegram-pr-notifications@main
        with:
          bot_token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat_id: ${{ secrets.TELEGRAM_CHAT_ID }}
          # message_thread_id: '12345' # Опционально: ID топика в форумах
          user_map: ${{ secrets.TELEGRAM_USER_MAP }}
          ignore_bots: "true"
          language: "ru"
```

### ⚙️ Параметры (Inputs)

| Параметр            | Обязательный | По умолчанию | Описание                                               |
| ------------------- | ------------ | ------------ | ------------------------------------------------------ |
| `bot_token`         | **Да**       |              | Токен вашего Telegram бота.                            |
| `chat_id`           | **Да**       |              | ID чата (группа, канал или ЛС).                        |
| `message_thread_id` | Нет          |              | ID топика (ветки), если группа в режиме форума.        |
| `user_map`          | Нет          |              | Маппинг никнеймов GitHub -> Telegram.                  |
| `ignore_bots`       | Нет          | `true`       | Не отправлять уведомления, если действие совершил бот. |
| `language`          | Нет          | `ru`         | Язык уведомлений (`en` или `ru`).                      |

### 👥 Маппинг пользователей (`user_map`)

Чтобы бот тегал нужных людей в Telegram, создайте секрет в GitHub (например, `TELEGRAM_USER_MAP`) в следующем формате:

```text
github_username1=telegram_username1
github_username2=@telegram_username2
```

_Примечание: Символ `@` перед ником в Telegram писать не обязательно, скрипт добавит его автоматически._

### Лицензия

Этот проект распространяется под лицензией [GNU General Public License v3.0 (GPL-3.0)](LICENSE).
