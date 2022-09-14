## Примеры работы c СУБД Oracle

### Задача 1
~~~~
Создать табличку сотрудников для хранения следующей информации:

id - первичный ключ, целое число. Поле заполнить из сиквенса триггером, если оно пустое
dept - номер отдела
lname - фамилия
fname - имя
mname - отчество
salary - размер заработной платы, число два знака после запятой
begin_date - дата приема, заполнить триггером при вставке, если оно пустое
end_date - дата увольнения

Все поля кроме последнего обязательны
Таблица должна располагаться в табличном пространстве TRN.
~~~~

### Решение
~~~~
-- СОЗДАЛ ТАБЛИЦУ

CREATE TABLE MAV_EMPLOYEE (
ID INTEGER NOT NULL,
DEPT NUMBER(2) NOT NULL,
LNAME VARCHAR2(20 BYTE) NOT NULL,
FNAME VARCHAR2(20 BYTE) NOT NULL,
MNAME VARCHAR2(30 BYTE) NOT NULL,
SALARY NUMBER(8,2) NOT NULL,
BEGIN_DATE DATE NOT NULL,
END_DATE DATE,
PRIMARY KEY(ID)
)
TABLESPACE TRN;

-- СОЗДАЛ СИКВЕНС

CREATE SEQUENCE SEQ_MAV_EMP START WITH 1;

-- СОЗДАЛ ТРИГГЕР

CREATE OR REPLACE TRIGGER TR_MAV_EMPLOYEE
BEFORE INSERT 
ON MAV_EMPLOYEE FOR EACH ROW
BEGIN
IF :NEW.ID IS NULL THEN
    :NEW.ID:= SEQ_MAV_EMP.NEXTVAL;
END IF;
IF :NEW.BEGIN_DATE IS NULL THEN
    :NEW.BEGIN_DATE :=SYSDATE;
END IF;
END;
~~~~

### Задача 2

~~~~
Вставить в таблицу несколько записей с помощью команды INSERT без указания полей ID, BEGIN_DATE
~~~~

### Решение
~~~~
INSERT INTO MAV_EMPLOYEE(DEPT, LNAME, FNAME, MNAME, SALARY)
VALUES (20, 'СОКОЛОВ', 'АЛЕКСЕЙ', 'ВИКТОРОВИЧ', 45265.29);

INSERT INTO MAV_EMPLOYEE(DEPT, LNAME, FNAME, MNAME, SALARY)
VALUES (20, 'ИВАНОВ', 'СЕРГЕЙ', 'ПЕТРОВИЧ', 65265.29);

INSERT INTO MAV_EMPLOYEE(DEPT, LNAME, FNAME, MNAME, SALARY)
VALUES (20, 'КОЗЛОВ', 'ДМИТРИЙ', 'ПЕТРОВИЧ', 75265.29);
~~~~

### Задача 3
~~~~
Дать пользователю user возможность читать данные из таблички сотрудников без возможности видеть поле SALARY
~~~~
### Решение

~~~~~
-- СОЗДАЛ ВЬЮШКУ БЕЗ СТОЛБЦА "SALARY"

CREATE VIEW V_MAV_EMPLOYEE AS
SELECT ID, DEPT, LNAME, FNAME, MNAME,BEGIN_DATE, END_DATE
FROM MAV_EMPLOYEE;

-- ВЫДАЛ ПРАВА НА ЧТЕНИЯ ПОЛЬЗОВАТЕЛЮ user

GRANT SELECT ON V_MAV_EMPLOYEE TO USER;

~~~~
