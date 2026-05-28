# Настройка GPU и облака

> Учиться можно и на CPU. Но для реального обучения моделей нужен GPU.

**Type:** Build
**Languages:** Python
**Prerequisites:** Фаза 0, Урок 01
**Time:** ~45 минут

## Цели обучения

- Проверить наличие локального GPU через `nvidia-smi` и CUDA API в PyTorch
- Настроить Google Colab с T4 для бесплатных облачных экспериментов
- Сравнить скорость умножения матриц на CPU и GPU
- Оценить максимальный размер модели по доступной VRAM (правило fp16)

## Проблема

Большинство уроков фаз 1-3 хорошо идут на CPU. Но при обучении CNN, трансформеров и LLM (фазы 4+) нужна GPU-акселерация. То, что на CPU занимает 8 часов, на GPU может занять 10 минут.

У тебя три варианта: локальный GPU, облачный GPU или Google Colab (бесплатно).

## Концепция

```text
Твои варианты:

1. Локальный NVIDIA GPU
   Стоимость: $0 (уже есть)
   Настройка: CUDA + cuDNN
   Лучше всего: регулярная работа, большие датасеты

2. Google Colab (бесплатный тариф)
   Стоимость: $0
   Настройка: не нужна
   Лучше всего: быстрые эксперименты, если дома нет GPU

3. Облачный GPU (Lambda, RunPod, Vast.ai)
   Стоимость: $0.20-2.00/час
   Настройка: SSH + установка пакетов
   Лучше всего: серьезное обучение, крупные модели
```

## Build It

### Вариант 1: Локальный NVIDIA GPU

Проверь, есть ли он:

```bash
nvidia-smi
```

Проверь PyTorch с CUDA:

```python
import torch

print(f"CUDA available: {torch.cuda.is_available()}")
print(f"CUDA version: {torch.version.cuda}")
if torch.cuda.is_available():
    print(f"GPU: {torch.cuda.get_device_name(0)}")
    print(f"Memory: {torch.cuda.get_device_properties(0).total_memory / 1e9:.1f} GB")
```

### Вариант 2: Google Colab

1. Открой [colab.research.google.com](https://colab.research.google.com)
2. Runtime > Change runtime type > T4 GPU
3. Запусти `!nvidia-smi` для проверки

Можно загружать ноутбуки курса напрямую в Colab.

### Вариант 3: Облачный GPU

Для Lambda Labs, RunPod или Vast.ai:

```bash
ssh user@your-gpu-instance

pip install torch torchvision torchaudio
python -c "import torch; print(torch.cuda.get_device_name(0))"
```

### Нет GPU? Не проблема

Большинство уроков работает на CPU. Где нужен GPU, в уроках будет указано и даны ссылки на Colab.

```python
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
print(f"Using: {device}")
```

## Build It: бенчмарк GPU против CPU

```python
import torch
import time

size = 5000

a_cpu = torch.randn(size, size)
b_cpu = torch.randn(size, size)

start = time.time()
c_cpu = a_cpu @ b_cpu
cpu_time = time.time() - start
print(f"CPU: {cpu_time:.3f}s")

if torch.cuda.is_available():
    a_gpu = a_cpu.to("cuda")
    b_gpu = b_cpu.to("cuda")

    torch.cuda.synchronize()
    start = time.time()
    c_gpu = a_gpu @ b_gpu
    torch.cuda.synchronize()
    gpu_time = time.time() - start
    print(f"GPU: {gpu_time:.3f}s")
    print(f"Speedup: {cpu_time / gpu_time:.0f}x")
```

## Упражнения

1. Запусти бенчмарк и сравни время CPU и GPU
2. Если GPU нет, запусти тот же тест в Google Colab
3. Узнай объем своей VRAM и оцени максимальный размер модели (fp16: примерно 2 байта на параметр)

## Ключевые термины

| Term | Как обычно говорят | Что это на самом деле |
|------|--------------------|------------------------|
| CUDA | "Программирование для GPU" | Платформа NVIDIA для параллельных вычислений на GPU |
| VRAM | "Память GPU" | Видеопамять GPU, отдельная от RAM; ограничивает размер модели |
| fp16 | "Половинная точность" | 16-битные числа, вдвое меньше памяти, чем fp32 |
| Tensor Core | "Быстрые матричные ядра" | Специализированные ядра GPU для матричных операций |
