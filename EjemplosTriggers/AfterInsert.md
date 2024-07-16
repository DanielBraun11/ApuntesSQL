# Ejemplo TRIGGER AFTER INSERT
```sql
DROP TABLE IF EXISTS members;
CREATE TABLE members (
id INT AUTO_INCREMENT,
name VARCHAR(100) NOT NULL,
email VARCHAR(255),
birthDate DATE,
PRIMARY KEY (id)
);
DROP TABLE IF EXISTS reminders;
CREATE TABLE reminders (
id INT AUTO_INCREMENT,
memberId INT,
message VARCHAR(255) NOT NULL,
PRIMARY KEY (id , memberId)
);
```

La siguiente instrucción crea un trigger AFTER INSERT que inserta un recordatorio en la tabla **reminders** si la fecha de nacimiento del miembro en la tabla **members** es NULL.
El identificador insertado en la tabla **reminders** corresponde al campo **id** de **members**:

```sql
DELIMITER $$
CREATE TRIGGER after_members_insert
AFTER INSERT
ON members FOR EACH ROW
BEGIN
IF NEW.birthDate IS NULL THEN
INSERT INTO reminders(memberId, message)
VALUES(new.id,CONCAT('Hi ', NEW.name, ', please update your date of birth.'));
END IF;
END$$
DELIMITER ;
```

Lo probamos, insertando dos filas en la tabla members:
```sql
INSERT INTO members(name, email, birthDate)
VALUES
('Daniel Braun', 'danielbraun@example.com', NULL),
('Diego Arenas', 'diegoarenas@example.com','2000-01-01');
```

y optenemos usando:
```sql
/* Obtener los registros de la tabla members */
SELECT * FROM members;
```

foto 1

```sql
/* Obtener los registros de la tabla reminders */
SELECT * FROM reminders;
```

foto 2

Insertamos dos filas en la tabla de **members**. Sin embargo, solo la primera fila tiene un valor de fecha de nacimiento NULL, por lo tanto,
el disparador insertó solo una fila en la tabla de **reminders**.


