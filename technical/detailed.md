---
description: детальное описание системы
---

# Detailed Schemes

## General Exchange Scheme



![&#x41E;&#x431;&#x449;&#x430;&#x44F; &#x441;&#x438;&#x441;&#x442;&#x435;&#x43C;&#x430; &#x440;&#x430;&#x431;&#x43E;&#x442;&#x44B; &#x441;&#x438;&#x441;&#x442;&#x435;&#x43C;&#x44B;](../.gitbook/assets/main-scheme.png)

Система построена на взаимодействии между всеми участниками системы через Ethereum смарт-контакты. 

* Клиент создает запрос на совершение обмена \(аукцион\)
* Исполнители конкурируя между собой получают возможность совершить обмен
* Сеть валидаторов следит за верным совершение сделки

## Validator Network Scheme





![&#x41F;&#x440;&#x438;&#x43D;&#x446;&#x438;&#x43F; &#x440;&#x430;&#x431;&#x43E;&#x442;&#x44B; &#x441;&#x435;&#x442;&#x438; &#x432;&#x430;&#x43B;&#x438;&#x434;&#x430;&#x442;&#x43E;&#x440;&#x43E;&#x432;](../.gitbook/assets/tendermint.png)

Сеть валидаторов реализована на Tendermint протоколе. Для валидации используется алгоритм BFT \(Byzantine fault tolerance\). Каждый валидатор наблюдает за всеми блокчейнами между которыми возможен обмен токенов. Для корректного наблюдения за блокчейном достаточно использовать две функции: высота и информацию о транзакции. Для завершения транзакции сеть валидаторов должна проверить состояния source and destination blockchain и что отношение source токен к destination токен соответствует индексной цене на данный момент.

{% hint style="info" %}
Подробнее про [сеть валидаторов](entities.md#validators-network)
{% endhint %}

## Simple exchange scheme

![&#x423;&#x43F;&#x440;&#x43E;&#x449;&#x435;&#x43D;&#x43D;&#x430;&#x44F; &#x441;&#x445;&#x435;&#x43C;&#x430; &#x441;&#x43E;&#x432;&#x435;&#x440;&#x448;&#x435;&#x43D;&#x438;&#x44F; &#x43E;&#x431;&#x43C;&#x435;&#x43D;&#x430;](../.gitbook/assets/simple-change.png)

1. Исполнитель блокирует свои средства в ETH на смарт-контакте обменника в страховом депозите. Сумма заблокированных средств должна превышать сумму обмена с учётом коэффициента коллитерации \(сейчас x1.2\)
2. Клиент отправляет свои токены прямо на адрес Исполнителя. Если Исполнитель не выполнит свои обязательства клиенту будут начислена компенсация в виде всего заблокированного депозита Исполнителя
3. После совершения обмена Исполнитель переводит Клиенту destination токен в соответствии с курсом обмена.
4. Сеть валидаторов проверяет исполнение сделки и разблокирует страховой депозит Исполнителя.

## Amends scheme

![&#x41E;&#x431;&#x43C;&#x435;&#x43D; &#x441; &#x43A;&#x43E;&#x43C;&#x43F;&#x435;&#x43D;&#x441;&#x430;&#x446;&#x438;&#x435;&#x439; &#x43A;&#x43B;&#x438;&#x435;&#x43D;&#x442;&#x443;](../.gitbook/assets/set-fine.png)

В случаи если Исполнитель не произвёл обмен за установленное время или вернул Клиенту кол-во destination токенов не соответствующее индексу цены, сеть валидаторов выписывает штраф Исполнителю и компенсацию разницы в цене клиенту. Таким образом клиент всегда уверен что либо будет совершён обмен по наилучшей цене исполнения в данный момент времени. Либо он получит дополнительную компенсацию, которая покрывает разницу в цене между Индексной ценой и ценой обмена.

## Sequence Diagram

![Sequence Diagram](https://lh4.googleusercontent.com/WiXEgiAO8WZR1Mk6xyR5IrcSE4-rpc8jnfZ6YANSBR-93zUpppMrHjvJ8GtJv0qCvwYHSH2NCz7VlbKrBCVnbWRjCAlKFjHhR8am4PR1rjlaxDH12zle13WIEccUlq5kbgbLmcTj)



1. Клиент, используя Web-сайт формирует заказ на обмен криптовалюты - указывает исходную и результирующую валюты, объем и кошелек для приема результирующей валюты.
2. В Ethereum смарт-контракт отправляется транзакция на создание нового аукциона, проставляются параметры в соответствии с запросом клиента. 
3. Исполнитель видит Ethereum event о новом аукционе. Обращается к делегату для подписи своего предложения \(bid\). Делегат возвращает подписанное и спрятанное \(sealed\) предложение исполнителя. 
4. Исполнитель создает Ethereum транзакцию на запись своего подписанного предложения для действующего аукциона в смарт-контракт. В предложении Исполнителя также указан адрес кошелька для исходной валюты.
5. По окончанию периода bidding, сеть делегатов получает список всех спрятанных bid и находит победителей в соответствии с распределением объема и рейтингов Исполнителей. В этот момент проверяется уровень коллитерации заблокированных средств ETH Исполнителей. Предложения Исполнителей с недостаточным уровнем коллитерации не учитываются.
6. Делегат переводит аукцион в состояние исполнения. На Web-сайте клиенту отображаются исходные адреса кошельков Исполнителей. 
7. Делегаты ожидают поступление средств на кошельки Исполнителей, как только средства поступают, создается Ethereum транзакция, где смарт контракт фиксирует поступление средств конкретному Исполнителю. 
8. Исполнитель видит поступление средств через Ethereum event, либо напрямую через блокчейн кошелька. Производит обмен криптовалют и отправляет результирующую валюту на кошелек клиента.
9. При наступлении времени окончания аукциона, делегаты проверяют наличие незавершенных транзакций \(mempool etc…\) на кошельке клиента. Если все операции завершены, то аукцион закрывается. Депозит Исполнителя используется для начисления комиссий делегатам. В случае невыполнения обязательств Исполнителя по данному аукциону \(либо частичного выполнения\), вычитаются средства Исполнителя на счет клиента \(использую внутреннюю цену делегатов\).
10. Факт окончания аукциона фиксируется в Ethereum смарт-контракте. Клиенту отправляется отчет о выполнении обмена.
