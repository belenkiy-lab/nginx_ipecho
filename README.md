# Nginx IP Echo

Конфиги nginx для реализации просмотра информации о подключении. Показывает в формате JSON  айпи адрес, порт и заголовок X-Forwarded-For.

## ipecho в стиле ciokan/ipecho (без контейнера)

Цель: сделать ipecho без доп контейнера, совместим по поведению с ciokan/ipecho: 
[ipecho Github](https://github.com/ciokan/ipecho)
[ipecho DockerHub](https://hub.docker.com/r/ciokan/ipecho)





- По умолчанию: JSON
  - `GET /` -> {"ip","port","forwarded_for"}
- Режим simple:
  - `GET /?display=simple` -> IP
  - `GET /?display=simple&field=port` -> port
  - `GET /?display=simple&field=forwarded-for` -> X-Forwarded-For

Почему так:
- В nginx нельзя использовать `map` внутри `server{}` — поэтому `map` вынесен в `nginx/conf.d/ipecho.conf` (контекст http{}).
- `snippets/ipecho-root.conf` содержит только `location` и `return` (без `if`), чтобы работать независимо от места include.

## Установка на сервер

Файлы:
- `/etc/nginx/conf.d/ipecho.conf` <- `nginx/conf.d/ipecho.conf`
- `/etc/nginx/snippets/ipecho-root.conf` <- `nginx/snippets/ipecho-root.conf`
- `/etc/nginx/sites-available/10-default` <- `nginx/sites-available/10-default`

Применение:
```bash
cp -a nginx/conf.d/ipecho.conf /etc/nginx/conf.d/
cp -a nginx/snippets/ipecho-root.conf /etc/nginx/snippets/
cp -a nginx/sites-available/10-default /etc/nginx/sites-available/
nginx -t && systemctl reload nginx

## Проверка

### 1) Nginx конфиг
```bash
nginx -t
systemctl reload nginx

### 2) ipecho (JSON)
```bash
curl -i https://your.domain/ | head -n 20

### 3) ipecho (simple)
```bash
curl -i 'https://your.domain/?display=simple' | head -n 20
curl -s 'https://your.domain/?display=simple&field=port'
curl -s 'https://your.domain/?display=simple&field=forwarded-for'


[Владимир Черданцев](https://github.com/belenkiy-lab)