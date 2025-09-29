Role Name Alertmanager
=========

Ansible роль для установки prometheus alertmanager

Requirements
------------

Нет.

Role Variables
--------------

Для установки alertmanager необходимо указать:
* alertmanager_image - docker образ;
* alertmanager_version - версия alertmanager (https://github.com/prometheus/alertmanager/releases);
* alertmanager_compose_dir - директорию docker-compose;
* alertmanager_data_dir - директорию с данными;
* alertmanager_config_dir - директорию с конфигурационными файлами;
* alertmanager_port - порт;
* alertmanager_user - пользователь для alertmanager;
```
alertmanager_version: v0.23.0
alertmanager_compose_dir: /opt/prometheus/alertmanager
alertmanager_data_dir: /usr/data/prometheus/alertmanager_data
alertmanager_config_dir: /usr/data/prometheus/alertmanager_config
alertmanager_port: 9093
alertmanager_user: 65534
```
Для настройки маршрута по умолчанию необходимо указать переменную alertmanager_default_route, где:
* receiver - получатель;
* group_wait - ожидание оповещений для группы;
* group_interval - ожидание новых оповещений для группы;
* repeat_interval - время для повторного отправления оповещения;
* group_by - группировка оповещений;
```
alertmanager_default_route:
  - receiver: slack
    group_wait: 10s
    group_interval: 2m
    repeat_interval: 24h
    group_by: ['...']
```
Для настройки маршрутов необходимо указать переменную alertmanager_routes, где:
* receiver - получатель;
* repeat_interval - время для повторного отправления оповещения;
* match_re - список условий, которым долно соответствовать оповещение для получателя;
* continue - должена ли осуществляться проверка оповещения на соответсвие после первого совпадения с получателем. Используется для отправки оповещения на несколько получателей при соответствии условий;
```
alertmanager_routes:
  - receiver: slack
    repeat_interval: 24h
    match_re:
      - env: production
    continue: false
```
Для настройки запрещающих правил необходимо указать переменную alertmanager_inhibit_rules, где:
* name - наименование правила;
* source_match - источник оповещения, которое будет отклчюено в случае совпадения;
* target_match - получатель оповещения, для которого выполнится оповещение;
* equal - метки, которые должны совпадать у источника и получателя;
```
alertmanager_inhibit_rules:
  - name: severity
    source_match: critical
    target_match: warning
    equal: ['...']
```
Для настройки оповещений в slack необходимо указать переменную alertmanager_receivers_slack, где:
* receiver - получатель;
* send_resolved - отправлять решенные оповещения;
* title - заголовок уведомления;
* title_link - ссылка на оповещения;
* text - содержимое уведомления;
* color - цвет уведомления;
* api_url - webhook URL;
* channel - канал или пользоватеь для отправки уведомлений;
```
alertmanager_receivers_slack:
  - receiver: slack
    send_resolved: true
    title: http://alertmanager/#/alerts
    title_link: slack_title
    text: slack_text
    color: slack_color
    api_url: https://hooks.slack.com/123
    channel: alerts
```
Для настройки оповещения в telegram необходимо указать переменную alertmanager_receivers_telegram, где:
* receiver - получатель;
* address - адрес телеграм бота;
* chat_id - ид чата телеграм;
* send_resolved - отправлять решенные оповещения;
```
alertmanager_receivers_telegram: 
  - receiver: telegram
    address: http://telegram_bot
    chat_id: 123
    send_resolved: true
```

Dependencies
------------

Роль Alertmanager используется с prometheus. Для оповещения в телеграм должен быть настроен телеграм бот

Example Playbook
----------------

```
- hosts: all
  become: yes
  gather_facts: yes
  roles:
    - role: alertmanager
```

License
-------

BSD

Author Information
------------------

Дата создания роли 10.01.2022.