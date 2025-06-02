# prometheus-grafana
# Установка Prometheus

`cd /opt/` - все рабочие компоненты и Prometheus находятся здесь 
## Этап аналогичен и с компонентами
`wget https://github.com/prometheus/prometheus/releases/download/v2.26.0/prometheus-2.26.0.linux-amd64.tar.gz` - ссылка на скачивания Prometheus
`tar zxf prometheus-2.26.0.linux-amd64.tar.gz` - архивирование файла
`mv prometheus-2.26.0.linux-amd64 prometheus` - переименование 

# Запуск вручную 
## Этап аналогичен и с компонентами
`mkdir data` - в этой папке сохраняются данные  
`./prometheus --storage.tsdb.path="/opt/prometheus/data/"` - запуск с указанием куда сохранять 

# Создание пользователя и его папок 
`useradd -s /sbin/nologin -d /opt/prometheus prometheus` - создает пользователя и указывает его окружение
`chown -R prometheus:prometheus /opt/prometheus` - ставит права на директорию

# Работа с запуском демонов 
`cat /etc/systemd/system/prometheus.service` - в данной директории находятся все демоны, которые будут запушены 
`systemctl daemon-reload` - перезагружает всех демонов 
`systemctl enable prometheus` - ставить автозапуск для демона 
## Конфигурация  для демона 
``` 
[Unit] 
Description=Node 
Exporter 
Wants=network-online.target 
After=network-online.target 
[Service] 
User=root 
Group=root 
Type=simple 
ExecStart=/opt/node_exporter/node_exporter 
[Install] 
WantedBy=multi-user.target
```


# Подключение системного экспортера

`cd /opt/` - загружаются в этой папке
`wget https://github.com/prometheus/node_exporter/releases/download/v1.1.2/node_exporter-1.1.2.linux-amd64.tar.gz`- ссылка на скачивание

## Подключение системного экспортера к Prometheus
`nano /etc/prometheus/prometheus.yml` - нужно перенести файл в этот путь, файл указываешь откуда собирать метрики 

#Пример 
```
global: 
scrape_interval: 15s # указываем интервал сбора
scrape_configs: 
 - job_name: 'prometheus' # указываем название
   static_configs: # указываем настройки
   - targets: ['localhost:9090'] # указываем где работает 
 - job_name: 'node' 
   scrape_interval: 5s 
   static_configs: 
   - targets: ['localhost:9100']
	 labels: # указываем тег для более удобного сбора информации 
	   env: 'dev' 
```
