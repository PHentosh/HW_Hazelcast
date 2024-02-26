# Homework_SWA

Увесь проект запескається черех docker-compose

Щоб запустити проект:
```
$> docker-compose build
$> docker-compose up -d
```

Щоб вимкнути все:
```
$> docker-compose down
```


Для завдань 1 та 2 я просто запустив 3 докер контейтера з імаджем hazelcast і вказав првильні порти для port-forwarding

## Завдання 3

У папці ```task-1``` лежить файл з кодом для запису 1000 повідомлень в distributed_map та Dockerfile для збирання цього коду в докер.
Також цей код створює мапи для наступниого завдання

### Розподіл даних по нодах

Можна бачити, що дані розмодаляються рівномірно по всіх 3 нодах

![data_on_nodes](task-1/3_instance.png)


Якщо вимкнути 1 ноду, дані розподіляться по тих двох, що залишились

![data_on_nodes](task-1/2_instance.png)

Якщо ж вимкнути ще одну, то всі дані будуть зберігатись на тій одній, що залишилась. Втрати даних не спостерігається

![data_on_nodes](task-1/1_instance.png)


## Завдання 4

У цьому завданні я запустив додатково по 3 докер контейнера для коного з блокувань, з кодом, що був у офіційних прикладах.

Вийшли досить цікаві результати. Оптимістичне блокування показало себе як і очікувалось, без жодних втрат, а ось песимістичне блокувння призвело до невеликох страти данних. На диво програма що працбвала взагалі без блокувань також змогла отримати всі дані без втрат. 

![client_1](client_1/without.png)
![client_1](client_2/pesimistic.png)
![client_1](client_3/optimistic.png)



## Завдання 5

У цьому завданні потрібно було сконфігурувати bounded-queue. Для цього при збирання контейтера з hazelcast я заміняю файли з конфігами на власні з тими значеннями, що мені потрібні.

Такми чином отримано чергу з лімітом у 10 значень.
Якщо в таку чергу записувати значення без зчитування, то вона перповниться і наступні значення не зможуть записатись, поки не будуть зчитані старі.

Це видно на наступному скріншоті, де звичайний інтервал між записами - 3с. але коли черга переповниться, потік буде чекати поки не зможе записати значення.

![fill](producer/limit_reached.png)

Якщо ж просто записувати і зчитувати значення з черги одним читачем, ми побачимо звичайну поведінку.

![common](first_reader/only_one_reader.png)

Якщо ж читачів буде більше ніж один, вони по черзі вичитуватимуть значення.


![common](first_reader/first_reader.png)

![common](second_reader/secomd_reader.png)