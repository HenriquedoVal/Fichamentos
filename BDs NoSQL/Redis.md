# BD NoSQL (key-value) Redis

BD mais utilizado para cache e sessão de usuário pela simplicidade em temporizar a expiração do dado.
Existe o sandbox do Redis, porém sem a opção de console, usar então https://try.redis.io/.
O Redis tem o conceito de database também, porém não é explorado neste ultimo app, ele parte direto para a parte de manipulação de dados.
~~~
SET user1:name 'Nome1'
GET user1:name
SET user '{"name":"Nome2","age":10}'
SET user2:name 'Nome3' EX 10
EXISTS user2:name
~~~

Listas:
~~~
LPUSH user2:cardeais 'norte', 'sul', 'leste', 'oeste'
LINDEX user2: cardeais 0
LRANGE user2:cardeais 0 2
~~~
LPUSH faz o *insert* no índice 0

Para retornar o tipo de dado, o tempo de expiração, retirar o tempo de expiração e deletar o dado
~~~
TYPE user1:name
TTL user1:name
PERSIST user1:name
DEL user1:name
~~~

Por fim, neste app, um comando errado retorna todas os possíveis comandos. Com essa lógica, a maioria se torna intuitivo. 
