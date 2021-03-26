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