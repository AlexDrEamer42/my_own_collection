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
ansible-galaxy collection init my_own_namespace.yandex_cloud_elk`
```
Перенёс модуль в созданную collection

**Шаг 10.** Single task playbook преобразуйте в single task role и перенесите в collection. У role должны быть default всех параметров module.

**Шаг 11.** Создайте playbook для использования этой role.

**Шаг 12.** Заполните всю документацию по collection, выложите в свой репозиторий, поставьте тег `1.0.0` на этот коммит.

**Шаг 13.** Создайте .tar.gz этой collection: `ansible-galaxy collection build` в корневой директории collection.

**Шаг 14.** Создайте ещё одну директорию любого наименования, перенесите туда single task playbook и архив c collection.

**Шаг 15.** Установите collection из локального архива: `ansible-galaxy collection install <archivename>.tar.gz`.

**Шаг 16.** Запустите playbook, убедитесь, что он работает.

**Шаг 17.** В ответ необходимо прислать ссылки на collection и tar.gz архив, а также скриншоты выполнения пунктов 4, 6, 15 и 16.
