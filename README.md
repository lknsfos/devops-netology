# devops-netology

# Домашнее задание к занятию «3.4. Операционные системы, лекция 2»
# Михаил Карпов

**1.** Запущен   
ps aux | grep node_  
root        6024  0.0  0.7 724992 14984 ?        Ssl  12:25   0:00 /opt/node_exporter/node_exporter  
  
Запущен через systemctl  
  
systemctl status node_exporter  
● node_exporter.service - Node Exporter  
     Loaded: loaded (/etc/systemd/system/node_exporter.service; disabled; vendor preset: enabled)  
     Active: active (running) since Wed 2022-10-05 12:25:16 IDT; 1min 48s ago  
   Main PID: 6024 (node_exporter)  
      Tasks: 5 (limit: 2236)  
     Memory: 6.6M  
        CPU: 17ms  
     CGroup: /system.slice/node_exporter.service  
             └─6024 /opt/node_exporter/node_exporter  
  
Oct 05 12:25:16 ubuntu-linux-22-04-desktop node_exporter[6024]: ts=2022-10-05T09:25:16.133Z caller=node_exporter.go:115 level
  
  Включаем автозапуск  
systemctl enable node_exporter  
  
После перезапуска - восстанавливается.
  
systemctl status node_exporter  
● node_exporter.service - Node Exporter  
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor>  
     Active: active (running) since Wed 2022-10-05 12:35:22 IDT; 50s ago  
   Main PID: 682 (node_exporter)  
      Tasks: 5 (limit: 2236)  
     Memory: 13.5M  
        CPU: 12ms  
     CGroup: /system.slice/node_exporter.service  
             └─682 /opt/node_exporter/node_exporter



  
Может останавливаться.    
  
sudo systemctl stop node_exporter  
sudo systemctl status node_exporter  
○ node_exporter.service - Node Exporter  
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor>  
     Active: inactive (dead) since Wed 2022-10-05 12:41:38 IDT; 5s ago  
    Process: 682 ExecStart=/opt/node_exporter/node_exporter (code=killed, signa>  
   Main PID: 682 (code=killed, signal=TERM)  
        CPU: 13ms  
  
systemctl status node_exporter
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor>
     Active: active (running) since Wed 2022-10-05 12:35:22 IDT; 50s ago
   Main PID: 682 (node_exporter)
      Tasks: 5 (limit: 2236)
     Memory: 13.5M
        CPU: 12ms
     CGroup: /system.slice/node_exporter.service
             └─682 /opt/node_exporter/node_exporter

Сам файл конфигурации для systemd  

cat node_exporter.service   
[Unit]  
Description=Node Exporter  
   
[Service]  
ExecStart=/opt/node_exporter/node_exporter  
EnvironmentFile=/etc/default/node_exporter  
   
[Install]  
WantedBy=default.target  



**2.** CPU
node_cpu_seconds_total{cpu="0",mode="idle"}  
node_cpu_seconds_total{cpu="0",mode="iowait"}  
node_cpu_seconds_total{cpu="0",mode="softirq"}  
node_cpu_seconds_total{cpu="0",mode="system"}  
node_cpu_seconds_total{cpu="0",mode="user"}  
Те же параметры для второго и последующих cpu.  
  
Memory  
node_memory_MemAvailable_bytes  
node_memory_MemTotal_bytes  
node_memory_SwapFree_bytes  
node_memory_SwapTotal_bytes
  
Disk  
node_disk_io_time_seconds_total
node_disk_read_bytes_total
node_disk_written_bytes_total

Network
node_network_carrier  
node_network_carrier_changes_total  
node_network_iface_link  
ode_network_mtu_bytes  
node_network_receive_bytes_total  
node_network_receive_drop_total  
node_network_receive_errs_total  
node_network_receive_packets_total  
node_network_speed_bytes  
node_network_transmit_bytes_total  
node_network_transmit_drop_total  
node_network_transmit_errs_total  
node_network_transmit_packets_total  
node_network_transmit_queue_length

**3.** Дз выполеяется не на x86 системе, vagrant+virtualbox не имееются, но bind на все адреса сработал, с головной машины доступ получил.  

**4.** В dmesg находится сообщение systemd об обнаружении сиситемы виртуализации. Возможно этого возможно будет избажать используя редкую нестандартную систему виртуализации.  
sudo dmesg | grep virt  
[    0.000000] arch_timer: cp15 timer(s) running at 24.00MHz (virt).  
[    0.144583] virtio-pci 0000:00:05.0: virtio_pci: leaving for legacy driver  
[    0.490725] virtio_net virtio1 enp0s5: renamed from eth0  
[    4.164584] systemd[1]: Detected virtualization parallels.  
[    4.679436] [drm] pci: virtio-vga detected at 0000:00:0a.0  
[    4.680157] [drm] Initialized virtio_gpu 0.1.0 0 for virtio2 on minor 0  

**5.** sudo sysctl -a | grep fs.nr_open  
fs.nr_open = 1048576
Это максимальное количество файловых дескрипторов, которые может использовать процесс. Но по факту будет ограничено ulimit -Sn.    
ulimit -Sn  
1024  
**6.** unshare --fork --pid --mount-proc sleep 1h  
В другом терминале  
ps ax | grep sleep  
  40939 pts/1    S+     0:00 unshare --fork --pid --mount-proc sleep 1h    
  40940 pts/1    S+     0:00 sleep 1h    
nsenter -t 40940 --pid --mount  
ps ax   
    PID TTY      STAT   TIME COMMAND  
      1 pts/1    S+     0:00 sleep 1h  
      2 pts/3    S      0:00 -bash  
     17 pts/3    R+     0:00 ps ax  

**7.** :(){ :|:& };:  
судя по действию и описанию, здесь создается фуннкция вызывающая саму себя. Организуется бесконечный цикл.  
Метод которым был остановлен скрипт:  
[ 5603.570118] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/user@1000.service/app.slice/app-org.gnome.Terminal.slice/vte-spawn-322c4bd2-cd21-44f1-8ee7-1429992d6611.scope  
Судя по всему он настроен в конфигурациях security limits.  
Скрипт может быть будет ограничен количеством процессов на пользователя, например ulimit -u 10, после которого он будет выполняться всего несколько секунд и успеет завершиться сам.  