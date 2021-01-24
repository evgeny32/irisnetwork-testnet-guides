# IRIS Network Testnet. Руководство по созданию ноды
Все действия проводятся на сервере с OS Ubuntu 20.04

### 1) Создадим пользователя
```bash
$ adduser <username>
```
Вводим пароль (сохраняем его). Вводим пароль еще раз. На остальные вопросы жмем Enter

### 2) Добавим пользователя в группу sudo
```bash
$ usermod -aG sudo <username>
```

### 3) Зайдем под новым пользователем
```bash
$ su <username>
```

### 4) Установим go
При запросе пароля ввести пароль, который сохраняли на шаге 1
```bash
$ cd /usr/local
$ sudo wget https://golang.org/dl/go1.15.7.linux-amd64.tar.gz
$ sudo tar -C /usr/local -xzf go1.15.7.linux-amd64.tar.gz
```
Переменные окружения
```bash
$ export PATH=$PATH:/usr/local/go/bin
$ mkdir -p $HOME/go/bin
$ echo "export GOPATH=$HOME/go" >> ~/.bashrc
$ source ~/.bashrc
$ echo "export GOBIN=$GOPATH/bin" >> ~/.bashrc
$ source ~/.bashrc
$ echo "export PATH=$PATH:$GOBIN" >> ~/.bashrc
$ source ~/.bashrc
```
Проверяем
```bash
$ go version
```
Должно вывести `go version go1.15.7 linux/amd64`

### 5) Установим git, make, gcc, tmux
```bash
$ sudo apt update
$ sudo apt -y install git make gcc tmux
```

### 6) Iris
```bash
$ cd $HOME
$ git clone --branch v1.0.0-rc0 https://github.com/irisnet/irishub
$ cd irishub
$ make install CGO_ENABLED=0
```
Проверяем
```bash
$ iris version
```
Должно вывести `1.0.0-rc0`

### 7) Запускаем ноду
```bash
$ iris init <your-name>-<pgp-id> --chain-id=bifrost-2
```
`<your-name>` - ваше имя, ник (например как в keybase)

`<pgp-id>` - PGPkey из Keybase (16 символов, например C6BA3F322D87843A)

```bash
$ curl -o ~/.iris/config/config.toml https://raw.githubusercontent.com/irisnet/testnets/master/nyancat/config/config.toml
$ curl -o ~/.iris/config/genesis.json https://raw.githubusercontent.com/irisnet/testnets/master/bifrost/phase-2/genesis.json
```
Редактируем config
```bash
$ nano $HOME/.iris/config/config.toml
```
Руководство по nano (популярный тектовый редактор в linux) https://help.ubuntu.ru/wiki/nano

Ищем строку

`moniker = "CHANGE THIS TO YOUR MONIKER"`

Заменяем на 

`moniker = "<your-name>-<pgp-id>"` по аналогии с шагом 7)


Ищем строку

`persistent_peers = "c87675bcaeb72a1f93dbf7d74f7b425690a994e3@34.80.202.172:26656"`

Заменяем на

`persistent_peers = "a246f5ea3055f7507c63b9ea50dbf5d401537b3a@34.80.22.255:26656"`


Ищем строку

`db_backend = "leveldb"`

Заменяем на

`db_backend = "goleveldb"`

Если не получилось разобраться с nano:

Качаем WinScp

Подключаемся к серверу как root

Идем по пути `/home/<username>/.iris/config`
  
Открываем файл `config.toml` в текстовом редактор и изменяем в соответствии с указаниями выше.

Запускаем ноду

```bash
$ tmux
$ iris start
```

Что бы выйти их tmux `ctrl+b затем жмем d`

Нода продолжить крутиться, что бы посмотреть `tmux attach -t 0`

###  8) Прокачаемся до валидатора
Создать новый кошелек
```bash
$ iris keys add mykey
```
Вводим пароль (сохраняем его). Сохраняем ВЕСЬ вывод команды.

Идем в офф чат https://app.element.io/#/room/!bmimZgJrUWSmxqQEmG:matrix.org (при необходимости регаемся)

Пишем, что бы вам прислали монет, указываете свой адрес из предыдущей команды (address)

Теперь надо дождаться, пока нода синхронизируется с сетью. Что бы узнать, вводим команду
```bash
$ iris status | jq .SyncInfo.catching_up
```

Если выводит `false` - нода синхронизировалась, можно дити дальше

Если выводит `true` - ждем, синхронизация идет ~1,5 часа.

> Если выводит много символов, копируем вывод в буфер. Идем по ссылке https://jsonformatter.org/
> Вбиваем скопированный текст в левое поле. В правом поле ищем `catching_up`, смотрим значение.

#### Если `catching_up = false`
Идем в сканер https://www.irisplorer.io/
Вводим адрес кошелька, смотрим баланс. Если больше нуля - продолжаем. Если 0 - ждем


```bash
Продолжение будет, когда автор получит тестовые токены на кошелек.
```
