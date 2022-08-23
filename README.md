# devops-netology

# Домашнее задание к занятию «3.2. Работа в терминале, лекция 2»
# Михаил Карпов

**1.** Команда cd...  
**2.** grep -c (-c это count)  
**3.** sudo ls -l /proc/1/exe    
lrwxrwxrwx 1 root root 0 Aug 23 08:25 /proc/1/exe -> /usr/lib/systemd/systemd    
Ответ: systemd  
Для просмотра дерева родительства  
pstree -p 1  
systemd(1)─┬─ModemManager(735)─┬─{ModemManager}(754)....    
**4.**  vagrant@vagrant:\~$ tty  
/dev/pts/0  
vagrant@vagrant:\~$ ls something 2>/dev/pts/1    
**5.** vagrant@vagrant:\~$ echo "test123" > something  
vagrant@vagrant:\~$ cat < something > someelse  
vagrant@vagrant:\~$ cat someelse  
test123  
**6.** Перевести возможно из pty в tty стандартным способом, например  
echo $PATH > /dev/tty2  
но для просмотра потребуется переключение в данную tty из графического режима (например ctrl+alt+F2)  
**7.** Выполнение визуально ничего не производит  
vagrant@vagrant:\~$ bash 5>$1  
vagrant@vagrant:\~$  
Но создастся кастомный дескриптор 5, перенаправляющий в stdout.  
vagrant@vagrant:\~$ echo "lala" >&5  
lala  
Ну или пример из задания  
vagrant@vagrant:\~$ echo netology > /proc/$$/fd/5  
netology  
Отправляем строку netology в процесс нашего баша ($$), в дескриптор 5, который перенаправляет поток в дескриптор 1 (stdout). Итого видим на выходе отправленную строку "netology"
**8.** ls /proc/$$/fd/4 
mkdir /root/yandex 4>&2 2>&1 1>&4 | grep can  
mkdir: cannot create directory ‘/root/yandex’: Permission denied  
Создали новый дескриптор 4, направили его в stderr, stderr вливаем в stdout, stdout в новый десприптор, который выльет в stderr.  
**9.** Выведутся переменные текущего окружения. Аналогичная команда env  
**10.** cmdline содержит команду запущенного процесса: /proc/[pid]/cmdline This read-only file holds the complete command line for the process, unless the process  is  a  zombie. In  the  latter case, there is nothing in this file: that is, a read on this file will return 0 characters.  The command-line arguments appear in this file as a set  of  strings  separated  by  null  bytes ('\0'), with a further null byte after the last string.  
exe представляет собой символьную ссылку на само приложение процесса:  
/proc/[pid]/exe
              Under  Linux 2.2 and later, this file is a symbolic link containing the actual pathname of the executed
              command.  This symbolic link can be dereferenced normally; attempting to open it  will  open  the  exe‐
              cutable.   You  can  even type /proc/[pid]/exe to run another copy of the same executable that is being
              run by process [pid].  If the pathname has been unlinked, the symbolic link  will  contain  the  string
              '(deleted)'  appended  to the original pathname.  In a multithreaded process, the contents of this sym‐
              bolic link are not  available  if  the  main  thread  has  already  terminated  (typically  by  calling
              pthread_exit(3)).
**11.** vagrant@vagrant:\~$ cat /proc/cpuinfo | grep -i SSE  
flags  : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid tsc_known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx rdrand hypervisor lahf_lm abm 3dnowprefetch invpcid_single fsgsbase avx2 invpcid rdseed clflushopt md_clear flush_l1d arch_capabilities    
Ответ: 4.2  
**12.** Для выполнения команды tty требуется созданный tty, однако при создании ssh соединения с выполнением команды, терминал не создается, потому не получается взять информацию о нем.    
Следует принудительно создать терминал с помощью ключа -t.  
vagrant@vagrant:\~$ ssh -t localhost 'tty'    
vagrant@localhost's password:    
/dev/pts/2    
**14.** tee читает из из stdin и пишет сразу в файл и в stdout.  
В данном случае tee с правами администратора берет stdin слева от pipe и вносит его в /root/new_file 
