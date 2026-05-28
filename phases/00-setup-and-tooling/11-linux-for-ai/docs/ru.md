# Linux для AI

> Большинство AI-задач запускается на Linux. Нужно уверенно владеть базой.

**Type:** Learn
**Languages:** --
**Prerequisites:** Фаза 0, Урок 01
**Time:** ~30 минут

## Цели обучения

- Навигация по файловой системе Linux и базовые файловые операции
- Управление правами (`chmod`, `chown`) и исправление Permission denied
- Установка пакетов через `apt` и подготовка GPU-сервера
- Понимание типичных отличий macOS/Windows от Linux

## Проблема

Как только ты подключаешься к облачному GPU, чаще всего это Ubuntu и терминал без GUI. Если не умеешь работать из командной строки, ты теряешь время и деньги.

## Файловая система

Главные директории:
- `/home/<user>` - твои файлы
- `/tmp` - временные файлы
- `/etc` - конфиги
- `/var/log` - логи
- `/mnt` или `/media` - подключенные диски

## Основные команды

```bash
pwd
ls -la
cd ~
cd ..
mkdir -p a/b/c
cp -r src/ backup/
mv old.txt new.txt
rm -rf my-dir/
cat file.txt
head -20 file.txt
tail -f log.txt
less file.txt
grep -r "cuda" .
find . -name "*.py"
```

## Права доступа

```bash
ls -l train.py
chmod +x train.sh
chmod 755 deploy.sh
chmod 644 config.yaml
chown user:group file.txt
```

## apt (Ubuntu)

```bash
sudo apt update
sudo apt install -y build-essential git curl wget tmux htop unzip python3-venv
apt list --installed
sudo apt remove htop
```

## Пользователи и sudo

```bash
whoami
sudo <command>
sudo su
```

## Процессы и сервисы

```bash
htop
ps aux | grep python
kill 12345
kill -9 12345
nvidia-smi
sudo systemctl status nginx
```

## Диск

```bash
df -h
du -sh *
du -sh ~/.cache
```

## Сеть и передача данных

```bash
wget https://example.com/model.bin
curl -O https://example.com/data.tar.gz
scp model.bin user@remote:/data/
rsync -avz --progress ./data/ user@remote:/data/
```

## tmux (обязательно для долгих запусков)

```bash
tmux new -s train
# Ctrl+B, d -> detach
tmux ls
tmux attach -t train
```

## WSL2 для Windows

```bash
# PowerShell (admin)
wsl --install -d Ubuntu-24.04
```

В WSL файлы Windows доступны как `/mnt/c/...`.

## Use It

Мини-шпаргалка:
- Навигация: `pwd`, `ls`, `cd`, `find`
- Файлы: `cp`, `mv`, `rm`, `mkdir`, `cat`
- Поиск: `grep`, `find`
- Права: `chmod`, `chown`, `sudo`
- Пакеты: `apt update`, `apt install`
- Процессы: `htop`, `ps`, `kill`, `nvidia-smi`
- Сессии: `tmux`

## Упражнения

1. Создай папку проекта и файлы через `touch`, проверь `ls -la`
2. Установи `htop` и найди процесс с максимальной памятью
3. Подними tmux-сессию, detach и reattach
4. Проверь, что занимает место в `~/.cache`
5. Передай файл через `scp`, затем через `rsync`
