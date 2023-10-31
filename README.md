По мотивам https://habr.com/ru/articles/687512/  
# Безопасный HTTPS-прокси  
## Установка dumbproxy  
```curl -Lo /usr/local/bin/dumbproxy 'https://github.com/Snawoot/dumbproxy/releases/download/v1.11.0/dumbproxy.linux-amd64' && chmod +x /usr/local/bin/dumbproxy```  
## Проверка:  
 команда ```dumbproxy -version```  
 должна выводить   
 ```v1.11.0```  
## Конфигурирование dumbproxy  
Команда  
```dumbproxy -passwd /etc/dumbproxy.htpasswd 'USERNAME' 'PASSWORD'```  
добавит в _/etc/dumbproxy.htpasswd_  строку с логином и хешированным паролем.  
## Конфигурация dumbproxy.
Создайте файл _/etc/default/dumbproxy_  
```OPTIONS=-auth basicfile://?path=/etc/dumbproxy.htpasswd&hidden_domain=DOMAIN.NAME.ORG -autocert -bind-address :443```  
Файл _/etc/systemd/system/dumbproxy.service_  
```
[Unit]
Description=Dumb Proxy
Documentation=https://github.com/Snawoot/dumbproxy/
After=network.target network-online.target
Requires=network-online.target

[Service]
EnvironmentFile=/etc/default/dumbproxy
User=root
Group=root
ExecStart=/usr/local/bin/dumbproxy $OPTIONS
TimeoutStopSec=5s
PrivateTmp=true
ProtectSystem=full
LimitNOFILE=20000

[Install]
WantedBy=default.target
```  
### Применить новую конфигурацию systemd:  
```systemctl daemon-reload```  
### Запустить dumbproxy  
#### автозапуск следующей командой:  
```systemctl enable dumbproxy```  
#### запустить сервис:  
```systemctl start dumbproxy```  
#### Проверка:  
команда  
```curl -x 'https://USERNAME:PASSWORD@DOMAIN' http://ifconfig.co```  
должна выводить IP-адрес сервера.  
