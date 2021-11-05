# BD NoSQL HBase

Atende ao CP do CAP. Consistência e tolerância das Partições a falhas.

## Prática
Principais comandos no HBase shell

~~~
> list
# lista as tabelas

> create 'funcionarios', 'pessoais', 'funcionais'
# cria a tabela 'funcionarios' com as columnfamilies 'pessoais' e 'funcionais'.

> put 'funcionarios', '1', 'pessoais:nome', 'Maria'
# insere na tabela 'funcionarios', na row-key '1', na columnfamily 'pessoais', na coluna 'nome', o dado 'Maria'. No Hbase o nome da coluna é definido na inserção do dado.

> put 'funcionarios', '1', 'pessoais:cidade', 'São Paulo'
> scan 'funcionarios'
 1 column=pessoais:cidade, timestamp=1636096078762, value=São Paulo
 1 column=pessoais:nome, timestamp=1636095898052, value=Maria

> disable 'funcionarios'
# para manter a integridade dos dados pode-se desabilitar a tabela para fazer alterações evitando que ela seja consultada durante esse período.

> alter 'funcionarios', NAME=>'hobby', VERSIONS=>5
# altera a tabela, cria um novo columnfamily 'hobby' e define que a tabela terá 5 versões.

> enable 'funcionarios'
> put 'funcionarios', '1', 'hobby:name', 'ler livros'
> put 'funcionarios', '1', 'hobby:name', 'pescar'
> scan 'funcionarios'
 1 column=hobby:nome, timestamp=1636128655066, value=pescar
 1 column=pessoais:cidade, timestamp=1636128423474, value=Sao Paulo
 1 column=pessoais:nome, timestamp=1636128393455, value=Maria
# o put insere e, caso o campo já esteja ocupado, atualiza.

> scan 'funcionarios', {VERSIONS=>3}
 1 column=hobby:nome, timestamp=1636128655066, value=pescar
 1 column=hobby:nome, timestamp=1636128633882, value=ler livros
 1 column=pessoais:cidade, timestamp=1636128423474, value=Sao Paulo
 1 column=pessoais:nome, timestamp=1636128393455, value=Maria
# é possível dar um scan e ver as versões com os dados 'duplicados'.

> count 'funcionarios'
# conta o número de row-keys.

> delete 'funcionarios', '1', 'hobby:nome'
> scan 'funcionarios'
# trás a versão anterior com o hobby ler livros.

> create 'ttl_exemplo', {'NAME'=>'cf', 'TTL'=>20}
> put 'ttl_exemplo', 'linha123', 'cf:desc', 'ttl exemplo'
# time to live: o parâmetro 'TTL' diz, em segundos, o tempo de expiração do dado. Pode ser aplicado a tabelas já criadas usando alter.
~~~