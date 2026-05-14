# Проект: Реализация простого блокчейна на Java

---

# Введение

## Цель проекта

Изучить принципы работы блокчейна и реализовать его упрощённую версию на Java.

## Задачи

* Изучить предметную область (блокчейн)
* Реализовать базовую архитектуру
* Добавить механизм Proof-of-Work
* Проверить целостность цепочки
* Задокументировать проект
* Визуализировать архитектуру

---

# Исследование предметной области

## Что такое блокчейн

Блокчейн — это:

> Связанная цепочка блоков, где каждый блок содержит hash предыдущего

---

## Основные компоненты

| Компонент     | Описание                 |
| ------------- | ------------------------ |
| Block         | Хранит данные            |
| Hash          | Уникальный идентификатор |
| Previous Hash | Связь между блоками      |
| Nonce         | Для майнинга             |
| Blockchain    | Список блоков            |

---

## Схема блокчейна

```
[Block 1] → [Block 2] → [Block 3]
   hash1      hash2        hash3
     ↓          ↓            ↓
 previousHash previousHash previousHash
```

---


## Proof-of-Work (майнинг)

Идея:

> Найти такой hash, который начинается с N нулей

---

## Иллюстрация 1 (майнинг)

```
nonce = 0 → hash = A93F...
nonce = 1 → hash = B12A...
...
nonce = 5321 → hash = 0000F3AB ✅
```

---

# Техническое руководство

---

## Шаг 1. Создание проекта

Создайте Java-проект:

```
PolyChain
```

Структура:

```
src/
 ├── Block.java
 └── PolyChain.java
```

---

## Шаг 2. Создание класса Block

```java
import java.util.Date;

public class Block {
    public String hash;
    public String previousHash;
    public String data;
    public long timeStamp;
    public int nonce;

    public Block(String data, String previousHash) {
        this.data = data;
        this.previousHash = previousHash;
        this.timeStamp = new Date().getTime();
        this.hash = calculateHash();
    }
}
```

---

## Шаг 3. Хеширование

```java
public String calculateHash() {
    String input = previousHash + timeStamp + data + nonce;
    return StringUtil.applySha256(input);
}
```

---

## Иллюстрация 2 (формирование hash)

```
hash = SHA256(previousHash + timeStamp + data + nonce)
```

---

## Шаг 4. Майнинг блока

```java
public void mineBlock(int difficulty) {
    String target = new String(new char[difficulty]).replace('\0', '0');

    while(!hash.substring(0, difficulty).equals(target)) {
        nonce++;
        hash = calculateHash();
    }
}
```

---

## Шаг 5. Создание блокчейна

```java
import java.util.ArrayList;

public class PolyChain {
    public static ArrayList<Block> blockchain = new ArrayList<>();
    public static int difficulty = 4;

    public static void main(String[] args) {

        blockchain.add(new Block("First block", "0"));
        blockchain.add(new Block("Second block", blockchain.get(0).hash));
        blockchain.add(new Block("Third block", blockchain.get(1).hash));
    }
}
```

---

## Иллюстрация 3 (архитектура классов UML)

```
+------------------+
|      Block       |
+------------------+
| hash             |
| previousHash     |
| data             |
| timeStamp        |
| nonce            |
+------------------+
| calculateHash()  |
| mineBlock()      |
+------------------+

         ▲
         |
+------------------+
|   PolyChain      |
+------------------+
| blockchain       |
| difficulty       |
+------------------+
| main()           |
+------------------+
```

---

## Шаг 6. Проверка целостности

```java
public static Boolean isChainValid() {
    Block currentBlock;
    Block previousBlock;

    for(int i=1; i<blockchain.size(); i++) {
        currentBlock = blockchain.get(i);
        previousBlock = blockchain.get(i-1);

        if(!currentBlock.hash.equals(currentBlock.calculateHash())) {
            return false;
        }

        if(!currentBlock.previousHash.equals(previousBlock.hash)) {
            return false;
        }
    }
    return true;
}
```

---

## Иллюстрация 4 (проверка цепочки)

```
Block2.previousHash == Block1.hash ✔
Block3.previousHash == Block2.hash ✔
```

---

# 4. Результат работы

Программа:

* создаёт блоки
* майнит их
* проверяет цепочку

---

# 5. Модификация проекта 

## Добавленные функции

### 1. Транзакции

```java
class Transaction {
    String from;
    String to;
    double amount;
}
```

---

### 2. Подписи

* добавлена проверка авторства

---

### 3. P2P сеть (эмуляция)

* несколько узлов
* синхронизация цепочек

---

## Иллюстрация 5 (P2P сеть)

```
Node A ←→ Node B ←→ Node C
   ↓          ↓         ↓
 blockchain blockchain blockchain
```

---

## Иллюстрация 6

```
User → Blockchain → Block → Hash
```
---

# Вывод

В рамках проекта:

* изучен блокчейн
* реализована модель
* добавлен майнинг
