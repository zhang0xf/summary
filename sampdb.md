# sampdb

### Reference book :《MySQL技术内幕》第五版

### Reference website : [MySQL](http://www.kitebird.com/mysql-book/)

### Create DB:

1. `tar -zxf sampdb.tar.gz`
2. `cd sampdb`
3. `mysql -u root -p < createdb.sql`
4. **WAIT FOR ENTER PASSWORD**
5. `show databases;`
6. `mysql -u root -p < adduser.sql`
7. `select * from user;`

### create tables:

1. `mysql sampdb < create_student.sql -u root -p`
2. `mysql sampdb < create_absence.sql -u root -p`
3. `mysql sampdb < create_grade_event.sql -u root -p`
4. `mysql sampdb < create_member.sql -u root -p`
5. `mysql sampdb < create_president.sql -u root -p`
6. `mysql sampdb < create_score.sql -u root -p`
7. `use sampdb; show tables;`

### insert contents:

1. `mysql sampdb < insert_student.sql -u root -p`
2. `mysql sampdb < insert_absence.sql -u root -p`
3. `mysql sampdb < insert_grade_event.sql -u root -p`
4. `mysql sampdb < insert_member.sql -u root -p`
5. `mysql sampdb < insert_president.sql -u root -p`
6. `mysql sampdb < insert_score.sql -u root -p`
7. `select * from absence;`

### CHECK:

1. `mysql -u root -p < init_all_tables.sh sampdb`
