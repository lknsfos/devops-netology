# devops-netology

# Домашнее задание к занятию «3.3. Операционные системы, лекция 1»
# Михаил Карпов

**1.** chdir("/home/vagrant")  
**2.** Библиотека - путь к которому постоянно обращается file  
openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3  
До него он также ищет его в локальных директориях пользователя, но не находит.  
**3.** Отправить пустое эхо в /proc/<PID>/fd/<FD>, абстрактный пример  
echo '' > /proc/999/fd/4  
**4.** Zombie- процессы полностью высвобождают занимаемые ресурсы ожидая передачи кода-завершения родительскому процессу,    
то есть среди всех ресурсов они занимают только место в таблице процессов.  
**5.** Для работы, утилите требуется подключить модуль ядра  
modprobe: ERROR: could not insert 'kheaders': Operation not permitted    
для чего потребуется запустить от рута.    
за первую секунды работы вызывались  
sudo /usr/sbin/opensnoop-bpfcc    
PID    COMM               FD ERR PATH    
871    vminfo              4   0 /var/run/utmp    
670    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services    
670    dbus-daemon        21   0 /usr/share/dbus-1/system-services    
670    dbus-daemon        -1   2 /lib/dbus-1/system-services    
670    dbus-daemon        21   0 /var/lib/snapd/dbus-1/system-services/    
677    irqbalance          6   0 /proc/interrupts    
677    irqbalance          6   0 /proc/stat    
**6.** uname трижды вызывает uname({sysname="Linux", nodename="vagrant", ...}) = 0  
В установленной системе не имелось man для системного вызова uname, потому использовался man внешний    
       Part of the utsname information is also accessible via
       /proc/sys/kernel/{ostype, hostname, osrelease, version,
       domainname}.  
**7.** && оператор который будет выполнять следующую команду только получив успешный exit code у первой. Аналог if .. then ..  
; - последовательное выполнение команд.  
set -e вкупе с && в целом не имеет смысла, так как последовательность логических и (&&) и так прервется при неверном завершении хоть одной команды.    
set -e в свою очередь также прерывает последовательность команд при exit code != 0.  
**8.** e останавливает очередность исполнения команд при ошибке.   
x отправит информацию о каждой команде в stderr.  
u параметры, которые не были заданы вызовут отправку ошибку в stderr, с выводом и остановку процесса  
o pipefail вернёт exit code всех команд в последовательности, или 0 при успешном выполнении всех команд.  
Команда с данными опциями повысит детализацию по выполнению команд, завершит выполнение при неполадках в последовательности команд.    
**9.** В системе самые часты процессы - процессы во сне и ожидании    
vagrant@vagrant:\~$ ps ax -o stat | grep -c S  
77  
vagrant@vagrant:\~$ ps ax -o stat | grep -c I  
55  
vagrant@vagrant:\~$ ps ax -o stat | grep -c T  
2  
vagrant@vagrant:\~$ ps ax -o stat | grep -c D  
0  
vagrant@vagrant:\~$ ps ax -o stat | grep -c Z  
0  
vagrant@vagrant:\~$ ps ax -o stat | grep -c R  
1  
Дополнительными символами являются характеристики: приоритеты, заблокированыне страницы в памяти, мультпоточность, лидерство в сессии и работу на переднем плане.