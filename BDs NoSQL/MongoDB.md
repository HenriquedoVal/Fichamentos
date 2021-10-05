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
