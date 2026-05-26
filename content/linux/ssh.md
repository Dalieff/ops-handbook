+++
title = "ssh"
tags = ["linux","ssh"]
date = "2026-02-15"
+++  

{{< notice info >}}
*Теория — это когда все известно, но ничего не работает. Практика — это когда все работает, но никто не знает почему. Мы же объединяем теорию и практику: ничего не работает… и никто не знает почему!*
{{< /notice >}}

`генерация ключей ssh`  
`старый RSA 4096 и новый ed25519 алгоритмы шифрования`
```go
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
ssh-keygen -t ed25519 -C "your_email@example.com"
```

`корпирование ключей ssh`
```go
ssh-copy-id "user@hostname"
ssh -p port "user@hostname"  /*-p задает нестандартный порт*/
```

`использование приватного ключа`
```go
ssh -i ~/.ssh/id_rsa user@hostname
```

`копирование с помощью scp`
```go
scp localfile.txt "user@hostname":/remote/path/  /*копирует локальный файл на сервер*/
scp -r localdir/ "user@hostname":/remote/path/  /*копирует локальную директорию на сервер*/
scp "user@hostname":/remote/file.txt ./localfolder/  /*копирует файлы с сервера на локальный ПК*/
```

`проброс портов`
```go
ssh -L 8080:localhost:80 user@hostname /*от "себя" порт 8080 на сервер порт 80*/
ssh -R 9090:localhost:3000 user@hostname /*от сервера порт 9090 к "себе" порт 3000*/
```

`подключение через Jump host`
```go
ssh -J user@gateway user@target
```

`SSH verbose`
```go
ssh -v user@hostname /*режим отладки, вывод информации при подключении, -vvv - более подробная информация*/
```

`SSH в фоновом режиме`
```go
ssh -fN user@hostname /*в основном используется при пробросе портов*/
-f — фон
-N — не выполнять команду, только туннель
```