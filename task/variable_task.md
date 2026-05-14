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

![Схема блокчейна](https://viewer.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=%D0%94%D0%B8%D0%B0%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B1%D0%B5%D0%B7%20%D0%BD%D0%B0%D0%B7%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F.drawio&dark=auto#R%3Cmxfile%3E%3Cdiagram%20name%3D%22%D0%A1%D1%82%D1%80%D0%B0%D0%BD%D0%B8%D1%86%D0%B0-1%22%20id%3D%22V_3oBCmtlqGwxrVDRCjS%22%3E7Vhdk5owFP01zLQP25FEPnxc1F07s%2Fvkw7Z92YkQgU4gNAaF%2FvoGCF%2BiVjtiacd9YHNPbm4uuefmqAqcBskzQ5H3Sh1MFDByEgXOFABUoE%2FEvwxJC0QHsABc5jvSqQaW%2Fk8swZFEY9%2FBm5Yjp5RwP2qDNg1DbPMWhhiju7bbmpL2rhFycQdY2oh00Tff4V6BmsCo8QX2Xa%2FcWS1fOECls3yTjYccumtAcK7AKaOUF6MgmWKSHV55LsW6pyOzVWIMh%2FycBUYwWSzeFrPX923MvhmjFxZ%2FfjgQRUIbnpZnwGgcOjgLM1KgtfN8jpcRsrPZnai6wDweEGGpYrj2CZlSQlm%2BFkIIrCeRklVE3SISy6gK0InY0lqJgctzjwJYU5FKMwH9R0zLiYdNTpFH4QDGUZIvE8XP98tA5q4%2BAE1TwDTzaAw%2BFjvIUNWe4pUmava08rE1z59ZJLXMRxxpkVI7TQE3Us9fDjOOk8YpykI8YxpgzlLh4jW4okti7GpeqSVZyihjacvugdJEktVuFbkuvBjI2l%2FCA7MIjJ1OO3SZQWNm4xPBxh0GZWGX0qSMe9SlISLzGrXaHKt9XiiNJLO%2BY85TeUOgmNM270SWLP0i1%2BfG18z4pJXmLGlOzlJpyTsFMRfzUwcEDxeUYYK4v20f2tXLM%2B67TTVTAz21qWpep00fNUVkbs7yp3mLBoWXN%2BikrwY1%2BmbAxDBnA2bAaIDFruavfx2Duy4f02UwAF2G2vhGujy5oi6r8H8UZv1vCnN5pHdlbipz7y16xmW936K9KbPa%2B4ez30uzrLhdranLBNf534HKybWbCIUXseVYoNVJTqiHi19hRRoduE2f21ClR13X7rp%2BTNfhAHRdM271fVu%2F60b3juidAmdcBvsU6E83BvCV7h%2FQDXC4%2BEPQjX2q%2FIluCLP%2BCTifa%2FyQDue%2FAA%3D%3D%3C%2Fdiagram%3E%3C%2Fmxfile%3E)
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
