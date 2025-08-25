	OpenLDAP Server Installation with Ansible

Этот Ansible плейбук устанавливает и настраивает OpenLDAP сервер на Ubuntu LTS.
Требования

    Ubuntu 16.04/18.04/20.04/22.04/24.04 LTS

    Ansible 2.9+

    Python 3.x

Структура проекта

.
├── ansible.cfg
├── group_vars/
│   └── all.yml
├── inventory.ini
├── playbook.yml
├── README.md
└── roles/
    └── openldap_server/
        ├── defaults/
        │   └── main.yml
        ├── tasks/
        │   └── main.yml
        └── templates/
            ├── groups.ldif.j2
            └── ou.ldif.j2

Настройка переменных

Перед запуском отредактируйте файл group_vars/all.yml:

ldap_admin_password: "secure_password_here"
ldap_domain: "your-domain.com"
ldap_organization: "Your Organization Name"
ldap_users:
  - { name: "user1", surname: "User1", uid: "1001", password: "password1" }
  - { name: "user2", surname: "User2", uid: "1002", password: "password2" }
ldap_groups:
  - { name: "developers", gid: "2001" }
  - { name: "admins", gid: "2002" }

1. Установите необходимые коллекции Ansible
ansible-galaxy collection install community.general
2. . Запустите плейбук
ansible-playbook playbook.yml -i inventory.ini

Что устанавливается и настраивается

    Пакеты: slapd, ldap-utils, ldapscripts, python3-ldap

    Конфигурация: Домен, организация, пароль администратора

    Структура LDAP:

    Базовый DN: dc=uno-soft,dc=ru

    Organizational Units: ou=people, ou=groups

    Пользователи: user1, user2 с UID 1001, 1002

    Группы: developers (GID 2001), admins (GID 2002)

    Сервис: slapd запущен и включен в автозагрузку

Проверка подключения

ldapwhoami -x -D "cn=admin,dc=uno-soft,dc=ru" -w admin

Поиск всех записей

ldapsearch -x -b "dc=uno-soft,dc=ru" -D "cn=admin,dc=uno-soft,dc=ru" -w admin

Поиск пользователей

ldapsearch -x -b "ou=people,dc=uno-soft,dc=ru" -D "cn=admin,dc=uno-soft,dc=ru" -w admin

Поиск групп

ldapsearch -x -b "ou=groups,dc=uno-soft,dc=ru" -D "cn=admin,dc=uno-soft,dc=ru" -w admin

Проверка конкретного пользователя

ldapsearch -x -b "uid=user1,ou=people,dc=uno-soft,dc=ru" -D "cn=admin,dc=uno-soft,dc=ru" -w admin

Результат

TASK [openldap_server : Final success message] *****************************************************************************************************************************************
ok: [localhost] => {
    "msg": "OpenLDAP installation completed successfully!\n============================================\nAdmin DN: cn=admin,dc=uno-soft,dc=ru\nBase DN: dc=uno-soft,dc=ru\nUsers created: user1, user2\nGroups created: developers, admins\n\nTo test connection:\nldapsearch -x -b \"dc=uno-soft,dc=ru\" -D \"cn=admin,dc=uno-soft,dc=ru\" -w \"admin\"\n"
}

![photo_2025-08-25_15-18-21](https://github.com/user-attachments/assets/a2ea733d-f5a0-4916-9fe0-e73addc8e87e)

