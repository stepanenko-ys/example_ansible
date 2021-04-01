***

# ANSIBLE

**Automation Configuration Management Tools**

**Система автоматизации конфигурации и управления серварами**

* https://docs.ansible.com/ansible/latest/index.html

* https://www.youtube.com/watch?v=Ck1SGolr6GI&list=PLg5SS_4L6LYufspdPupdynbMQTBnZd31N

<br>

***


### Все разделы:

1. <a href="#Install">Install</a><br>
2. <a href="#Uninstall">Uninstall</a><br>
3. <a href="#Поехали!">Поехали!</a><br>
4. <a href="#Файл hosts.txt">Файл hosts.txt</a><br>
5. <a href="#Add-Hoc команды">Add-Hoc команды</a><br>
6. <a href="#Вынос переменных в файл">Вынос переменных в файл</a><br>
7. <a href="#Создание Playbook">Создание Playbook</a><br>
- <a href="#Возможные ошибки">Возможные ошибки</a><br>

<br>

***

<a id="Install"></a>

## 1. Install:

###### На управляемых серверах должен быть открыт порт 22

#### Разные виды установки:

```bash
sudo python3 -m pip install ansible
```

```bash
brew install ansible
```

```bash
pip3 install ansible
```

<br>

***

<a id="Uninstall"></a>

## 2. Uninstall:

```bash
pip3 uninstall ansible
```

<br>

***

<a id="Поехали!"></a>

## 3. Поехали!

<br>

#### 1. Создание файла с описанием серверов:

> nano hosts.txt
> ```
> [MYXA_SERVERS]
> linux1 ansible_host=2.194.19.166 ansible_user=ubuntu
> 
> [STAGING_SERVERS]
> linux2 ansible_host=3.236.37.216 ansible_user=ubuntu
> linux3 ansible_host=3.236.186.202 ansible_user=ubuntu
> ```

<br>

#### 2. Запуск команды "ping" на разных группах серверов:

```bash
ansible -i hosts.txt all -m ping
```

```bash
ansible -i hosts.txt MYXA_SERVERS -m ping
```

```bash
ansible -i hosts.txt STAGING_SERVERS -m ping
```

<br>

#### 3. Отображение версии и параметров Ansible

```bash
ansible --version
```

*  `config file = None` - **Это означает что Конфиг файла у нас пока никакого нет**

<br>

#### 4. Создание файла конфигурации

> nano ansible.cfg
> ```
> [defaults]
> host_key_checking   = false
> inventory           = ./hosts.txt
> ```

*  `host_key_checking` - **Это никогда не будет спрашивать за отпечаток ключа при попытке подключения к серверу вручную**
*  `inventory` - **Это позволит не писать каждый раз в команде: -i hosts.txt**

<br /><br />

#### 5. Запуск команды "ping" без указания: -i hosts.txt:

```bash
ansible all -m ping    
```

<br>

#### 6. Создание плейбука:

> nano myfile_1.yml
> ```
> ---
> 
> - name: Test Connection to my servers
>   hosts: all
>   become: yes
>
>   tasks:
>
>     - name: Ping my servers
>       ping:
>  
> ...
> ```

Важно не забыть написать "---" первой строкой файла!

* `- name:` - **Это называется Лист** 
* `become: yes` - **Это запуск команд в режиме SUDO**

<br>

#### 7. Запуск созданного плейбука:

```bash
ansible-playbook myfile_1.yml
```

<br><br><br>

***

<a id="Файл hosts.txt"></a>

## 4. Файл hosts.txt (расширенное описание)

* https://www.youtube.com/watch?v=KsBb4ezQXq8&list=PLg5SS_4L6LYufspdPupdynbMQTBnZd31N&index=6

<br>

> nano hosts.txt
> ```
> 10.50.1.1
> 10.50.1.2
> 
> [staging_DB]
> 2.194.19.166
> 2.194.19.167
> 
> [staging_WEB]
> 3.236.37.210
> 3.236.37.211
> 3.236.37.212
> 
> [staging_APP]
> 3.236.186.238
> 3.236.186.239
> 
> [staging_ALL:children]
> staging_DB
> staging_WEB
> staging_APP
> 
> [prod_DB]
> 2.194.19.164
> 2.194.19.36
> 
> [prod_WEB]
> 3.236.37.53
> 3.236.37.55
> 3.236.37.61
> 
> [prod_APP]
> 3.236.186.213
> 3.236.186.223
> ```

Здесь описаны все наши сервера.<br>
Все они кроме первых двух "10.50.1.1" и "10.50.1.2" входят в группу "all".<br>
Первые два входят в группу "ungroup".<br>

<br /><br />

#### Создание групп с группами серверов:

> ```
> [prod_ALL:children]
> prod_DB
> prod_WEB
> prod_APP
>
> [DB_ALL:children]
> staging_DB
> prod_DB
> 
> [CUSTOM:children]
> staging_WEB
> prod_APP
> staging_APP
> staging_DB
> ```

<br /><br />

#### Создание групп переменных

Они удобны для того — что-бы не писать для каждого сервера повторяющиеся данные, например такте как<br>
ansible_user=ubuntu<br>
или если у нескольких серверов будет путь к одному SSH ключу:

> ```
> [STAGING_SERVERS]
> linux2 ansible_host=3.236.37.216
> linux3 ansible_host=3.236.186.202
> 
> [STAGING_SERVERS:vars]
> ansible_user=ubuntu
> ansible_ssh_private_key_file=/home/stepanenko/.ssh/staging_rsa
> ```

<br /><br />

#### Отобразить дерево серверов со связанными переменными:

```bash
ansible-inventory --list
```

<br><br><br>

***

<a id="Add-Hoc команды"></a>

## 5. Add-Hoc команды (расширенное описание)

* https://www.youtube.com/watch?v=3eRQDA7Y-lA&list=PLg5SS_4L6LYufspdPupdynbMQTBnZd31N&index=7

<br>

**Add-Hoc** — это просто отдельные команды, если она не в ПлейБуке.

Пример запуска: 

```bash
ansible all -m ping
```

```bash
ansible all -m shell -a "uptime"
```

Ключ `-m` - обозначает "Модуль". 

Описание всех доступных модулей можно взять по следующей команде:

```bash
ansible-doc -l
```

<br />

### Сами команды:


> Пинг
> > ansible all -m ping

> Сканировать сервер и выдать ВСЕ ВСЕ данные о нем
> > ansible all -m setup

<br>

**Shell** - Выполнить любую Линуксовскую команду

> Показать время работы сервера с момента загрузки
> > ansible all -m shell -a "uptime"

> Показать путь к текущему месту
> > ansible all -m shell -a "pwd"

> Создать папку "World" в глобальной папке пользователя (например: /home/ubuntu/)
> > ansible all -m shell -a "mkdir World"

<br>

**Сommand** - То-же самое что и shell, только запускается НЕ в режиме SHELL, тоесть он не будет видеть Environment Variables (Переменные окружающией среды) $HOME и < > | : &**

> Показать время работы сервера с момента загрузки
> > ansible all -m command -a "uptime"

<br>

> Скопировать файл на все сервера. А атрибут "mode=777" - необязательный параметр
> > ansible all -m copy -a "src=2.txt dest=/home/ubuntu/ mode=777"

> Скопировать файл
> > ansible all -m copy -a "src=2.txt dest=/home/ mode=777" -b

Ключ `-b` - означает Запуск команд в режиме SUDO

<br>

> Удаление файла или папки. Так-же, при помощи этого модуля можно создавать дериектории, ....
> > ansible all -m file -a "path=/home/ububntu/1.txt state=absent"

> Скачать файл из интернета
> > ansible all -m get_url -a "url=https://neofacto.com/wp-content/uploads/2019/08/sticker-hello-300x211.png dest=/home/ubuntu/"

<br>

> Установка приложений (у меня не сработала)
> > ansible all -m yum -a "name=stress state=installed" -b

> Рабочая
> > ansible all -m apt -a "name=stress state=latest" -b
> > 
> > ansible all -m apt -a "name=gunicorn state=latest" -b

<br>

> Удаление приложений
> > ansible all -m apt -a "name=stress state=absent" -b

<br>

> Проверить, может ли сервер подконнектиться к сайту (status: 200)
> > ansible all -m uri -a "url=https://myxa.com.ua"

> Получить контент с сайта
> > ansible all -m uri -a "url=https://myxa.com.ua return_content=yes"

<br>

> Запустить сервис httpd (у меня не сработал, но должен был запуститься Apache на всех серврах).
> > ansible all -m service -a "name=httpd state=started enabled=yes" -b

Параметр `enabled=yes` - необязательный, позволяет запускать данный сервис при старте всей системы.

<br>

> Запуск команд с подробной технической информацией о сервере
> > ansible all -m ping -v` **- **

> С БОЛЕЕ подробной информацией
> > ansible all -m ping -vv

> С еще БОЛЕЕ
> > ansible all -m ping -vvv

> С еще БОЛЕЕ
> > ansible all -m ping -vvvv

<br>

> Перезагрузить все сервера
> > ansible all -m reboot -b

<br><br><br>

***

<a id="Вынос переменных в файл"></a>

## 6. Вынос переменных в отдельный файл

* https://www.youtube.com/watch?v=_fAgi3jeQJM&list=PLg5SS_4L6LYufspdPupdynbMQTBnZd31N&index=9

<br />

#### Показать список всех серверов и какие переменные к ним относятся:

```bash
ansible-inventory --list - 
```

<br />

#### Создать папку, в которой будут храниться файлы с переменными:

```bash
mkdir group_vars
```

<br />

#### В ней создавать файлы с названиями групп из "hosts.txt"

> nano group_vars/STAGING_SERVERS
> ```
> ---
> ansible_user                 : ubuntu
> ansible_ssh_private_key_file : /home/stepanenko/.ssh/staging_rsa
> ```

Внимание! Знак "=" заменяем на знак ":"

<br />

> nano group_vars/MYXA_SERVERS
> ```
> ---
> ansible_user                 : ubuntu
> ansible_ssh_private_key_file : /home/stepanenko/.ssh/myxa_rsa
> ``` 

Внимание! Знак "=" заменяем на знак ":"

<br />

#### Запуск и проверка:

```bash
ansible-playbook myfile_1.yml
```

<br><br><br>

***

<a id="Создание Playbook"></a>

## 7. Создание Playbook

* https://www.youtube.com/watch?v=5VjcJNQ7nlI&list=PLg5SS_4L6LYufspdPupdynbMQTBnZd31N&index=10

<br>

Вариант № 1

> nano myfile_1.yml
> ```
> ---
> 
> - name: Test Connection to my servers
>   hosts: all
>   become: yes
>
>   tasks:
>
>   - name: Ping my servers
>     ping:
> 
> ...
> ```

<br>

Вариант № 2

> nano myfile_2.yml
> ```
> ---
> 
> - name: Create custom Folder an install Gunicorn
>   hosts: STAGING_SERVERS
>   become: yes
>
>   tasks:
>
>   - name: Create Folder
>     file:
>         path : Hello/123
>         state: directory
>         owner: ubuntu
>         group: ubuntu
>         mode : 0777
> 
>   - name: Install Gunicorn
>     yum: name=gunicorn state=latest
> 
>   - name: Start Gunicorn and Enabled in on the every boot 
>     service: name=gunicorn state=started enabled=yes
> 
> ...
> ```

<br>

Вариант № 3

> nano myfile_3.yml
> ```
> ---
> 
> - name: Install Apache and Upload my Web page
>   hosts: STAGING_SERVERS
>   become: yes
> 
>   vars:
>     source_file: ./MyWebSite/index.html
>     destin_file: /var/www/html
> 
>   tasks:
>   - name: Update apt-get repo and cache
>     apt: update_cache=yes force_apt_get=yes cache_valid_time=3600
> 
>   - name: Install Apache Web Server
>     apt: name=apache2 state=latest
> 
>   - name: Install MINI-HTTPD Web Server
>     apt: name=mini-httpd state=latest
> 
>   - name: Copy myHomePage to Servers
>     copy: src={{ source_file }} dest={{ destin_file }} mode=0555
>     notify: Restart Apache
> 
>   - name: Start WebServer and make it enable on boot
>     service: name=mini-httpd state=started enabled=yes
> 
>   handlers:
>   - name: Restart Apache
>     service: name=mini-httpd state=restarted
> 
> ...
> ```

Конструкция `{{ source_file }}` и `{{ destin_file }}` позволяет использовать переменные из раздела "vars".  

Конструкция `handlers:` запуститься только в том случае — если файл изменился и скопировался на сервер в задаче "Copy myHomePage to Servers".

<br><br><br>

***

<a id="Создание Playbook"></a>

## 8. Переменные - Debug, Set_fact, Register

* https://www.youtube.com/watch?v=-vuZdaMdX4I&list=PLg5SS_4L6LYufspdPupdynbMQTBnZd31N&index=11

<br>

Добавляем для каждого сервера переменные "owner" (в файл с описанием серваров):

> nano hosts.txt
> ```
> [STAGING_SERVERS]
> linux2 ansible_host=34.231.225.44 owner=VASYA
> linux3 ansible_host=35.175.127.130 owner=Petya
> ```
 
<br>

Создаем новый Плейбук:

> nano myfile_4.yml
> ```
> ---
> 
> - name: Playbook for Variables Lesson
>   hosts: STAGING_SERVERS
>   become: yes
> 
>   vars:
>     message1 : Hello
>     message2 : World
>     secret   : AABBCCDDEE
> 
>   tasks:
>   - name: Print 1 Secret variable From playbook
>     debug:
>       var: secret
> ```

`var:` - Выводит значение переменных

<br>

> ```
>   - name: Print 2 Secret variable From playbook
>     debug:
>       msg: "Sekretnoe slovo: {{ secret }}"
> 
>   - name: Print Secret variable From Hosts
>     debug:
>       msg: "Vladelec etogo servera: {{ owner }}"
> ```

`msg:` - Выводит сообщения

<br>

Соединение переменных:

> ```
>   - set_fact: full_message="{{ message1 }} {{ message2 }} from {{ owner }}"
> 
>   - name: Print new variables - full_message
>     debug:
>       var: full_message
> ```

<br>

Вывод дефолтных переменных с данными серверов (полученных командой `ansible all -m setup`)

> ```
>   - name: Print default variable from server
>     debug:
>       var: ansible_distribution
> ```

<br>

Создание переменной "results" с результатами выполнения команды `uptime`.<br>
А так-же вывод Полных данных или только из Определенного ключа:

> ```
>   - name: Create OUTPUT data from command
>     shell: uptime
>     register: results
> 
>   - name: Print new results - Full
>     debug:
>       var: results
> 
>   - name: Print new results - Only one key
>     debug:
>       var: results.stdout
> ```

<br><br><br>

***

<a id="ХХХ"></a>

## 9. ХХХ




<br><br><br><br><br>

<a id="Возможные ошибки"></a>

***

### Возможная ошибка № 1: 
Невозможно залогиниться на `docker login registry-exist.exist.ua` или выполнить `docker run hello-world`

***

```
sudo chmod 666 /var/run/docker.sock
```

***

* https://askubuntu.com/questions/477551/how-can-i-use-docker-without-sudo

```
sudo -s
> sudo groupadd docker
> sudo usermod -aG docker ysstepanenko
> sudo chown root:docker /var/run/docker.sock
> sudo chown -R root:docker /var/run/docker
> sudo chmod 666 /var/run/docker.sock
> ```

***