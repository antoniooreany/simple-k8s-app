# Простое Kubernetes приложение (Frontend + Backend + Redis)

Это минимальный пример развертывания трехкомпонентного приложения в Kubernetes.

## Компоненты:

* **Frontend:** Nginx (слушает на порту 80)
* **Backend:** httpbin (слушает на порту 80, настроен на поиск Redis через `redis-service`)
* **Redis:** Redis (слушает на порту 6379)

## Как запустить:

1.  Убедитесь, что у вас установлен `kubectl` и настроен доступ к вашему Kubernetes кластеру (например, Minikube, Docker Desktop Kubernetes, Kind, или облачный кластер).
2.  Перейдите в директорию с этими файлами (`simple-k8s-app`).
3.  Примените все конфигурационные файлы:
    ```bash
    kubectl apply -f .
    ```
4.  Проверьте статус подов:
    ```bash
    kubectl get pods -l app=frontend
    kubectl get pods -l app=backend
    kubectl get pods -l app=redis
    ```
    Дождитесь, пока все поды перейдут в статус `Running`.
5.  Проверьте статус сервисов:
    ```bash
    kubectl get services
    ```
    Вы увидите `frontend-service` с типом `NodePort`.

## Доступ к приложению:

* **Если вы используете Minikube:**
    Выполните команду, чтобы получить URL для доступа к frontend:
    ```bash
    minikube service frontend-service --url
    ```
    Откройте полученный URL в браузере.

* **Если вы используете другой тип кластера (Docker Desktop, Kind, облачный):**
    a.  Узнайте IP-адрес одного из узлов вашего кластера. Для Docker Desktop/Kind это часто `localhost` или `127.0.0.1`. Для облачных провайдеров это будет внешний IP узла.
    b.  Узнайте порт (`NodePort`), который Kubernetes назначил для `frontend-service`:
        ```bash
        kubectl get service frontend-service -o jsonpath='{.spec.ports[0].nodePort}'
        ```
    c.  Откройте в браузере `http://<IP-адрес-узла>:<NodePort>`. Например: `http://localhost:31234` (если порт 31234).

Вы должны увидеть стандартную страницу приветствия Nginx.

## Важно:

* Это **максимально упрощенная** конфигурация.
* Данные в Redis **не сохраняются** при перезапуске пода Redis (нет Persistent Volumes).
* Нет настроек безопасности, ресурсов (limits/requests), readiness/liveness проб.
* Frontend Nginx не настроен для проксирования запросов к бэкенду. Это нужно добавить в конфигурацию Nginx, если требуется взаимодействие.
