Структура папок
---------------

inventory/ описание инфраструктуры

- hosts - описание хостов

playbooks/ - операции в порядке запуска
  
- add_ssh - добавление ключей
- add_hosts - добавление хостов
- install_packages - установка общих пакетов
- network - генерация и установка /etc/hosts 
- galera - установка пакетов, конфигурация и запуск кластера бд *
- proxysql - установка, конфигурация и запуск балансировщика бд *
- nginx - установка, конфигурация и запуск web сервера *
- wordpress - установка, конфигурация и запуск базового сайта wordpress *
- unison - установка, конфигурация и запуск синхронизации сайтов wordpress
- backup - настройка резервного копирования базы и сайтов *

roles/ - библиотека ролей

- prometheus - установка и конфигурация сервера мониторинга
- grafana - установка и конфигурация графиков
- node_exporter - установка и конфигурация агентов мониторинга
- install_server - задание правил для iptables 

vars/ - глобальные переменные

- passwords - пароли (*)
- wordpress - настройки сайта wordpress



Запуск установки
----------------

start_play.yml запускает все плейбуки в нужном порядке

start_role.yml запускает все роли для установки

Для запуска установки клонируем репозиторий, переходим в /exam/ansible.
 
Выполняем ansible-playbook -i inventory/ start_play.yml --ask-vault-pass

Затем

Выполняем ansible-playbook -i inventory/ start_role.yml --ask-vault-pass, так как в хранилище лежит sudo_pass для хостов

При необходимости установки\перестановки отдельных элементов можно использовать теги:
- install_server
- monitoring (включает в себя установку grafana,prometheus,node_exporter,alertmanager)
- grafana
- prometheus (включает в себя установку prometheus,alertmanager)
- node_exporter
- * при необходимости очистить правила iptables НА ВСЕХ хостах использовать тег flush_rules

Пример: ansible-playbook -i inventory/ start_role.yml -t grafana --ask-vault-pass



Внимание!
---------

(*) Пароли зашифрованы средствами Ansible Vault
Для запуска playbooks galera, proxysql, nginx, wordpress, backup - используйте доп-ключ --ask-vault-pass
Например:
  ansible-playbook playbooks/galera.yaml -bK --ask-vault-pass

Посмотреть сами пароли можно командой
  ansible-vault view vars/passwords.yaml

Отредактировать
  ansible-vault edit vars/passwords.yaml

Альтернативно, можно на время расшифровать пароли, чтобы не указывать флаг и пароль, но после этого нельзя заливать в Git!
  ansible-vault decrypt vars/passwords.yaml
