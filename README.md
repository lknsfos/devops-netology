# devops-netology

#Домашнее задание к занятию «2.1. Системы контроля версий.»
#Михаил Карпов

В директории terraform будут проигнорированы:  
-все файлы во вложенных директориях .terraform;  
-все файлы с именем окначивающиемся на .tfstate и/или имеющих .tfstate в середине имени;  
-логи crash.log и все логи именованнные как crash и имеющие нумерацию или доп. описание в середине имени файла;  
-все файлы имеющие окончание .tfvars и .tfvars.json так как несут конфеденциальную информацию;  
-Пропускаются override.tf, override.tf.json, и все файлы с именами заканчивающиемися на *_override.tf, *_override.tf.json;  
-Игнорируются файлы конфигурации cli: .terraformrc, terraform.rc;  
-В комментариях (#) предлагается пример для исключения файлов из игнора.