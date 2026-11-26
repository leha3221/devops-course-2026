## 10. Ansible

Управляющий узел — Ubuntu в WSL на Windows. Проект находится в `~/devops-course-2026/ansible`. Управляемый узел — учебная VirtualBox VM `devops-vm`, Ubuntu Server 24.04 amd64, Host-only адрес `192.168.213.101`.

- `inventory/bootstrap.ini` используется для начального запуска от `student` с интерактивным вводом паролей SSH и sudo.
- `inventory/prod.ini` используется для повторных запусков от `devops` по ключу SSH на порту 22.
- Роль `base` проверяет платформу, восстанавливает доступное зеркало Ubuntu и устанавливает Docker Engine.
- Роль `ssh` настраивает учётную запись автоматизации, аутентификацию по ключу и UFW.
- Роль `nginx` настраивает reverse proxy на порту 80 и проверяет конфигурацию перед перезагрузкой.
- Роль `app` загружает образ, запускает контейнер и проверяет его HTTP-ответ.

Образ — `ghcr.io/leha3221/devops-ci-app:4f4795e69305127e113fe2aca2dc4f25088c0081`. Порт контейнера `5000/tcp` привязан на VM к `127.0.0.1:5000`. Клиенты проверяют приложение через Nginx по адресу `http://192.168.213.101/health`.

Команды из каталога `ansible`:

```bash
ansible-playbook -i inventory/bootstrap.ini site.yml --ask-pass --ask-become-pass
ansible-playbook -i inventory/prod.ini site.yml
ansible-playbook -i inventory/prod.ini verify.yml
```

Аудит `verify.yml` проверяет конфигурацию SSH, состояние контейнера и ответ приложения без внесения изменений.
