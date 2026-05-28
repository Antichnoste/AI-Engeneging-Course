# Отладка и профилирование

> Самые опасные баги в AI не падают с ошибкой: обучение идет, а модель учится на мусоре.

**Type:** Build
**Language:** Python
**Prerequisites:** Урок 1 (Dev Environment), базовый PyTorch
**Time:** ~60 минут

## Цели обучения

- Использовать `breakpoint()` и `debug_print` для проверки shape/dtype/NaN
- Профилировать код через `cProfile`, `line_profiler`, `tracemalloc`
- Ловить типовые AI-баги: shape mismatch, NaN loss, data leakage, wrong-device tensors
- Настроить TensorBoard для контроля динамики обучения

## Проблема

В AI код часто не падает сразу: он может часами обучаться неверно из-за тихих ошибок. Нужны инструменты, которые ловят такие сбои рано.

## Концепция

3 уровня отладки:
1. Обычный Python (breakpoints, логирование, профилирование)
2. Тензорные операции (shape, dtype, device, NaN/Inf)
3. Динамика обучения (loss, градиенты, активации)

## Build It

### 1) Print-debugging

```python
def debug_print(name, tensor):
    print(f"{name}: shape={tensor.shape}, dtype={tensor.dtype}, "
          f"device={tensor.device}, "
          f"min={tensor.min().item():.4f}, max={tensor.max().item():.4f}, "
          f"mean={tensor.mean().item():.4f}, "
          f"has_nan={tensor.isnan().any().item()}")
```

### 2) breakpoint / pdb

```python
if loss.item() > 100 or torch.isnan(loss):
    breakpoint()
```

Полезные команды: `p outputs.shape`, `p loss.item()`, `c`, `q`.

### 3) Logging

```python
import logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)
logger.info("training started")
```

### 4) Тайминг

```python
import time
start = time.perf_counter()
# блок кода
print(time.perf_counter() - start)
```

### 5) cProfile / line_profiler

```bash
python -m cProfile -s cumtime train.py
pip install line_profiler
# kernprof -l -v train.py
```

### 6) Память

CPU:

```python
import tracemalloc
tracemalloc.start()
snapshot = tracemalloc.take_snapshot()
print(snapshot.statistics("lineno")[:10])
```

GPU:

```python
print(torch.cuda.memory_summary())
print(torch.cuda.memory_allocated())
print(torch.cuda.memory_reserved())
```

### 7) Частые AI-баги

- Shape mismatch
- NaN loss
- Data leakage
- Тензоры на разных устройствах (CPU/GPU)

### 8) TensorBoard

```bash
pip install tensorboard
tensorboard --logdir=runs
```

```python
from torch.utils.tensorboard import SummaryWriter
writer = SummaryWriter("runs/experiment_1")
writer.add_scalar("loss/train", loss.item(), step)
writer.close()
```

### 9) VS Code Debugger

Используй `launch.json` с `debugpy`, breakpoint в нужной точке и Debug Console для интерактивных проверок.

## Use It

Практический workflow:
1. До тренировки - проверить shape на одном батче
2. Первые шаги - печатать ключевые тензоры и loss
3. Во время обучения - логирование + TensorBoard
4. При сбое - `breakpoint()` и проверка значений
5. При медленной работе - тайминг и профилирование

## Ship It

```bash
python phases/00-setup-and-tooling/12-debugging-and-profiling/code/debug_tools.py
```

См. `outputs/prompt-debug-ai-code.md` для шаблона диагностики.

## Упражнения

1. Запусти `debug_tools.py` и добавь искусственный NaN
2. Профилируй training loop через `cProfile`
3. Найди строку с максимальным потреблением памяти через `tracemalloc`
4. Подними TensorBoard и оцени переобучение
5. Попрактикуй `breakpoint()` внутри train-цикла
