# netology-8.1
## Домашнее задание к занятию "08.01 Введение в Ansible"

### Задание 1

1. Установите ansible версии 2.10 или выше.
2. Создайте свой собственный публичный репозиторий на github с произвольным именем.
3. Скачайте `playbook` из репозитория с домашним заданием и перенесите его в свой репозиторий.

![alt](https://i.ibb.co/VxpV44d/Screenshot-2.jpg)

---
### Основная часть

> 1. Попробуйте запустить `playbook` на окружении из `test.yml`, зафиксируйте какое значение имеет факт `some_fact` для указанного хоста при выполнении `playbook'a`.

```
ansible-playbook -i inventory/test.yml site.yml
```

![alt](https://i.ibb.co/pWxKQb4/Screenshot-3.jpg)



> 2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.
```
romrsch@ubuntu:~/8.1/playbook$ cat group_vars/all/examp.yml
---
  some_fact: "all default fact"


romrsch@ubuntu:~/8.1/playbook$ 
romrsch@ubuntu:~/8.1/playbook$ ansible-playbook -i inventory/test.yml site.yml
```
![alt](https://i.ibb.co/m6XxFkQ/Screenshot-4.jpg)




> 3. Воспользуйтесь подготовленным (используется docker) или создайте собственное окружение для проведения дальнейших испытаний.
> 4. Проведите запуск playbook на окружении из prod.yml. Зафиксируйте полученные значения some_fact для каждого из managed host.

```
romrsch@ubuntu:~/8.1/playbook$ ansible-playbook -i inventory/prod.yml site.yml 

PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [centos7]
ok: [ubuntu]

TASK [Print OS] ********************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ******************************************************************************************************************************
ok: [centos7] => {
    "msg": "el"
}
ok: [ubuntu] => {
    "msg": "deb"
}

PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

> 5. Добавьте факты в group_vars каждой из групп хостов так, чтобы для some_fact получились следующие значения: для deb - 'deb default fact', для el - 'el default fact'.

![alt](https://i.ibb.co/ZXcXh2P/Screenshot-5.jpg)

> 6. Повторите запуск playbook на окружении prod.yml. Убедитесь, что выдаются корректные значения для всех хостов.

```
romrsch@ubuntu:~/8.1/playbook$ ansible-playbook -i inventory/prod.yml site.yml 

PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [centos7]
ok: [ubuntu]

TASK [Print OS] ********************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ******************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```


> 7. При помощи ansible-vault зашифруйте факты в group_vars/deb и group_vars/el с паролем netology.

```
ansible-vault encrypt group_vars/deb/examp.yml
ansible-vault encrypt group_vars/el/examp.yml
```
![alt](https://i.ibb.co/1Qg4Rg2/Screenshot-6.jpg)


> 8. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь в работоспособности.
``` 
romrsch@ubuntu:~/8.1/playbook$ 
romrsch@ubuntu:~/8.1/playbook$ ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ********************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ******************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

>9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.

```
romrsch@ubuntu:~/8.1/playbook$ ansible-doc -t connection -l
```
Подходящим будет:
```
local                          execute on controller 
```
![alt](https://i.ibb.co/bdX2NVv/Screenshot-7.jpg)


>10. В `prod.yml` добавьте новую группу хостов с именем `local`, в ней разместите `localhost` с необходимым типом подключения.

![alt](https://i.ibb.co/qdLTLSq/Screenshot-8.jpg)

>11. Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь что факты some_fact для каждого из хостов определены из верных group_vars.

сделал `group_vars/local/examp.yml` для `local`


```
romrsch@ubuntu:~/8.1/playbook$ 
romrsch@ubuntu:~/8.1/playbook$ ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [localhost]
ok: [centos7]
ok: [ubuntu]

TASK [Print OS] ********************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ******************************************************************************************************************************
ok: [localhost] => {
    "msg": "local default fact"
}
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

>12. Заполните README.md ответами на вопросы. Сделайте git push в ветку master. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым playbook и заполненным README.md.

`https://github.com/romrsch/netology-8.0`

