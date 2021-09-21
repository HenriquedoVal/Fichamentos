# BD NoSQL (wide-column) Cassandra 
Playground: https://katacoda.com/datastax/courses/cassandra-try-it-out/try-cql
Lembrar que os valores que são apresentados nas consultas como nulos não são salvos dessa forma, ao contrário dos bd relacionais
~~~
CREATE KEYSPACE nomeDaKeyspace WITH replication = {'class':'SimpleStrategy', 'replication_factor': 1};
USE nomeDaKeyspace;
CREATE COLUMNFAMILY tabela (name TEXT PRIMARY KEY, age int);
INSERT INTO tabela (name, age) VALUES ('Nome1',30);
INSERT INTO tabela JSON '{"name":"Nome2"}';
SELECT age, WRITETIME(age) FROM tabela;
SELECT JSON * FROM tabela;
UPDATE tabela SET age=35 WHERE name='Nome2';
ALTER COLUMNFAMILY tabela ADD country text;
UPDATE tabela SET country='BR' WHERE name='Nome1';
SELECT WRITETIME(age), WRITETIME(country) FROM tabela WHERE name='Nome1';
DELETE FROM tabela WHERE name='Nome2';
~~~
Linguagem CQL - Cassandra Query Language
