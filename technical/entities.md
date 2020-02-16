---
description: Описание всех сущностей системы
---

# Entities

## Gluon parts

### Client

* должен иметь Ethereum кошелёк для работы с биржей. Кошелёк необходим для инициации сделки и для получения возможных выплат в случаи нарушений со стороны исполнителя

### Relayer

* исполнитель должен поддержать 
* кто угодно может стать исполнителем поддержав необходимый функциона взаимодействия со смарт-контрактом

### Validators Network

* работает на Tendermint
* поднимает ноды блокчейнов чтобы следить за выполнением сделки
* подписывает все опирации в системе
* кто угодно может стать валидатором имея необходимое кол-во токенов системы
* каждый валидатор следит за индексом цены
* валидатору необходимо иметь достаточное кол-во токенов системы для подтверждения транзакции

## Ethereum parts

### Смарт-контракт

* использовается в сети Ethereum
* для создания аукциона
* для блокировки страхового депозита

###  Страховой депозит

* Стаховой депозит размещается в смарт-контракте эфиреума
* В момент акциона блокируется сумма равная сумме сделки
* После завершения сделки страховой депозит полностью разблокируется
* Если были нарушены условия сделки часть или полностью страхового депозита идёт на штрафы
* Необходим чтобы исполнитель участвовал в сделке
* Исполнитель может свободно заводить и выводить деньги из страхового депозита
* После блокировки страхового депозита разблокировка производится валидаторами

## Tendermint parts

### Аукцион

* создаётся заказчиком
* чтобы участвовать в аукционе исполнители должны заблокировать страховой депозит
* для выполнения сделки выбирает исполнитель в соответствии с рейтингом, штрафами и объёмом гарантированного депозита

###  Рейтинг

* влияет на вероятность выиграть аукцион
* присваивается исполнителю после завершения сделки
* чем короче время сделки и чем ближе к индексной цене тем больше присваиваемый рейтинг

### Индексная цена

* средневзвешенная цена токена по ряду бирж
* определяется каждым валидатором по единой формуле
* является референсом для начисления рейтинга и штрафов

###  Штрафы

* снимаются с заблокированного страхового депозита за
  * неисполнение сделки
  * за превышение максимального времени
  * за выход цены исполнения за допустимый коридор цены исполнения
* перечисляются клиенту в ETH на кошелёк клиента

### Штрафы валидатору системы

* валидаторы могут штрафовать друг друга за неверную работу
* оштрафованный валидатор лишается части своих Gluon токенов
