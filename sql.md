### Задания по SQL

**0. Импорт БД**
В таблице OP_ART имеются разные значения с одинаковыми индексами ID_OPART (18 и 28), что нарушает условия первичного ключа на уникальность. Значения исправлены.

**1. Напишите SELECT, который выводит номер операции, дату операции, сумму операции с сортировкой по возрастанию по полю с первичным ключом(Primary Key) из таблицы «Операции».**
```
SELECT t1.ID_OP, t1.OPDATE, t1.OPSUM FROM OPERATIONS t1
ORDER BY t1.ID_OP ASC
```
**2. Напишите SELECT, который выводит все записи из таблицы «Операции», с пустым значением по ключевому полю с таблицей «Контрагенты» или отрицательным значением поля суммы операции.**
```
SELECT * FROM OPERATIONS t1
WHERE t1.ID_CONTR IS NULL OR t1.OPSUM < 0
```
**3. Напишите SELECT для таблицы «Операции», который выводит только уникальные записи ключевого поля таблицы «тип операций»**
```
SELECT DISTINCT t1.ID_TOP FROM OPERATIONS t1
```
**4. Напишите SELECT для таблицы «Товары» , который выводит количество для каждой единицы измерения (шт/кг)**
```
SELECT t1.UNIT, COUNT(1) FROM ARTICLE t1
GROUP BY t1.UNIT
```
**5. Напишите SELECT для таблицы «Товары», который выведет текст «HELLO WORLD!» в строке, в которой значение поля с целочисленным типом данных меньше тысячи (1000)**
```
SELECT
t1.*,
case when t1.ID_ART < 1000 then 'HELLO WORLD!' END as HW
FROM ARTICLE t1
```
**6. Напишите SELECT который выводит наименование типа операции, номер операции и дату операции по товарам, в наименовании которых есть слово «паста»**
```
SELECT t4.NAME, t3.NUMBER, t3.OPDATE FROM OP_ART t1
LEFT JOIN ARTICLE t2
on t2.ID_ART = t1.ID_ART
LEFT JOIN OPERATIONS t3
on t3.ID_OP = t1.ID_OP
LEFT JOIN TYPEOP t4
on t4.ID_TOP = t3.ID_TOP
WHERE UPPER(t2.NAME) LIKE '%ПАСТА%'
```
**7. Напишите SELECT, который выведет дату и сумму всех сумм операций с типом 'Расход (продажа)' из таблицы «Операции»**
```
SELECT t1.opdate, sum(t1.opsum) FROM OPERATIONS t1
LEFT JOIN TYPEOP t2
on t2.ID_TOP = t1.ID_TOP
WHERE t2.NAME = 'Расход (продажа)'
GROUP BY t1.opdate
```
**8. Напишите SELECT, который выводит из таблицы «Товары»: наименование товара, цену покупки, цену продажи и процент % на который отличаются цены.**
```
SELECT
    t1.NAME,
    t1.PRICEBUY,
    t1.PRICESALE,
    ROUND((1 - t1.PRICEBUY/t1.PRICESALE) * 100, 2) as PRICEDIFF
FROM ARTICLE t1
```
**9. Напишите SELECT который выдаёт максимальное значение поля opsum, из таблицы «Товары по операциям», без использования функции max**
```
SELECT t1.OPSUM FROM op_art t1
ORDER BY t1.OPSUM DESC
LIMIT 1
```
**10. Напишите SELECT, который выведет наименование поставщика, его ИНН и адрес из таблицы  «Контрагенты». Значения необходимо вывести одной колонкой с разделителем (точка с запятой), и только по тем поставщикам, по которым существует операции с типом 'Приход Местный'**
```
SELECT
    t1.NAME || ';' || t1.INN || ';' || t1.ADRESS as INFO
FROM
    CONTRACTOR t1
WHERE
    EXISTS(SELECT 1 FROM OPERATIONS s1
        LEFT JOIN TYPEOP s2
        ON s2.ID_TOP = s1.ID_TOP
        WHERE s1.ID_CONTR = t1.ID_CONTR
        AND s2.NAME = 'Приход Местный')
```
**11. Напишите SELECT, который выведет дублированные значения полей с первичным ключом(Primary Key) из таблицы «товары по операциям»**
```
SELECT ID_OPART FROM
    (SELECT ID_OPART, count(1) as cnt FROM OP_ART t1
    GROUP BY ID_OPART)
WHERE
    cnt > 1
```
**12. Напишите SELECT, выдающий из таблицы «товары по операциям» только время у записей, которые относятся к типу 'Расход (продажа)''**
```
SELECT t1.DATETIME FROM OP_ART t1
LEFT JOIN OPERATIONS t2
on t2.ID_OP = t1.ID_OP
LEFT JOIN TYPEOP t3
on t3.ID_TOP = t2.ID_TOP
WHERE t3.NAME = 'Расход (продажа)'
```
**13. Напишите DELETE который удалит из таблицы «Товары» те позиции, по которым нет движения (отсутствуют операции)**
```
DELETE FROM ARTICLE
WHERE
    ID_ART NOT IN (SELECT t2.ID_ART FROM OP_ART t2
                WHERE t2.ID_ART = ID_ART)
```
**14. Напишите DELETE, который удалит из таблицы «Контрагенты» тех поставщиков, инн которых начинается с цифры 30, или вторая цифра кода равна 0.**
```
DELETE FROM contractor
WHERE INN LIKE '30%'
OR CODE LIKE '_0%'
```
**15.  Напишите UPDATE для таблицы «тип операций», который изменит поле автонумерации на единицу и изменит маску последнего номера на '001' у тех записей, где поле автонумерации равно 0.**
```
UPDATE TYPEOP
SET AUTONUM = 1,
LASTNUMBER = '001'
WHERE AUTONUM = 0
```
**16. Напишите UPDATE для таблицы «Операции», который изменит номер у операции за 28.02.2019 на значение последнего номера у соответствующей записи из таблицы «тип операции».**
```
UPDATE operations
SET NUMBER = (SELECT LASTNUMBER FROM typeop t1 where t1.ID_TOP = ID_TOP)
WHERE OPDATE = '28 02 19 0:00'
```
