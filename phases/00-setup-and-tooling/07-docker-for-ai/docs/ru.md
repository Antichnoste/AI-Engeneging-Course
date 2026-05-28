# Docker для AI

> Контейнеры убирают проблему "у меня работает".

**Type:** Build
**Languages:** Docker
**Prerequisites:** Фаза 0, Уроки 01 и 03
**Time:** ~60 минут

## Цели обучения

- Собирать GPU-образ Docker с CUDA и PyTorch
- Подключать volume-монты для кода, моделей и данных
- Настраивать NVIDIA Container Toolkit для доступа к GPU из контейнера
- Поднимать multi-service стек (инференс + векторная БД) через Docker Compose

## Проблема

AI-стек обычно хрупкий: Python, PyTorch, CUDA, cuDNN, C-библиотеки, специфичные зависимости. На двух машинах окружение отличается - код ломается.

Docker упаковывает окружение в образ и делает запуск одинаковым везде.

## Концепция

| Term | Что это |
|------|---------|
| Image | Шаблон (рецепт) среды |
| Container | Запущенный экземпляр образа |
| Dockerfile | Инструкции сборки образа |
| Volume | Постоянное хранилище вне контейнера |
| docker-compose | Оркестрация нескольких сервисов |

## Build It

### Шаг 1: Установка Docker

```bash
# macOS
brew install --cask docker
open /Applications/Docker.app

# Ubuntu
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```

Проверка:

```bash
docker --version
docker run hello-world
```

### Шаг 2: NVIDIA Container Toolkit (Linux + NVIDIA)

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

Тест GPU:

```bash
docker run --rm --gpus all nvidia/cuda:12.4.1-base-ubuntu22.04 nvidia-smi
```

### Шаг 3: Сборка AI-образа

```bash
docker build -t ai-dev -f phases/00-setup-and-tooling/07-docker-for-ai/code/Dockerfile .
```

Запуск:

```bash
docker run --rm -it --gpus all \
    -v $(pwd):/workspace \
    -v ~/models:/models \
    ai-dev python -c "import torch; print(torch.__version__, torch.cuda.is_available())"
```

Jupyter в контейнере:

```bash
docker run --rm -it --gpus all \
    -v $(pwd):/workspace \
    -v ~/models:/models \
    -p 8888:8888 \
    ai-dev jupyter notebook --ip=0.0.0.0 --port=8888 --no-browser --allow-root
```

### Шаг 4: Docker Compose для multi-service

```bash
cd phases/00-setup-and-tooling/07-docker-for-ai/code
docker compose up -d
```

Остановка:

```bash
docker compose down
docker compose down -v
```

## Use It

- Поднимай окружение и сервисы одной командой
- Храни модели/данные в volumes, чтобы не терять при пересборке
- Добавляй зависимости в Dockerfile и пересобирай образ

## Упражнения

1. Собери образ и проверь импорт `torch` внутри контейнера
2. Подними compose-стек и проверь доступность Qdrant
3. Добавь новый пакет в Dockerfile, пересобери и запусти тест

## Ключевые термины

| Term | Что это |
|------|---------|
| Reproducibility | Повторяемость окружения и результатов |
| GPU passthrough | Передача доступа к GPU из хоста в контейнер |
| Volume mount | Связка папки хоста и контейнера |
| Compose network | Общая сеть сервисов в одном docker-compose проекте |
