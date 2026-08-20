## x-ui-pro (x-ui + nginx) — модификация https://github.com/GFW4Fun/x-ui-pro для REALITY
- Автоматическая установка (легковесная)
- Автоматическое продление SSL / Ежедневный перезапуск Nginx и X-ui
- Работа **REALITY** и **WebSocket** через **nginx**.
- Мультипользовательская конфигурация через порт **443**
- Автоматически включённые подписки через порт **443**
- Автоматическая настройка VLESS+Reality и VLESSoverWebSocket
- **Пользовательская веб-страница подписки**
- Возможность использования **пользовательских конфигураций клиентов для SING-BOX и CLASH META**
- **Локальный экземпляр sub2sing-box**
- Автоматическая настройка файрвола
- Больше безопасности и меньше детектирования благодаря nginx
- Совместимость с Cloudflare (только для WebSocket/GRPC)
- Случайный шаблон из 150+ фейковых сайтов!
- Linux Debian12/Ubuntu24!
  >
   **Вам нужны ДВА домена или поддомена**
  1. Для панели и WebSocket/GRPC/HttpUgrade/SplitHttp
  2. Для REALITY-назначения
  >
  Бесплатные поддомены - лучше не брать. Купите лучше за 100-200 рублей где нить домен (например на https://sweb.ru/) и не парьтесь. 
  >
  Инструкция на русском - https://scarce-hole-1e2.notion.site/3X-UI-pro-with-REALITY-panel-and-inbaunds-on-port-443-10d1666462e48085be0fee4c136ce417

➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖

### Установка X-UI-PRO

```
bash <(wget -qO- https://github.com/2Kappa-Mikey/x-ui-kappa/raw/master/x-ui-pro.sh) -install yes -panel 1 -ONLY_CF_IP_ALLOW no
```
> 
> Не меняйте SubDomain при продлении SSL❗


**Удаление X-UI-PRO**:x:
```
sudo su -c "bash <(wget -qO- https://raw.githubusercontent.com/2Kappa-Mikey/x-ui-kappa/master/x-ui-pro.sh) -Uninstall yes"
```

**Резервное копирование панели и конфигов nginx**:x:
```
sudo su -c "bash <(wget -qO- https://raw.githubusercontent.com/2Kappa-Mikey/x-ui-kappa/master/backup.sh)"
```

➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖➖
### Скриншоты :wrench:🐧⚙️
>
**Как открыть пользовательскую веб-страницу подписки?**
>
![](https://github.com/legiz-ru/x-ui-pro/blob/master/media/CustomWebSubHow2Open.png?raw=true)
>
**Главная страница пользовательской веб-подписки**
>
![](https://github.com/legiz-ru/x-ui-pro/blob/master/media/CustomWebSub.png?raw=true)
>
**Секция sub2sing-box на странице пользовательской веб-подписки**
>
![](https://github.com/legiz-ru/x-ui-pro/blob/master/media/CustomWebSubSingBox.png?raw=true)
>
**Локальный экземпляр sub2sing-box (форк legiz)**
>
![](https://github.com/legiz-ru/x-ui-pro/blob/master/media/sub2sing.png?raw=true)