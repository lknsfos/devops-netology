# devops-netology

# Домашнее задание к занятию «3.1. Работа в терминале, лекция 1»
# Михаил Карпов


**5.** Оперативная память - 1024МБ, Процессоры - 2, Видеопамять - 4МБ, Жесткий диск - Виртуальный размер 64ГБ, Сетевая карта Intel PRO/1000 MT Desktop в режиме NAT.

**6.** Добавить в конфиг внутрь VagrantConfigure("2") do |config|, например, такие строки для увеличения памяти и количества процессоров:  
	config.vm.provider "virtualbox" do |v|  
  		v.memory = 2048  
  		v.cpus = 4  
	end

**8.** 3010 строка - описание HISTORY, упоминается HISTSIZE и далее HISTFILESIZE - количество строк записываемых в файл  
862 Строка HISTSIZE
846 Строка HISTFILESIZE  
ignoreboth включает в себя ignorespace и ignoredups, которые в свою очередь исключают в истории записи о командах, начинающихся с пробелов и дубли команд.

**9.** {} используются
как тело в связках зарезервированных команд for ! case do done elif else esac fi for function if in select then until while - 179 строка   
в составных командах - 257 строка  
как обозначение тела функции - 400 строка  
в массивах для исключения конфликтов с именами - 1035 строка  
brace expansion - замена одних данных другими из фигурных скобок - раздел с 1091 строки  
в переменных(параметрах) для помечания позиционных параметров - строка 1167

**10.** touch file{1..100000} получится, до 300000 не получится, так как передастся на выполнение аргументов больше чем готово  принять окружение   
getconf ARG_MAX  
2097152


**11.** [[ -d /tmp ]] проверит существование каталога /tmp и вернет true(1) при его существовании

**12.** sudo mkdir /tmp/new_path_directory  
sudo ln /bin/bash /usr/local/bin/  
sudo ln /bin/bash /tmp/new_path_directory/  
PATH=/tmp/new_path_directory/:/usr/local/bin/:$PATH  
type -a bash  
>bash is /tmp/new_path_directory/bash  
>bash is /usr/local/bin/bash  
>bash is /usr/local/bin/bash  
>bash is /usr/bin/bash  
>bash is /bin/bash

**13.** at запускает команду в определенное время  
batch запускает команду когда загрузка системы достигает заданного значения.