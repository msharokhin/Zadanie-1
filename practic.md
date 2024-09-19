# Задача 1
Вывести отсортированный в алфавитном порядке список имен пользователей в файле passwd (вам понадобится grep).
## Код
```bash
grep '.*' /etc/passwd | cut -d: -f1 | sort
```

```bash
xartd0@xartd0-Strix-GL504GW-GL504GW:~$ grep '.*' /etc/passwd | cut -d: -f1 | sort
_apt
avahi
backup
bin
colord
cups-browsed
cups-pk-helper
daemon
dhcpcd
dnsmasq
ftp
fwupd-refresh
games
gdm
geoclue
gnome-initial-setup
gnome-remote-desktop
hplip
irc
kernoops
list
lp
mail
man
messagebus
news
nm-openvpn
nobody
polkitd
proxy
root
rtkit
saned
speech-dispatcher
sshd
sssd
sync
sys
syslog
systemd-network
systemd-oom
systemd-resolve
systemd-timesync
tcpdump
tss
usbmux
uucp
uuidd
whoopsie
www-data
xartd0
```

# Задача 2
Вывести данные /etc/protocols в отформатированном и отсортированном порядке для 5 наибольших портов, как показано в примере ниже:
## Код
```bash
awk '{print $2, $1}' /etc/protocols | sort -nr | head -n 5
```
```bash
awk '{print $2, $1}' /etc/protocols | sort -nr | head -n 5
262 mptcp
143 ethernet
142 rohc
141 wesp
140 shim6
```

# Задача 3
Написать программу banner средствами bash для вывода текстов, как в следующем примере (размер баннера должен меняться!):
## Код
```bash
#!/bin/bash

text=$*
length=${#text}

for i in $(seq 1 $((length + 2))); do
    line+="-"
done

echo "+${line}+"
echo "| ${text} |"
echo "+${line}+"
```
```bash
xartd0@xartd0-Strix-GL504GW-GL504GW:~/confmirea$ ./banner.sh "xartd0"
+--------+
| xartd0 |
+--------+
```

# Задача 4
Написать программу для вывода всех идентификаторов (по правилам C/C++ или Java) в файле (без повторений).
## Код
```bash
#!/bin/bash

file="$1"

id=$(grep -o -E '\b[a-zA-Z]*\b' "$file" | sort -u)

```
```bash
xartd0@xartd0-Strix-GL504GW-GL504GW:~/confmirea$ grep -oE '\b[a-zA-Z_][a-zA-Z0-9_]*\b' hello.c | grep -vE '\b(int|void|return|if|else|for|while|include|stdio)\b' | sort | uniq
h
hello
main
n
printf
world
```

# Задача 5
Написать программу для регистрации пользовательской команды (правильные права доступа и копирование в /usr/local/bin).
## Код
```bash
#!/bin/bash

file=$1

chmod 755 "./$file"

sudo cp "$file" /usr/local/bin/
```

Например, пусть программа называется reg:
```bash
xartd0@xartd0-Strix-GL504GW-GL504GW:~/confmirea$ ./reg.sh banner.sh
xartd0@xartd0-Strix-GL504GW-GL504GW:~/confmirea$ ls /usr/local/bin
banner.sh  ngrok
```

# Задача 6
Написать программу для проверки наличия комментария в первой строке файлов с расширением c, js и py.
## Код
```bash
#!/bin/bash

# Проход по всем файлам с расширениями .c, .js, .py
for file in $(find . -type f \( -name "*.c" -o -name "*.js" -o -name "*.py" \)); do
    # Чтение первой строки файла
    first_line=$(head -n 1 "$file")
    
    # Проверка, начинается ли строка с комментария (для C, JS и Python)
    if [[ "$file" == *.c || "$file" == *.js ]]; then
        if [[ "$first_line" =~ ^(//|/\*) ]]; then
            echo "Файл $file содержит комментарий в первой строке."
        else
            echo "Файл $file не содержит комментарий в первой строке."
        fi
    elif [[ "$file" == *.py ]]; then
        if [[ "$first_line" =~ ^# ]]; then
            echo "Файл $file содержит комментарий в первой строке."
        else
            echo "Файл $file не содержит комментарий в первой строке."
        fi
    fi
done

```

```bash
xartd0@xartd0-System-Product-Name:~/Desktop/confupr$ ./6.sh 
Файл ./test.js содержит комментарий в первой строке.
Файл ./test.c содержит комментарий в первой строке.
Файл ./test.py не содержит комментарий в первой строке.
```

# Задача 7
Написать программу для нахождения файлов-дубликатов (имеющих 1 или более копий содержимого) по заданному пути (и подкаталогам).
## Код
```bash
#!/bin/bash

# Создаем временный файл для хранения контрольных сумм
temp_file=$(mktemp)

# Находим все файлы и считаем для них хэш SHA256
find "$1" -type f -exec sha256sum {} \; > "$temp_file"

# Сортируем и находим дубликаты по контрольным суммам
awk '{ print $1 }' "$temp_file" | sort | uniq -d > duplicates.txt

# Если найдено совпадение по хэшу, выводим файлы-дубликаты
if [ -s duplicates.txt ]; then
    echo "Найдены дубликаты файлов:"
    grep -f duplicates.txt "$temp_file"
else
    echo "Дубликатов файлов не найдено."
fi

# Удаляем временный файл
rm "$temp_file"

```

```bash
xartd0@xartd0-System-Product-Name:~/Desktop/confupr$ ./6.sh .
Найдены дубликаты файлов:
553844d44cf63d240f17403dd8b6ff5074efcfd3d783fcd9ebd60e522124d4a6  ./test.js
553844d44cf63d240f17403dd8b6ff5074efcfd3d783fcd9ebd60e522124d4a6  ./test (Copy).js
553844d44cf63d240f17403dd8b6ff5074efcfd3d783fcd9ebd60e522124d4a6  ./test (Copy 2).js
e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  ./duplicates.txt
e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  ./test (Copy 2).py
e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  ./test.py
e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  ./test (Copy 3).py
e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855  ./test (Copy).py
23dc822407041599a9a2e54fae33bff8c640ed087f8f1b2da78536f3fc8b47dc  ./conf_upravlenie/.git/logs/HEAD
23dc822407041599a9a2e54fae33bff8c640ed087f8f1b2da78536f3fc8b47dc  ./conf_upravlenie/.git/logs/refs/heads/main
23dc822407041599a9a2e54fae33bff8c640ed087f8f1b2da78536f3fc8b47dc  ./conf_upravlenie/.git/logs/refs/remotes/origin/HEAD

```

# Задача 8
## Код 
```bash
#!/bin/bash

# Проверка аргументов
if [ "$#" -ne 2 ]; then
    echo "Использование: $0 <каталог> <расширение>"
    exit 1
fi

directory=$1
extension=$2
archive_name="archive.tar"

# Поиск файлов с указанным расширением и архивирование
find "$directory" -type f -name "*.$extension" -print0 | tar -cvf "$archive_name" -T - --null

echo "Архив создан: $archive_name"
```

Написать программу, которая находит все файлы в данном каталоге с расширением, указанным в качестве аргумента и архивирует все эти файлы в архив tar.
```bash
xartd0@xartd0-Strix-GL504GW-GL504GW:~/confmirea$ ./tar.sh "test" "py"
tar: -: file name read contains nul character
test/1.py
test/2.py
Архив создан: archive.tar
```

# Задача 9
Написать программу, которая заменяет в файле последовательности из 4 пробелов на символ табуляции. Входной и выходной файлы задаются аргументами.
## Код
```bash
#!/bin/bash

# Проверка на наличие двух аргументов: входной и выходной файл
if [ "$#" -ne 2 ]; then
    echo "Использование: $0 input_file output_file"
    exit 1
fi

input_file=$1
output_file=$2

# Замена 4 пробелов на символ табуляции и запись в выходной файл
sed 's/    /\t/g' "$input_file" > "$output_file"

echo "Замена завершена. Результат сохранен в файл $output_file."

```
```bash
xartd0@xartd0-System-Product-Name:~/Desktop/confupr$ ./6.sh test.c test_new.c
Замена завершена. Результат сохранен в файл test_new.c.
```
Изначальный файл
```bash
    //test 4
        //test 8
        //test 8
            //test 12
```
Новый файл
```bash
	//test 4
		//test 8
		//test 8
			//test 12
```



# Задача 10
Написать программу, которая выводит названия всех пустых текстовых файлов в указанной директории. Директория передается в программу параметром.
## Код
```bash
#!/bin/bash

# Проверка аргументов
if [ "$#" -ne 1 ]; then
    echo "Использование: $0 <директория>"
    exit 1
fi

directory=$1

# Поиск пустых текстовых файлов
find "$directory" -type f -empty -print
```
```bash
xartd0@xartd0-Strix-GL504GW-GL504GW:~/confmirea$ ./10.sh test
test/3.js
test/1.py
test/1ff
test/2.py
```

