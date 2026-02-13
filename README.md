# Домашнее задание к занятию "`Система мониторинга Zabbix`" - `Сысоев Алексей`
---
### Задание 1 

Установите Zabbix Server с веб-интерфейсом.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите PostgreSQL. Для установки достаточна та версия, что есть в системном репозитороии Debian 11.
3. Пользуясь конфигуратором команд с официального сайта, составьте набор команд для установки последней версии Zabbix с поддержкой PostgreSQL и Apache.
4. Выполните все необходимые команды для установки Zabbix Server и Zabbix Web Server.

#### Требования к результатам 
1. Прикрепите в файл README.md скриншот авторизации в админке.
2. Приложите в файл README.md текст использованных команд в GitHub.

**Решение:**  
Команды:  
```# ssh 0dm1n@89.169.132.67```

Установка PG:  
```# sudo apt update && sudo apt upgrade -y```  
```# sudo apt install postgresql postgresql-contrib -y```  

Что происходит при выполнении этих команд:  
- sudo apt update — обновляет локальный список пакетов, чтобы система «видела» актуальные версии ПО;  
- sudo apt upgrade -y — обновляет уже установленные пакеты (-y автоматически подтверждает действие, чтобы не ждать ввода);  
- postgresql — основной пакет СУБД;  
- postgresql-contrib — дополнительные модули (например, pgcrypto для шифрования или uuid-ossp для генерации UUID).  

Далее, по ссылке:  
https://www.zabbix.com/download?zabbix=6.0&os_distribution=debian&os_version=11&components=server_frontend_agent&db=pgsql&ws=apache  
Install Zabbix repository  
```# wget https://repo.zabbix.com/zabbix/6.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_6.0+debian11_all.deb```  
```# dpkg -i zabbix-release_latest_6.0+debian11_all.deb```  
```# apt update```  

Install Zabbix server, frontend, agent  
```# apt install zabbix-server-pgsql zabbix-frontend-php php7.4-pgsql zabbix-apache-conf zabbix-sql-scripts zabbix-agent```  

Create initial database  
```# sudo -u postgres createuser --pwprompt zabbix```  
```# sudo -u postgres createdb -O zabbix zabbix```  

или:  
```# su - postgres -c 'psql --command "CREATE USER zabbix WITH PASSWORD '\'1234567890\'';"'```  
```# su - postgres -c 'psql --command "CREATE DATABASE zabbix OWNER zabbix;"'```  

On Zabbix server host import initial schema and data. You will be prompted to enter your newly created password.  
```# zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix```

Configure the database for Zabbix server  
```# sed -i 's/# DBPassword=/DBPassword=1234567890/g' /etc/zabbix/zabbix_server.conf```  

Start Zabbix server and agent processes  
```# systemctl restart zabbix-server zabbix-agent apache2```  
```# systemctl enable zabbix-server zabbix-agent apache2```  

**Скриншоты:**  
скриншот VM:  
<img src="https://raw.githubusercontent.com/1000karat/hw-02/refs/heads/main/img/_0001.png">  
скриншот авторизации в админке:  
<img src="https://raw.githubusercontent.com/1000karat/hw-02/refs/heads/main/img/_0002.png">  
<img src="https://raw.githubusercontent.com/1000karat/hw-02/refs/heads/main/img/_0003.png">  
<img src="https://raw.githubusercontent.com/1000karat/hw-02/refs/heads/main/img/_0004.png">  

---

### Задание 2 

Установите Zabbix Agent на два хоста.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите Zabbix Agent на 2 вирт.машины, одной из них может быть ваш Zabbix Server.
3. Добавьте Zabbix Server в список разрешенных серверов ваших Zabbix Agentов.
4. Добавьте Zabbix Agentов в раздел Configuration > Hosts вашего Zabbix Servera.
5. Проверьте, что в разделе Latest Data начали появляться данные с добавленных агентов.

#### Требования к результатам
1. Приложите в файл README.md скриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу
2. Приложите в файл README.md скриншот лога zabbix agent, где видно, что он работает с сервером
3. Приложите в файл README.md скриншот раздела Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные.
4. Приложите в файл README.md текст использованных команд в GitHub

**Решение:**
```# ssh -J 0dm1n@89.169.132.67 0dm1n@10.10.0.20```

Далее, по ссылке:  
https://www.zabbix.com/download?zabbix=6.0&os_distribution=ubuntu&os_version=22.04&components=agent&db=&ws=  

Install Zabbix repository  
```# wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_6.0+ubuntu22.04_all.deb```  
```# dpkg -i zabbix-release_latest_6.0+ubuntu22.04_all.deb```  
```# apt update```  

Install Zabbix agent  
```# apt install zabbix-agent```  

Правим конфиг  
```# sed -i 's/Server=127.0.0.1/Server=10.10.0.10/g' /etc/zabbix/zabbix_agentd.conf```

Start Zabbix agent process  
```# systemctl restart zabbix-agent```  
```# systemctl enable zabbix-agent```  

Такие шаги и для Debian.  

**Скриншоты:**  
скриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу:  
<img src="https://raw.githubusercontent.com/1000karat/hw-02/refs/heads/main/img/_0005.png">  

скриншот лога zabbix agent, где видно, что он работает с сервером:  
<img src="https://raw.githubusercontent.com/1000karat/hw-02/refs/heads/main/img/_00010.png">  
<img src="https://raw.githubusercontent.com/1000karat/hw-02/refs/heads/main/img/_0009.png">  

скриншот Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные:  
<img src="https://raw.githubusercontent.com/1000karat/hw-02/refs/heads/main/img/_0007.png">  
<img src="https://raw.githubusercontent.com/1000karat/hw-02/refs/heads/main/img/_0008.png">  

---
## Задание 3 со звёздочкой*
Установите Zabbix Agent на Windows (компьютер) и подключите его к серверу Zabbix.

#### Требования к результатам
1. Приложите в файл README.md скриншот раздела Latest Data, где видно свободное место на диске C:
--- 

## Критерии оценки

1. Выполнено минимум 2 обязательных задания
2. Прикреплены требуемые скриншоты и тексты 

3. Задание оформлено в шаблоне с решением и опубликовано на GitHub
