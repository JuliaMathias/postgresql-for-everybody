# Building a many-to-many roster

Create the following tables.

```SQL
CREATE TABLE student (
    id SERIAL,
    name VARCHAR(128) UNIQUE,
    PRIMARY KEY(id)
);
```

```SQL
DROP TABLE course CASCADE;
CREATE TABLE course (
    id SERIAL,
    title VARCHAR(128) UNIQUE,
    PRIMARY KEY(id)
);
```

```SQL
DROP TABLE roster CASCADE;
CREATE TABLE roster (
    id SERIAL,
    student_id INTEGER REFERENCES student(id) ON DELETE CASCADE,
    course_id INTEGER REFERENCES course(id) ON DELETE CASCADE,
    role INTEGER,
    UNIQUE(student_id, course_id),
    PRIMARY KEY (id)
);
```

## Course Data

You will normalize the following data (each user gets different data), and insert the following data items into your database, creating and linking all the foreign keys properly. Encode instructor with a role of 1 and a learner with a role of 0.

```bash
Moya, si106, Instructor
Anayah, si106, Learner
Brad, si106, Learner
Chi, si106, Learner
Nikodem, si106, Learner
Jebadiah, si110, Instructor
Basile, si110, Learner
Celia, si110, Learner
Janani, si110, Learner
Maximus, si110, Learner
Lina, si206, Instructor
Calen, si206, Learner
Harlie, si206, Learner
McCaulley, si206, Learner
Oluwatimilehin, si206, Learner
```

You can test to see if your data has been entered properly with the following SQL statement.

```SQL
SELECT student.name, course.title, roster.role
    FROM student
    JOIN roster ON student.id = roster.student_id
    JOIN course ON roster.course_id = course.id
    ORDER BY course.title, roster.role DESC, student.name;
```

The order of the data and number of rows that comes back from this query should be the same as above. There should be no missing or extra data in your query.

## Solution

```SQL
INSERT INTO student (name) VALUES('Moya');
INSERT INTO student (name) VALUES('Anayah');
INSERT INTO student (name) VALUES('Brad');
INSERT INTO student (name) VALUES('Chi');
INSERT INTO student (name) VALUES('Nikodem');
INSERT INTO student (name) VALUES('Jebadiah');
INSERT INTO student (name) VALUES('Basile');
INSERT INTO student (name) VALUES('Celia');
INSERT INTO student (name) VALUES('Janani');
INSERT INTO student (name) VALUES('Maximus');
INSERT INTO student (name) VALUES('Lina');
INSERT INTO student (name) VALUES('Calen');
INSERT INTO student (name) VALUES('Harlie');
INSERT INTO student (name) VALUES('McCaulley');
INSERT INTO student (name) VALUES('Oluwatimilehin');
```

```SQL
INSERT INTO course (title) VALUES('si106');
INSERT INTO course (title) VALUES('si110');
INSERT INTO course (title) VALUES('si206');
```

```SQL
INSERT INTO roster (student_id, course_id, role) VALUES(1, 1, 1);
INSERT INTO roster (student_id, course_id, role) VALUES(2, 1, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(3, 1, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(4, 1, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(5, 1, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(6, 2, 1);
INSERT INTO roster (student_id, course_id, role) VALUES(7, 2, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(8, 2, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(9, 2, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(10, 2, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(11, 3, 1);
INSERT INTO roster (student_id, course_id, role) VALUES(12, 3, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(13, 3, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(14, 3, 0);
INSERT INTO roster (student_id, course_id, role) VALUES(15, 3, 0);

```
