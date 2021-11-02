# BD NoSQL (Multi-style) MongoDB

Não recomendado quando for necessários relacionamentos/joins, quando forem importantes propriedades ACIDs e transações, ou para dados financeiros.

#### Boas práticas

- Preferir embedding quando a 'relação' dos documentos for one-to-one ou one-to-few, referência quando for one-to-many e many-to-many
- Evitar documentos muito grandes
- Usar nomes, campos e objetivos curtos
- Analisar as queries usando explain()
- Atualizar apenas campos alterados
- Evitar negações em queries
- Lista/array não podem crescer sem limites

## Prática

Mongo instalado na máquina.

~~~MongoDB
> mongo --host 127.0.0.1:27027 -u usuario -p senha
localhost e porta padrão do mongo

> show databases;
alguns são nativos do cluster

> use nome-do-data-base;
caso o nome do database ainda não exista ele o cria

> db.createCollection("nome-da-collection", {capped: true, max: 2, size: 2});
criação da collection de forma explícita, forma esta que permite a configuração do capped (impor limite à collection), max (quantidade de documentos)
a terceira inserção nessa collection vai substituir a mais antiga

> db.nome-da-collection.insertOne({"name": "Teste 1"});
insere dados

> db.nome-da-collection.find({})
SELECT *

> db.collection-que-ainda-não-existe.insertOne({"age":10})
cria nova collection de forma implícita e adiciona um documento com o dado

> db.nova_collection.insert{[{"name": "José", "idade": 36},{"Cidade": "Poá"}]}
cria a collection e gera dois documentos inserindo os dados; chama-se bulkwriter

> db.collection.save()
realiza a atualização ou a inserção caso não exista. Observar que precisa passar todo o id dentro do save

> db.collection.update({"chave-p-identificar-o-doc-a-ser-atualizado": "valor-dessa-chave"}, {$set: {"chave": "valor"}});
> db.collection.update({"chave-p-identificar-o-doc-a-ser-atualizado": "valor-dessa-chave"}, {$set: {"chave": "valor"}, {multi: true});
todos os documentos cujas chaves forem identificadas serão atualizadas

> db.collection.updateMany({"chave-identificadora-contida-em-varios-documentos": "valor-da-chave"}, {$set: {"chave-a-ser-inserida":"valor"}});

> db.collection.find({"chave": "valor"}).limit(1)
retorna somente o primeiro documento contendo essa chave e valor
> db.collection.find({"chave": "valor", "outra-chave": "valor-correspondente"})
para busca específica. quantas chaves forem necessárias
> db.collection.find({"chave": {$in: [30, 41]}})
retorna os documentos que contém na chave "chave" os valores 30 e 41
> db.collection.find({$or: [{"chave": "valor"}, {"outra-chave": "outro-ou-mesmo-valor"}]})
retorna ambos se ambos forem verdadeiros
> db collection.find({"chave": {$lt: 55}})
retorna todos o documentos com o valor para "chave" que sejam limitados ao valor de até 55 (<55)
> db collection.find({"chave": {$lte: 55}})
(<= 55)

> db.collection.deleteOne({"chave": "valor"})
deleta apenas o primeiro documento com esse conjunto chave-valor
> db.collection.deleteMany({"chave": "valor"})
deleta todos
~~~

## Performance e índices

~~~MongoDB
> for(var=i; i<10000; i++){db.collection.insert({"Nome": "cliente"+i, "age":i})}
gera 10000 documentos com esses dados atribuindo à var i o valor conforme o loop

> db.getCollection("collection").count({})
retorna a contagem de documentos na collection (10000 nesse caso)

> db.getCollection("collection").find({_id: {ObjectId('aklsdjflçajksd')}).explain(true)
explain faz análise da query, trás alguns dados da consulta (tempo de exec, docs analisados, uso de índice...)
será possível ver nesse caso que somente um doc foi analisado
> db.getCollection("collection").find({name: "cliente0"}).explain(true)
mostra agora 100000 doc analisados

> db.getCollection("collection").createIndex({name : 1}, {"name": "idx_name"})
cria índices para os docs
> db.getCollection("collection").find({name: "cliente0"}).explain(true)
ao rodar novamente esse comando, o resultado da análise retornará apenas um doc examinado devido ao uso de índice
~~~

## Agregações

A sugestão de estudo é usando o sample dataset do Mongo Cloud (Atlas)
~~~
> db.getCollection("restaurants").count({})
retornará mais de 23000 docs

> db.getCollection("restaurants").distict("cuisine")
retorna todos os possíveis valores para esse campo

> db.getCollection("restaurants").aggregate([{$group: {_id: {"$cuisine", total: {$sum: 1}}}}])
parâmentros do aggregate dentro de um array, pois podem ser infinitos
nesse caso, retorna o total de restaurantes com uma determinada cozinha

> db.getCollection("restaurants").aggregate([{$group: {_id :{"$cuisine", total: {$sum: 1}, id_maximo: {$max: "$restaurant_id"}}}}])
retornara a query dos campos _id, total e id_maximo. vale o mesmo para $min e $avg (cuidado com o tipo de dado no avg - string ou number)

> db.getCollection("restaurants").aggregate([{$addFields: {"teste": true}}])
adiciona um campo ao resultado da query, um novo find({}) retornará os campos normais

> db.getCollection("restaurants").aggregate([{$match: {$and: [{cuisine: "American"}, {borough: "Brooklyn"}]}}])
$and: trás todos docs cujos ambos valores sejam os específicados
> db.getCollection("restaurants").aggregate([{$match: {$or: [{cuisine: "American"}, {borough: "Brooklyn"}]}}])
$or: um ou outro
~~~
Ainda os operadores de comparação
$gt: >; $gte: >=; $lt: <; $lte: <=; $nte: !=; $eq: ==; 
