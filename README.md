# Дипломная работа к курсу Системный администратор - `Алифанов Сергей`

## Задача
Ключевая задача — разработать отказоустойчивую инфраструктуру для сайта, включающую мониторинг, сбор логов и резервное копирование основных данных. Инфраструктура должна размещаться в [Yandex Cloud](https://cloud.yandex.com/) и отвечать минимальным стандартам безопасности: запрещается выкладывать токен от облака в git. Используйте [инструкцию](https://cloud.yandex.ru/docs/tutorials/infrastructure-management/terraform-quickstart#get-credentials).

### Пошаговое выполнение

1. Подготовив и протестировав на работоспособность инфраструктуру, первым делом добавляем в исключения gitignore файл yc_provider.tf, чтобы соответствовать требованию безопасности. Далее выполняем требования по мере развертывания инфраструктуры.

2. Файлы терраформа размещены в настоящем репозитории, а файлы сервисов размещены в отдельном репозитории  [config_and_install](https://github.com/Adrenokrome72/config_and_install).

3. Первым делом развернем инфраструктуру с помощью terraform. Запускаем построение при помощи команд:

- Инициализируем систему:

`terraform init`

- Проверяем правильность файлов:

`terraform plan`

- Запускаем построение инфраструктуры:

`terraform apply`

4. По итогу мы видим, что построение прошло успешно и мы можем видеть назначенные IP-адреса всем хостам:

![Список созданных хостов]( )

5. Тестируем сайт командой 


`curl -v <публичный IP балансера>:80`

- получим:

![Результат команды]( )

6. Подключаемся через ssh  к хосту `bastion_host_public`:

`ssh alifanov@<ip>`

- пароль `admin`

![Результат команды]( )

7. Сперва нам нужно настроить на данном хосте видимость остальных хостов в сети, поэтому переходим к настройке 

`sudo nano /etc/hosts`

8. После чего нам необходимо создать ключ ssh и расшарить его по остальным хостам с помощью команды:

`ssh-keygen`

`ssh-copy-id <название хоста>`

![Результат команды]( )

9. Добавляем ранее подготовленный репозиторий git с сервисами и конфигами для того чтобы расшарить между хостами необходимые сервисы.

`git clone https://github.com/Adrenokrome72/config_and_install`

10. Запускаем по очереди все плейбуки:

`ansible-playbook web.yaml`
`ansible-playbook ELK.yaml`
`ansible-playbook monitor.yaml`

- по завершении убеждаемся, что всё установилось без ошибок, видя сообщения :

![Результат команды web]( )
![Результат команды ELK]( )
![Результат команды monitor]( )

11. Переходим на elasticsearch хост:

`ssh alifanov@<IP-elastic>`

12. Устанавливаем пароль:

``
