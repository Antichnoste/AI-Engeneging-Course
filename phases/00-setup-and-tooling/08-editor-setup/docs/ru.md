# Настройка редактора

> Редактор - твой второй пилот. Настрой один раз, чтобы он ускорял работу, а не тормозил.

**Type:** Build
**Languages:** --
**Prerequisites:** Фаза 0, Урок 01
**Time:** ~20 минут

## Цели обучения

- Установить VS Code и ключевые расширения для Python/Jupyter/linting/Remote SSH
- Включить format-on-save, type checking и удобный вывод ноутбуков
- Настроить Remote SSH для работы с удаленными GPU как с локальной папкой
- Понять альтернативы (Cursor, Windsurf, Neovim)

## Проблема

Ты проведешь тысячи часов в редакторе. Плохая конфигурация = постоянное трение: нет автодополнения, подсказок типов, встроенной диагностики и удобного терминала.

## Build It

### Шаг 1: Установи VS Code

Скачай с [code.visualstudio.com](https://code.visualstudio.com/).

Проверка:

```bash
code --version
```

### Шаг 2: Поставь важные расширения

```bash
code --install-extension ms-python.python
code --install-extension ms-python.vscode-pylance
code --install-extension ms-toolsai.jupyter
code --install-extension eamodio.gitlens
code --install-extension ms-vscode-remote.remote-ssh
code --install-extension ms-python.debugpy
code --install-extension ms-python.black-formatter
code --install-extension charliermarsh.ruff
```

### Шаг 3: Настройки под AI-workflow

```jsonc
{
    "python.analysis.typeCheckingMode": "basic",
    "editor.formatOnSave": true,
    "editor.rulers": [88, 120],
    "notebook.output.scrolling": true,
    "files.autoSave": "afterDelay"
}
```

### Шаг 4: Интегрированный терминал

```jsonc
{
    "terminal.integrated.defaultProfile.osx": "zsh",
    "terminal.integrated.defaultProfile.linux": "bash",
    "terminal.integrated.fontSize": 13,
    "terminal.integrated.scrollback": 10000
}
```

### Шаг 5: Remote SSH

1. Установи расширение Remote SSH
2. `Ctrl+Shift+P` -> Remote-SSH: Connect to Host
3. Подключись к `user@your-gpu-box-ip`

SSH-ключи:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
ssh-copy-id user@your-gpu-box-ip
```

Пример `~/.ssh/config`:

```text
Host gpu-box
    HostName 203.0.113.50
    User ubuntu
    IdentityFile ~/.ssh/id_ed25519
    ForwardAgent yes
```

## Alternatives

- Cursor и Windsurf: VS Code-совместимые AI-first форки
- Neovim: подходит, если ты уже опытный пользователь

## Use It

Ежедневный цикл:
1. Открыть проект (локально или через Remote SSH)
2. Писать код с подсказками и диагностикой
3. Запускать ноутбуки и скрипты из терминала
4. Проверять изменения через GitLens

## Упражнения

1. Установи расширения из урока
2. Примени `settings.json`
3. Проверь, что Black форматирует при сохранении, а Pylance дает типы
4. Подключись к удаленной машине через Remote SSH

## Ключевые термины

| Term | Что это |
|------|---------|
| LSP | Протокол связи редактора с языковым сервером |
| Pylance | Языковой сервер Python для типов и IntelliSense |
| Remote SSH | Работа с удаленной машиной прямо из локального VS Code |
| Format on save | Автоформатирование файла при сохранении |
