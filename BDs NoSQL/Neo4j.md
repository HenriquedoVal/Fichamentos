# BD NoSQL (graph) Neo4j
## Estrutura básica:
~~~
CREATE (:Label{age:31,name:'Nome1',hobbies:['exemplo 1','exemplo 2']}) -[:NomeDoRelacionamento]-> (:Label{name:'Nome2',hobbies:'exemplo 3'})
~~~
Cria dois nós dentro de um label, define atributos (age, name, etc) e estabelece um relacionamento.

## Funções básicas
~~~
MATCH (todos) RETURN todos;
MATCH (variavelQualquer:Label{name:'Nome2'}) RETURN variavelQualquer;
MATCH (X:Label{name:'Nome1'}), (Y:Label{name:'Nome2'}) CREATE (X) -[:nomeDoRelacionamentoASerCriado]-> (Y);
MATCH (variavel1:Label{name:'Nome1'}) -[variavel2:NomeDoRelacionamento]-() DELETE variavel2;
MATCH (var:Label{name:'Nome2'}) DELETE var;
~~~
Respectivamente: consulta tudo, consulta nó especificado, cria um relacionamento entre nós já existentes, deleta um relacionamento e deleta um nó

~~~
MATCH (V:Label{name:'Nome1'}) SET V.age = 35;
MATCH (A:Label{name:'Nome1'}) SET A:NovaLabel;
~~~
Dá um novo valor a um atributo e define nova label
