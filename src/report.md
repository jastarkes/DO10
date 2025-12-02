## 1. Использование готового манифеста

Для начала установил кубер на школьный мак через ```brew install minikube``` и линканул в goinfre ```mkdir -p ~/goinfre/minikube``` ```ln -sf ~/goinfre/minikube ~/.minikube```

![alt text](screens/1.png "k8s")

Поднял систему с драйвером докера, так как vbox на macOS 13+ не поддерживается кубером.

![alt text](screens/2.png "k8s2")

Применил тестовый манифест.

![alt text](screens/3.png "k8s3")

Запустил Dashboard ```minikube dashboard```

![alt text](screens/4.png "k8s4")

Создаем туннели для каждого сервиса в отдельном терминале и проверяем страницу apache.
```minikube service apache --url```
```minikube service catalog --url```
```minikube service customer --url```
```minikube service order --url```

![alt text](screens/5.png "k8s5")

Список туннелей.

## 2. Написание собственного манифеста

Начинаем писать yaml файлы сервисов.

![alt text](screens/6.png "k8s6")

Кодируем логин, пароль и ключи для файла секретов.
После написания файлов yaml копируем докерфайлы для сервисов из проекта 7 и собираем их ```docker build -t payment-service:latest .```

![alt text](screens/7.png "k8s7")

Применяем каждый манифест по порядку.
Кубер не мог найти образы, собранные локально, поэтому загружаем все образы в minikube ```minikube image load session-service:latest``` и переключаемся на docker daemon minicube ```eval $(minikube docker-env)```

![alt text](screens/8.png "k8s8")
![alt text](screens/9.png "k8s9")
![alt text](screens/10.png "k8s10")
![alt text](screens/11.png "k8s11")
![alt text](screens/12.png "k8s12")
![alt text](screens/13.png "k8s13")

Проверяем статус созданных объектов командами ```kubectl get и kubectl describe```

![alt text](screens/14.png "k8s14")

Декодируем секреты для проверки наличия правильных значений.

![alt text](screens/15.png "k8s15")

Проверяем логи приложения.

![alt text](screens/16.png "k8s16")

Прокинут порт на session-service.

![alt text](screens/17.png "k8s17")

Меняем порт и хост и проверяем сервисы в Postman.

![alt text](screens/18.png "k8s18")
![alt text](screens/19.png "k8s19")
![alt text](screens/20.png "k8s20")
![alt text](screens/21.png "k8s21")

Открываем  ```manifest dashboard``` и смотрим информацию.

![alt text](screens/22.png "k8s22")
![alt text](screens/23.png "k8s23")
![alt text](screens/24.png "k8s24")
![alt text](screens/25.png "k8s25")
![alt text](screens/26.png "k8s26")

Смотрим конфигурации и секреты.

Далее добавляем в pom файл зависимость Spring Boot Actuator и создаем yaml для стратегии recreate на примере session-service. Собираем новую версию образа ```docker build -t session-service:v2 .```

![alt text](screens/27.png "k8s27")

Пересборка session-service с замером времени.

Для rolling update также создаем новый манимфест и зависимость SB Starter Validation. Запускаем сборку ```docker build -t session-service:v3 .```

![alt text](screens/28.png "k8s28")

Последовательное обновление с замером времени.