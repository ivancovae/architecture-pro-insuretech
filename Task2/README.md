## Настройка окружения

```bash
# Запуск Minikube
minikube delete; # на случай ошибок
minikube start --driver=docker --insecure-registry="host.minikube.internal:5000"

# Включение metrics-server
minikube addons enable metrics-server

# Проверка статуса
kubectl get pods -n kube-system | grep metrics-server

# Установка namespace
kubectl create namespace monitoring
```

## Установка Prometheus

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Установка Prometheus Stack
helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --set prometheus.prometheusSpec.serviceMonitorSelectorNilUsesHelmValues=false \
  --set prometheus.prometheusSpec.podMonitorSelectorNilUsesHelmValues=false
```

## Установка Prometheus Adapter

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus-adapter prometheus-community/prometheus-adapter \
  --namespace monitoring \
  --values adapter-values.yaml
```

## Деплой

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

```bash
# Применение ServiceMonitor для сбора метрик
kubectl apply -f servicemonitor.yaml
```

```bash
kubectl apply -f hpa-rps.yaml

kubectl port-forward service/scaletestapp-service 8080:8080
```

```bash
python -m locust -f locustfile.py
```