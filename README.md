1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте значение, которое имеет факт `some_fact` для указанного хоста при выполнении playbook.
```
TASK [Print fact] ****************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}
```
2. Найдите файл с переменными (group_vars), в котором задаётся найденное в первом пункте значение, и поменяйте его на `all default fact`.
```
TASK [Print fact] ****************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}
```
3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.
```
root@epdp0vk339ui0c121q2c:~/homework/playbook# docker --version
Docker version 24.0.5, build ced0996
```
4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.
```

ok: [centos7] => {
    "msg": "el"
}
ok: [ubuntu] => {
    "msg": "deb"
}
```
5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились значения: для `deb` — `deb default fact`, для `el` — `el default fact`.
6. Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.
```
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}
```
7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.
```
azamat@epd4amcvrliufcovl7ua:~/homework/ansible-homework/playbook$ ansible-vault encrypt ./group_vars/deb/examp.yml
New Vault password:
Confirm New Vault password:
Encryption successful
azamat@epd4amcvrliufcovl7ua:~/homework/ansible-homework/playbook$ ansible-vault encrypt ./group_vars/el/examp.yml
New Vault password:
Confirm New Vault password:
Encryption successful
```
8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.
```
azamat@epd4amcvrliufcovl7ua:~/homework/ansible-homework/playbook$ ansible-playbook -i ./inventory/prod.yml site.yml --ask-vault-pass
Vault password:

PLAY [Print os facts] ************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************
[WARNING]: The "docker" connection plugin has an improperly configured remote target value, forcing "inventory_hostname" templated value instead of the string
[WARNING]: The "docker" connection plugin has an improperly configured remote target value, forcing "inventory_hostname" templated value instead of the string
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ******************************************************************************************************************************************************************************
[WARNING]: The "docker" connection plugin has an improperly configured remote target value, forcing "inventory_hostname" templated value instead of the string
ok: [centos7] => {
    "msg": "CentOS"
}
[WARNING]: The "docker" connection plugin has an improperly configured remote target value, forcing "inventory_hostname" templated value instead of the string
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ****************************************************************************************************************************************************************************
[WARNING]: The "docker" connection plugin has an improperly configured remote target value, forcing "inventory_hostname" templated value instead of the string
ok: [centos7] => {
    "msg": "el default fact"
}
[WARNING]: The "docker" connection plugin has an improperly configured remote target value, forcing "inventory_hostname" templated value instead of the string
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ***********************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.
```
azamat@epd4amcvrliufcovl7ua:~/homework/ansible-homework/playbook$ ansible-doc -t connection -l | grep controller
ansible.builtin.local          execute on controller
community.docker.nsenter       execute on host running controller container
```
10. В `prod.yml` добавьте новую группу хостов с именем `local`, в ней разместите localhost с необходимым типом подключения.
```
azamat@epd4amcvrliufcovl7ua:~/homework/ansible-homework/playbook$ cat ./inventory/prod.yml
---
  el:
    hosts:
      centos7:
        ansible_connection: docker
  deb:
    hosts:
      ubuntu:
        ansible_connection: docker
  local:
    hosts:
      localhost:
        ansible_host: localhost
        ansible_connection: local
```
11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь, что факты `some_fact` для каждого из хостов определены из верных `group_vars`.
```
TASK [Print fact] ****************************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}
ok: [localhost] => {
    "msg": "all default fact"
}
```
12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.
