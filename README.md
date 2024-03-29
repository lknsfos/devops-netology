# devops-netology

# Домашнее задание к занятию «4.1. Командная оболочка Bash: Практические навыки»
# Михаил Карпов

## Обязательная задача 1

Есть скрипт:
```bash
a=1
b=2
c=a+b
d=$a+$b
e=$(($a+$b))
```

Какие значения переменным c,d,e будут присвоены? Почему?

| Переменная  | Значение | Обоснование                                                                                                                                    |
| ------------- |----------|------------------------------------------------------------------------------------------------------------------------------------------------|
| `c`  | a+b      | Не указан символ переменной для a,b, нет обозначения для расчета сложения как математической функции, всё после знака равно считается строкой. |
| `d`  | 1+2      | a и b помечены как переменные, но выражение не отмечено как какая-либо функция, потому считается строкой, переменные заменены их значением.    |
| `e`  | 3        | Сразу объявляется, что будут производиться математические операции, переменные внутри также оформлены, потому будет произведено сложение       |


## Обязательная задача 2
На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным (после чего скрипт должен завершиться). В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить:
```bash
while ((1==1)
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
	fi
done
```

### Ваш скрипт:
```bash
#!/bin/bash

a=1
while (($a != 0))
do
        curl https://localhost:4757
        a=$?
        if (($a != 0))
        then
                date >> curl.log
        fi
sleep 1
done

```

## Обязательная задача 3
Необходимо написать скрипт, который проверяет доступность трёх IP: `192.168.0.1`, `173.194.222.113`, `87.250.250.242` по `80` порту и записывает результат в файл `log`. Проверять доступность необходимо пять раз для каждого узла.

### Ваш скрипт:
```bash
#!/bin/bash

hosts=("192.168.0.1" "173.194.222.113" "87.250.250.242")
port=80
logfile=log
counts=5
for hcounter in ${hosts[@]}
do      
        for (( counter=1; counter<=$counts; counter++ ))
        do
                curl $hcounter:$port > /dev/null
                conn_result=$?
                case $conn_result in
                        0)
                                text_result="Up"
                                ;;
                        *)
                                text_result="Down"
                                ;;
                esac
                now=`date`
                echo "$now - Host $hcounter:$port is $text_result now. This is $counter count" >> log
        done
done
```

## Обязательная задача 4
Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается.

### Ваш скрипт:
```bash
#!/bin/bash

hosts=("173.194.222.113" "87.250.250.242" "192.168.0.1")
port=80
logfile=log
while ((conn_result == 0))
do
        for hcounter in ${hosts[@]}
        do      
                        curl $hcounter:$port > /dev/null
                        conn_result=$?
                        case $conn_result in
                                0)
                                        text_result="Up"
                                        ;;
                                *)
                                        text_result="Down"
                                        echo "$hcounter" > error
                                        break
                                        ;;
                        esac
                        now=`date`
                        echo "$now - Host $hcounter:$port is $text_result now." >> log
        done
done
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Мы хотим, чтобы у нас были красивые сообщения для коммитов в репозиторий. Для этого нужно написать локальный хук для git, который будет проверять, что сообщение в коммите содержит код текущего задания в квадратных скобках и количество символов в сообщении не превышает 30. Пример сообщения: \[04-script-01-bash\] сломал хук.

### Ваш скрипт:
```bash
???
```