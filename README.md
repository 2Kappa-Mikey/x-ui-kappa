# x-ui-kappa (x-ui + nginx)

> Модификация [GFW4Fun/x-ui-kappa](https://github.com/GFW4Fun/x-ui-kappa) для **REALITY**

[![Platform](https://img.shields.io/badge/platform-Linux%20Debian%2012%20%7C%20Ubuntu%2024-blue)]()
[![License](https://img.shields.io/badge/license-MIT-green)]()

## 📋 Оглавление

- [Особенности](#особенности)
- [Требования](#требования)
- [Быстрый старт](#быстрый-старт)
- [Установка](#установка)
- [Удаление](#удаление)
- [Резервное копирование](#резервное-копирование)
- [Использование](#использование)
- [Скриншоты](#скриншоты)
- [Troubleshooting](#troubleshooting)
- [Внося свой вклад](#внося-свой-вклад)
- [Лицензия](#лицензия)

---

## ✨ Особенности

- 🚀 **Автоматическая установка** (легковесная)
- 🔐 **Автоматическое продление SSL** / Ежедневный перезапуск Nginx и X-ui
- 🌐 Работа **REALITY** и **WebSocket** через **nginx**
- 👥 **Мультипользовательская конфигурация** через порт **443**
- 📡 **Автоматически включённые подписки** через порт **443**
- ⚙️ **Автоматическая настройка** VLESS+Reality и VLESS over WebSocket
- 🎨 **Пользовательская веб-страница подписки**
- 🛠️ Возможность использования **пользовательских конфигураций клиентов для SING-BOX и CLASH META**
- 🔄 **Локальный экземпляр sub2sing-box**
- 🔒 **Автоматическая настройка файрвола**
- 🛡️ **Больше безопасности и меньше детектирования** благодаря nginx
- ☁️ **Совместимость с Cloudflare** (только для WebSocket/GRPC)
- 🎲 **Случайный шаблон из 150+ фейковых сайтов!**
- 🐧 **Поддержка Linux Debian 12 / Ubuntu 24**

---

## ⚠️ Требования

> **Вам нужны ДВА домена или поддомена:**
> 1. Для панели и WebSocket/GRPC/HttpUpgrade/SplitHttp
> 2. Для REALITY-назначения

💡 **Совет:** Бесплатные поддомены - лучше не брать. Купите лучше за 100-200 рублей где-нибудь домен (например, на [sweb.ru](https://sweb.ru/)) и не парьтесь.

### Системные требования

- **ОС:** Debian 12 или Ubuntu 24
- **Права:** root или sudo доступ
- **Домены:** 2 домена или поддомена с настроенными DNS записями
- **Порты:** 80, 443 должны быть открыты

---

## 🚀 Быстрый старт

```bash
bash <(wget -qO- https://github.com/2Kappa-Mikey/x-ui-kappa/raw/master/x-ui-kappa.sh) -install yes -panel 1 -ONLY_CF_IP_ALLOW no
```

> ⚠️ **Не меняйте SubDomain при продлении SSL!**

📖 **Инструкция на русском:** [Notion Guide](https://scarce-hole-1e2.notion.site/3X-UI-pro-with-REALITY-panel-and-inbaunds-on-port-443-10d1666462e48085be0fee4c136ce417)

---

## 📦 Установка

### Стандартная установка

```bash
bash <(wget -qO- https://github.com/2Kappa-Mikey/x-ui-kappa/raw/master/x-ui-kappa.sh) -install yes -panel 1 -ONLY_CF_IP_ALLOW no
```

### Параметры установки

| Параметр | Описание |
|----------|----------|
| `-install yes` | Запустить установку |
| `-panel 1` | Номер панели (можно изменить) |
| `-ONLY_CF_IP_ALLOW no` | Разрешить только Cloudflare IP (no/yes) |

---

## ❌ Удаление

Для полного удаления X-UI-PRO:

```bash
sudo su -c "bash <(wget -qO- https://raw.githubusercontent.com/2Kappa-Mikey/x-ui-kappa/master/x-ui-kappa.sh) -Uninstall yes"
```

---

## 💾 Резервное копирование

Для резервного копирования панели и конфигов nginx:

```bash
sudo su -c "bash <(wget -qO- https://raw.githubusercontent.com/2Kappa-Mikey/x-ui-kappa/master/backup.sh)"
```

---

## 🔧 Использование

### Как открыть пользовательскую веб-страницу подписки?

См. раздел [Скриншоты](#скриншоты) ниже.

### Доступные команды

После установки вы можете управлять сервисом через стандартные systemd команды:

```bash
# Статус сервиса
systemctl status x-ui

# Перезапуск
systemctl restart x-ui

# Остановка
systemctl stop x-ui

# Запуск
systemctl start x-ui
```

---

## 📸 Скриншоты

### Как открыть пользовательскую веб-страницу подписки

![Как открыть пользовательскую веб-страницу подписки](https://github.com/legiz-ru/x-ui-kappa/blob/master/media/CustomWebSubHow2Open.png?raw=true)

### Главная страница пользовательской веб-подписки

![Главная страница пользовательской веб-подписки](https://github.com/legiz-ru/x-ui-kappa/blob/master/media/CustomWebSub.png?raw=true)

### Секция sub2sing-box на странице пользовательской веб-подписки

![Секция sub2sing-box](https://github.com/legiz-ru/x-ui-kappa/blob/master/media/CustomWebSubSingBox.png?raw=true)

### Локальный экземпляр sub2sing-box (форк legiz)

![Локальный экземпляр sub2sing-box](https://github.com/legiz-ru/x-ui-kappa/blob/master/media/sub2sing.png?raw=true)

---

## 🔍 Troubleshooting

### Частые проблемы и решения

#### 1. SSL не продлевается автоматически
- Проверьте, что домен правильно настроен (A запись указывает на ваш сервер)
- Убедитесь, что порты 80 и 443 открыты в фаерволе
- Проверьте логи: `journalctl -u x-ui -f`

#### 2. Nginx не запускается
- Проверьте конфигурацию: `nginx -t`
- Посмотрите логи: `journalctl -u nginx -f`
- Убедитесь, что порт 80 и 443 не заняты другими сервисами

#### 3. REALITY не работает
- Проверьте, что второй домен правильно настроен
- Убедитесь, что сертификат действителен
- Проверьте настройки в панели x-ui

#### 4. Подписка не обновляется
- Очистите кэш браузера
- Проверьте URL подписки
- Убедитесь, что порт 443 доступен

#### 5. Cloudflare не работает
- Убедитесь, что используется WebSocket или GRPC
- Проверьте настройки проксирования в Cloudflare
- Включите опцию `-ONLY_CF_IP_ALLOW yes` при установке

### Где посмотреть логи

```bash
# Логи x-ui
journalctl -u x-ui -f

# Логи nginx
journalctl -u nginx -f

# Логи SSL (certbot)
tail -f /var/log/certbot/certbot.log
```

### Восстановление после сбоя

Если что-то пошло не так, вы можете:
1. Сделать резервную копию текущей конфигурации
2. Переустановить скрипт с параметром `-install yes`
3. Восстановить конфиги из резервной копии

---

## 🤝 Внося свой вклад

Мы приветствуем ваш вклад в развитие проекта!

### Как помочь:

1. **Сообщить о баге** - создайте Issue с подробным описанием проблемы
2. **Предложить улучшение** - создайте Feature Request
3. **Отправить Pull Request** - форкните репозиторий и отправьте ваши изменения
4. **Поделиться опытом** - напишите руководство или tutorial

### Разработка

```bash
# Клонировать репозиторий
git clone https://github.com/2Kappa-Mikey/x-ui-kappa.git

# Перейти в директорию
cd x-ui-kappa

# Внести изменения
# ...

# Отправить PR
```

---

## 📄 Лицензия

Этот проект распространяется под лицензией MIT. Подробнее см. в файле [LICENSE](LICENSE), если он присутствует.

---

## 🔗 Полезные ссылки

- **Оригинальный проект:** [GFW4Fun/x-ui-kappa](https://github.com/GFW4Fun/x-ui-kappa)
- **Форк:** [2Kappa-Mikey/x-ui-kappa](https://github.com/2Kappa-Mikey/x-ui-kappa)
- **Инструкция на русском:** [Notion Guide](https://scarce-hole-1e2.notion.site/3X-UI-pro-with-REALITY-panel-and-inbaunds-on-port-443-10d1666462e48085be0fee4c136ce417)
- **Купить домен:** [sweb.ru](https://sweb.ru/)

---

## ⚠️ Отказ от ответственности

Этот инструмент предназначен только для образовательных целей и исследований в области сетевой безопасности. Используйте его ответственно и в соответствии с законодательством вашей страны. Авторы не несут ответственности за неправильное использование данного программного обеспечения.

---

<div align="center">

**Made with ❤️ by the Community**

[⬆️ Вернуться к началу](#x-ui-kappa-x-ui--nginx)

</div>