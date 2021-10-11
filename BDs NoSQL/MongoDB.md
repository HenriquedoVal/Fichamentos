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

Mongo instalado na máquina

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
gera dois documentos, chama-se bulkwriter

> db.collection.save()
realiza a atualização ou a inserção caso não exista. Observar que precisa passar todo o id dentro do save
