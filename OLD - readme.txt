================================================================================
Более подробная инструкция по "Add-Hoc" командам
--------------------------------------------------------------------------------

-m setup			- Сканировать сервер и выдать ВСЕ ВСЕ данные о нем

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