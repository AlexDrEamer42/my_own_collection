# Домашнее задание к занятию 6 «Создание собственных модулей»

Создал файл `my_own_module.py`.
Добавил логику по созданию файла на удалённом хосте.
Проверил модуль на исполняемость локально:
```
(venv) alex@example ~/repo/my_own_collection/ansible (devel) $ python -m ansible.modules.my_own_module my_module_parameters.json 

{"changed": true, "message": "File is created", "invocation": {"module_args": {"path": "/home/alex/tmp.txt", "content": "123"}}}
(venv) alex@example ~/repo/my_own_collection/ansible (devel) $ python -m ansible.modules.my_own_module my_module_parameters.json 

{"changed": false, "message": "File exists", "invocation": {"module_args": {"path": "/home/alex/tmp.txt", "content": "123"}}}
```

Создал плейбук для запуска модуля:
```
---
- name: Write a file
  hosts: localhost
  tasks:
    - name: Call my_own_module
      my_own_module:
        path: /home/alex/tmp.txt
        content: 123
```
Результат:
```
(venv) alex@example ~/repo/my_own_collection/ansible (devel) $ ansible-playbook -v site.yml 
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features under development. This is a rapidly changing source
of code and can become unstable at any point.
Using /home/alex/.ansible.cfg as config file
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Write a file] ***********************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Call my_own_module] *****************************************************************************************************************************************************************************************************
changed: [localhost] => {"changed": true, "message": "File is created"}

PLAY RECAP ********************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
```
(venv) alex@example ~/repo/my_own_collection/ansible (devel) $ ansible-playbook -v site.yml 
[WARNING]: You are running the development version of Ansible. You should only run Ansible from "devel" if you are modifying the Ansible engine, or trying out features under development. This is a rapidly changing source
of code and can become unstable at any point.
Using /home/alex/.ansible.cfg as config file
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Write a file] ***********************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Call my_own_module] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {"changed": false, "message": "File exists"}

PLAY RECAP ********************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
Вывод: плейбук идемпотентен.

Инициализировал новую collection: 
```
ansible-galaxy collection init my_own_namespace.yandex_cloud_elk
```
Перенёс модуль в созданную collection, преобразовал в роль. Плейбук для роли:

```
---
- hosts: localhost
  collections:
    - my_own_namespace.yandex_cloud_elk

  tasks:
    - import_role:
        name: role_for_module

    - my_own_module:
        path: /home/alex/tmp.txt
        content: 234
```

Создал тарбол для коллекции:
```
alex@example ~/repo/my_own_collection/my_own_namespace/yandex_cloud_elk (master) $ ansible-galaxy collection build
Created collection for my_own_namespace.yandex_cloud_elk at /home/alex/repo/my_own_collection/my_own_namespace/yandex_cloud_elk/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz

```

Установил коллекцию из тарбола:

```
alex@example ~/repo/my_own_collection/test_tarball (master) $ ansible-galaxy collection install my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz
Starting galaxy collection install process
Process install dependency map
Starting collection install process
Installing 'my_own_namespace.yandex_cloud_elk:1.0.0' to '/home/alex/.ansible/collections/ansible_collections/my_own_namespace/yandex_cloud_elk'
my_own_namespace.yandex_cloud_elk:1.0.0 was installed successfully
```

Проверил работу плейбука:

```
alex@example ~/repo/my_own_collection/test_tarball (master) $ ansible-playbook -v site.yml 
Using /home/alex/.ansible.cfg as config file
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] **************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [my_own_module] **********************************************************************************************************************************************************************************************************
changed: [localhost] => {"changed": true, "message": "File is created"}

PLAY RECAP ********************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
```
alex@example ~/repo/my_own_collection/test_tarball (master) $ ansible-playbook -v site.yml 
Using /home/alex/.ansible.cfg as config file
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [localhost] **************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [my_own_module] **********************************************************************************************************************************************************************************************************
ok: [localhost] => {"changed": false, "message": "File exists"}

PLAY RECAP ********************************************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

Ссылка на коллекцию: https://github.com/AlexDrEamer42/my_own_collection/tree/master/my_own_namespace

Ссылка на тарбол: https://github.com/AlexDrEamer42/my_own_collection/blob/master/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz