================================================================================
Ansible

Automation Configuration Management Tools
Система автоматизации конфигурации и управления серварами

https://docs.ansible.com/ansible/latest/index.html
https://www.youtube.com/watch?v=Ck1SGolr6GI&list=PLg5SS_4L6LYufspdPupdynbMQTBnZd31N
================================================================================



================================================================================
Install:

На управляемых серверах должен быть открыт порт 22
--------------------------------------------------

1.  Разные виды установки:

    > sudo python3 -m pip install ansible
    > brew install ansible
    > pip3 install ansible

    Удаление:
    > pip3 uninstall ansible


2.  nano hosts.txt

    i: [myxa_servers]
    i: linux1 ansible_host=2.194.19.166 ansible_user=ubuntu
    i: 
    i: [staging_servers]
    i: linux2 ansible_host=3.236.37.216 ansible_user=ubuntu
    i: linux3 ansible_host=3.236.186.202 ansible_user=ubuntu


3.  Запуск команды "ping" на разных группах серверов:

    > ansible -i hosts.txt all -m ping
    > ansible -i hosts.txt myxa_servers -m ping
    > ansible -i hosts.txt staging_servers -m ping


4.  ansible --version

    i: config file = None
    i: Это означает что Конфиг файла у нас пока никакого нет


5. nano ansible.cfg

    i: [defaults]
    i: host_key_checking   = false
    i: inventory           = ./hosts.txt

    i: host_key_checking - Это никогда не будет спрашивать за отпечаток ключа при попытке подключения к серверу вручную
    i: inventory         - Это позволит не писать каждый раз в команде "-i hosts.txt"


6.  ansible all -m ping    


7.  ansible all -m shell -a "uptime"
8.  ansible all -m shell -a "pwd"


9. nano myfile.yml

---
- name: Test Connection to my servers
  hosts: all
  become: yes
  
  tasks:
  
  - name: Ping my servers
    ping:


10. ansible-playbook myfile.yml

================================================================================





===================================================================
Ошибка: Не могу заголиниться на "docker login registry-exist.exist.ua" или выполнить "docker run hello-world"
---------------------------------------------------

А можно получить маленькую подсказочку?

Почитал про ансибл, очень крутая штука!) Создал YML, умею пинговать сервер )

А теперь когда ставлю в задачу "docker_login" получаю следующую ошибку
sudo chmod 666 /var/run/docker.sock

----------------------------------------------------
https://askubuntu.com/questions/477551/how-can-i-use-docker-without-sudo
----
sudo -s
sudo groupadd docker
sudo usermod -aG docker ysstepanenko
sudo chown root:docker /var/run/docker.sock
sudo chown -R root:docker /var/run/docker
sudo chmod 666 /var/run/docker.sock
----------------------------------------------------

===================================================================





================================================================================
Более подробная инструкция по "hosts.txt"

https://www.youtube.com/watch?v=KsBb4ezQXq8&list=PLg5SS_4L6LYufspdPupdynbMQTBnZd31N&index=6
--------------------------------------------------------------------------------

10.50.1.1
10.50.1.2

[staging_DB]
2.194.19.166
2.194.19.167

[staging_WEB]
3.236.37.210
3.236.37.211
3.236.37.212

[staging_APP]
3.236.186.238
3.236.186.239

[staging_ALL:children]
staging_DB
staging_WEB
staging_APP

[prod_DB]
2.194.19.164
2.194.19.36

[prod_WEB]
3.236.37.53
3.236.37.55
3.236.37.61

[prod_APP]
3.236.186.213
3.236.186.223

[prod_ALL:children]
prod_DB
prod_WEB
prod_APP

    i: Эти все сервера входят в группу "all" только кроме кроме "10.50.1.1" b "10.50.1.2" - эти два входят в группу "ungroup".

    i: Очень удобно можно создать группу, например, "DB_ALL" или "CUSTOM"

[DB_ALL:children]
staging_DB
prod_DB

[CUSTOM:children]
staging_WEB
prod_APP
staging_APP
staging_DB


    i: Можно сделать группу переменных, для того что-бы не писать для каждого 
    i: сервера повторяющиеся данные, например такте как "ansible_user=ubuntu"
    i: или если у нескольких серверов будет путь к одному SSH ключу.

[staging_servers]
linux2 ansible_host=3.236.37.216
linux3 ansible_host=3.236.186.202

[staging_servers:vars]
ansible_user=ubuntu


~~~~~~~~~~~~~~~~~~~~~~~~~~


> ansible-inventory --list

    i: Показать дерево серверов со связанными переменными.

================================================================================





================================================================================
Более подробная инструкция по "Add-Hoc" командам
--------------------------------------------------------------------------------

Add-Hoc - отдельаня команда, если она не в ПлейБуке

Пример запуска: 
> ansible all -m ping
> ansible all -m shell -a "uptime"

-----------------

-m ping			- Пинг
-m setup			- Сканировать сервер и выдать ВСЕ ВСЕ данные о нем

-m shell -a "uptime"	- Выполнить любую Линуксовскую команду

-m command -a "uptime"	- То-же самое что и shell, только запускается НЕ в 
			  режиме SHELL, тоесть он не будет видеть
			  Environment Variables (Переменные окружающией среды)
			  $HOME и < > | : &

-m copy -a "src=2.txt dest=/home/ubuntu/ mode=777"	- Скопировать файл на все сервера
						  	  mode=777 - необязательный параметр
								  	  
-m copy -a "src=2.txt dest=/home/ mode=777" -b 		- Скопировать файл в режиме SUDO

-m file -a "path=/home/ububntu/1.txt state=absent"	- Удаление файла или папки.
							  Так-же, при помощи этого модуля можно создавать дериектории, ....


-m get_url -a "url=https://neofacto.com/wp-content/uploads/2019/08/sticker-hello-300x211.png dest=/home/ubuntu/"
							- Скачать файл из интернета

-m yum -a "name=stress state=installed" -b		- Утсановка приложений (у меня не сработала)
-m apt -a "name=stress state=latest" -b			- Рабочая
-m apt -a "name=gunicorn state=latest" -b

-m apt -a "name=stress state=absent" -b			- Удаление приложений

-m uri -a "url=https://myxa.com.ua"			- Проверить, может ли сервер подконнектиться к сайту (status: 200)
-m uri -a "url=https://myxa.com.ua return_content=yes"	- Получить контент с сайта

-m service -a "name=httpd state=started enabled=yes" -b	- Запустить сервис httpd (у меня не сработал, но должен был запуститься Apache на всех серврах)
							  enabled=yes - это необязательный параметр, позвонялет запускать данный сервис при старте всей системы
							  
-m ping -v	-vv	-vvv	-vvvv			- Запуск команд с ПОДРОБНОЙ информацией

ansible-doc -l						- Показать все достпуне модули в ansible

-m reboot -b						- Перезагрузить все сервера


--------------------------------
CREATE FOLDER

From - Command line:
> ansible all -m shell -a "mkdir World"


From - Playbook:
- name: Creates directory
  file:
    path: Hello/123
    state: directory
    owner: ubuntu
    group: ubuntu
    mode: 0777
--------------------------------


      
================================================================================





================================================================================

--------------------------------------------------------------------------------

================================================================================





================================================================================

--------------------------------------------------------------------------------

================================================================================





================================================================================

--------------------------------------------------------------------------------

================================================================================
