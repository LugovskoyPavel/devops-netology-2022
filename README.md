№Основная часть
1. Попробуйте запустить playbook на окружении из test.yml, зафиксируйте какое значение имеет факт some_fact для указанного хоста при выполнении playbook'a.
```
lugy@lugy-virtual-machine:~/ansibdir/mnt-homeworks/08-ansible-01-base/playbook$ ansible-playbook -i inventory/test.yml site.yml

PLAY [Print os facts] ***************************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************************** 
ok: [localhost]

TASK [Print OS] ********************************************************************************************************************************************************************* 

ok: [localhost] => { "msg": "Ubuntu" }

TASK [Print fact] ******************************************************************************************************************************************************************* 

ok: [localhost] => { "msg": 12 }

PLAY RECAP ************************************************************************************************************************************************************************** 
localhost : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```
2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.
```
lugy@lugy-virtual-machine:~/ansibdir/mnt-homeworks/08-ansible-01-base/playbook$ ansible-playbook -i inventory/test.yml site.yml

PLAY [Print os facts] ***************************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************************** 
ok: [localhost]

TASK [Print OS] ********************************************************************************************************************************************************************* ok: [localhost] => { "msg": "Ubuntu" }

TASK [Print fact] ******************************************************************************************************************************************************************* 
ok: [localhost] => { "msg": "all default fact" }

PLAY RECAP ************************************************************************************************************************************************************************** 
localhost : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```
3. Воспользуйтесь подготовленным (используется docker) или создайте собственное окружение для проведения дальнейших испытаний.

Поставлен образ Centos7

3. Проведите запуск playbook на окружении из prod.yml. Зафиксируйте полученные значения some_fact для каждого из managed host.
```
lugy@lugy-virtual-machine:~/ansibdir/mnt-homeworks/08-ansible-01-base/playbook$ sudo ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************* 
ok: [centos7] ok: [ubuntu]

TASK [Print OS] ******************************************************************************************************************************** 
ok: [centos7] => { "msg": "CentOS" } ok: [ubuntu] => { "msg": "Ubuntu" }

TASK [Print fact] ****************************************************************************************************************************** 
ok: [centos7] => { "msg": "el" } ok: [ubuntu] => { "msg": "deb" }

PLAY RECAP ************************************************************************************************************************************* 
centos7 : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
ubuntu : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```
4. Добавьте факты в group_vars каждой из групп хостов так, чтобы для some_fact получились следующие значения: для deb - 'deb default fact', для el - 'el default fact'.

Выполнено

5. Повторите запуск playbook на окружении prod.yml. Убедитесь, что выдаются корректные значения для всех хостов.
```
lugy@lugy-virtual-machine:~/ansibdir/mnt-homeworks/08-ansible-01-base/playbook$ sudo ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************* 
ok: [centos7] ok: [ubuntu]

TASK [Print OS] ******************************************************************************************************************************** 
ok: [centos7] => { "msg": "CentOS" } ok: [ubuntu] => { "msg": "Ubuntu" }

TASK [Print fact] ****************************************************************************************************************************** 
ok: [centos7] => { "msg": "el default fact" } ok: [ubuntu] => { "msg": "deb default fact" }

PLAY RECAP ************************************************************************************************************************************* 
centos7 : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
ubuntu : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```
6. При помощи ansible-vault зашифруйте факты в group_vars/deb и group_vars/el с паролем netology.
```
lugy@lugy-virtual-machine:~/ansibdir/mnt-homeworks/08-ansible-01-base/playbook$ sudo ansible-vault encrypt group_vars/deb/examp.yml

New Vault password:

Confirm New Vault password:

Encryption successful

lugy@lugy-virtual-machine:~/ansibdir/mnt-homeworks/08-ansible-01-base/playbook$ sudo ansible-vault encrypt group_vars/el/examp.yml

New Vault password:

Confirm New Vault password:

Encryption successful
```

7. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь в работоспособности.
```
lugy@lugy-virtual-machine:~/ansibdir/mnt-homeworks/08-ansible-01-base/playbook$ sudo ansible-playbook -i inventory/prod.yml site.yml

Vault password:

PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************* 
ok: [ubuntu] ok: [centos7]

TASK [Print OS] ******************************************************************************************************************************** 
ok: [centos7] => { "msg": "CentOS" } ok: [ubuntu] => { "msg": "Ubuntu" }

TASK [Print fact] ****************************************************************************************************************************** 
ok: [centos7] => { "msg": "el default fact" } ok: [ubuntu] => { "msg": "deb default fact" }

PLAY RECAP ************************************************************************************************************************************* 
centos7 : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
ubuntu : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```
8. Посмотрите при помощи ansible-doc список плагинов для подключения. Выберите подходящий для работы на control node.

Выполнено. Нужен модуль для подключния localhost на Ubuntu

9. В prod.yml добавьте новую группу хостов с именем local, в ней разместите localhost с необходимым типом подключения.
el: hosts: centos7: ansible_connection: docker deb: hosts: ubuntu: ansible_connection: docker local: hosts: localhost: ansible_connection: local

10. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь что факты some_fact для каждого из хостов определены из верных group_vars.
```
lugy@lugy-virtual-machine:~/ansibdir/mnt-homeworks/08-ansible-01-base/playbook$ sudo ansible-playbook -i inventory/prod.yml site.yml

Vault password:

PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************* 
[DEPRECATION WARNING]: Distribution Ubuntu 20.04 on host localhost should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with prior Ansible releases. A future Ansible release will default to using the discovered platform python for this host. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information. This feature will be removed in version 2.12. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg. ok: [localhost] ok: [centos7] ok: [ubuntu]

TASK [Print OS] ******************************************************************************************************************************** 
ok: [localhost] => { "msg": "Ubuntu" } ok: [centos7] => { "msg": "CentOS" } ok: [ubuntu] => { "msg": "Ubuntu" }

TASK [Print fact] ****************************************************************************************************************************** 
ok: [localhost] => { "msg": "local default fact" } ok: [centos7] => { "msg": "el default fact" } ok: [ubuntu] => { "msg": "deb default fact" }

PLAY RECAP ************************************************************************************************************************************* 
centos7 : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
localhost : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
ubuntu : ok=3 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```
11. Заполните README.md ответами на вопросы. Сделайте git push в ветку master. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым playbook и заполненным README.md.
