# Терминал и shell

> Терминал - основное рабочее место AI-инженера.

**Type:** Learn
**Languages:** --
**Prerequisites:** Фаза 0, Урок 01
**Time:** ~35 минут

## Цели обучения

- Использовать пайпы, редиректы и `grep` для обработки логов
- Работать с `tmux` для долгих задач и нескольких панелей
- Мониторить ресурсы через `htop`, `nvtop`, `nvidia-smi`
- Передавать файлы через SSH, `scp`, `rsync`

## Проблема

Тренировки, мониторинг GPU, SSH-сессии, управление окружениями - все проходит через shell. Медленно работаешь в терминале -> медленно работаешь везде.

## Концепция

`tmux` позволяет держать несколько задач в одной сессии и переподключаться без остановки обучения.

## Build It

### Шаг 1: Базовые команды

```bash
echo $SHELL
cd ~/projects/ai-engineering-from-scratch
pwd
ls -la
```

Горячие клавиши:
- `Ctrl+R` - поиск по истории
- `Ctrl+C` - прервать команду
- `Ctrl+L` - очистить экран

### Шаг 2: Пайпы и редиректы

```bash
cat train.log | grep "loss" | wc -l
grep "loss:" train.log | awk '{print $NF}' > losses.txt
tail -f train.log | grep --line-buffered "ERROR"
python train.py > output.log 2> errors.log
python train.py > train_full.log 2>&1
```

### Шаг 3: Фоновые процессы

```bash
python train.py &
nohup python train.py > train.log 2>&1 &
jobs
ps aux | grep train.py
kill $(pgrep -f "train.py")
```

### Шаг 4: tmux

```bash
tmux new -s training
tmux ls
tmux attach -t training
tmux kill-session -t training
```

Часто используемое внутри tmux:
- `Ctrl+B` затем `"` - split horizontal
- `Ctrl+B` затем `%` - split vertical
- `Ctrl+B` затем `d` - detach

### Шаг 5: Мониторинг

```bash
htop
nvtop
nvidia-smi
watch -n1 nvidia-smi
```

### Шаг 6: SSH и передача файлов

```bash
ssh user@gpu-box-ip
scp model.pt user@gpu-box-ip:~/models/
scp user@gpu-box-ip:~/results/metrics.json ./
rsync -avz ./data/ user@gpu-box-ip:~/data/
ssh -L 8888:localhost:8888 user@gpu-box-ip
```

### Шаг 7: Полезные alias

```bash
alias gpu='nvidia-smi --query-gpu=index,name,utilization.gpu,memory.used,memory.total,temperature.gpu --format=csv,noheader'
alias killtraining='pkill -f "python.*train"'
alias ae='source .venv/bin/activate'
```

## Use It

| Tool | Когда нужен |
|------|-------------|
| tmux | Любой долгий training run |
| `tail -f` + `grep` | Мониторинг логов |
| `htop` / `nvtop` | Поиск узких мест и OOM |
| SSH + `rsync` | Работа на удаленных GPU |

## Упражнения

1. Подними tmux-сессию с 3 панелями
2. Подключи alias из `code/shell_aliases.sh`
3. Создай fake log и извлеки loss через `grep`/`awk`
4. Настрой SSH-конфиг для сервера

## Ключевые термины

| Term | Что это |
|------|---------|
| Shell | Интерпретатор команд (bash/zsh) |
| tmux | Мультиплексор терминалов с detach/attach |
| Pipe | Передача вывода одной команды в другую (`|`) |
| PID | ID процесса |
| nohup | Запуск команды, живущий после закрытия терминала |
