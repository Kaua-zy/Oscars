# Oscar - Exercícios MongoDB

Bem-vindo à base de dados do **Oscar**!

O Oscar é a premiação mais prestigiada do cinema mundial, realizada anualmente desde 1929 pela Academia de Artes e Ciências Cinematográficas. Nesta base de dados, você encontrará registros históricos de indicados e vencedores de diversas categorias ao longo de quase 100 anos de história do cinema.

Nestes exercícios, você vai explorar o banco de dados MongoDB do Oscar e responder perguntas que revelam insights fascinantes sobre a história do cinema, tendências de premiação, e momentos marcantes da indústria cinematográfica.

## Dicas Gerais

- **Conversão de Dados**: Use `$toInt`, `$toBool` para converter tipos
- **Arrays**: Use `$addToSet` para valores únicos
- **Datas**: Use `$year` para extrair o ano de campos de data
- **Performance**: Para queries complexas, considere criar índices em campos frequentemente consultados
- **Boa prática**: Sempre teste com `.limit(5)` antes de executar queries que retornam muitos resultados

---

## Avaliação

- **Nível 1-4**: Operações básicas e queries simples
- **Nível 5-8**: Agregações e análise de dados
- **Nível 9-11**: Queries complexas e pensamento analítico
- **Nível 12-14**: Expertise avançada e pensamento estratégico

**Objetivo de aprendizado**: Ao completar todos os níveis, você será capaz de trabalhar com bases de dados históricas complexas, realizar análises estatísticas sofisticadas e extrair insights valiosos de grandes volumes de dados.

---

## Nível 1: Primeiros Passos

### Conhecendo a Base de Dados

**1.1** Quantos registros existem na coleção de indicados ao Oscar?

Exemplo de resposta:

R: 1430 registros

```javascript
db.oscar_indicados.countDocuments();
```

**1.2** Quais são as diferentes categorias de premiação que existem no banco de dados? Liste todas as categorias únicas.

Exemplo de resposta:

R: 92 registros

```javascript
db.oscar_indicados.distinct("categoria");
```

**1.3** Qual foi o primeiro ano de cerimônia do Oscar registrado na base?

R: 1928

```
db.registros.find().skip(15).limit(1).sort({ano_cerimonia: -1})
``` 


**1.4** Qual foi o último ano de cerimônia registrado na base?
R: 2024

```
db.oscars.find().sort({ano_cerimonia:-1}).limit(1);
```

**1.5** Quantas cerimônias do Oscar estão registradas no total?
R: Existem 96 cerimônias registradas

```
db.oscars.distinct("cerimonia").length;
```

**1.6** Atualize os registros da coleção com os dados do Oscar 2025 e 2026 (pesquise os vencedores e adicione-os).

---

## Nível 2: Explorando Categorias

**2.1** Quantas indicações existem para cada categoria? Agrupe por categoria e ordene da mais frequente para a menos frequente.

R: DIRECTING, total: 469 , FILM EDITING, total: 450, ACTRESS IN A SUPPORTING ROLE, total: 440

  
  
**2.2** Qual categoria teve mais indicações ao longo da história do Oscar?

R: DIRECTING, total: 469
  
```
db.oscars.aggregate([
{$group:{_id:"$categoria",total:{$sum:1}}},
{$sort:{total:-1}},
{$limit:1}
]);

```

**2.3** Qual categoria teve menos indicações ao longo da história?

R: SPECIAL ACHIEVEMENT AWARD (Sound Effects) ,   total: 1

```
db.oscars.aggregate([
{$group:{_id:"$categoria",total:{$sum:1}}},
{$sort:{total:1}},
{$limit:1}
]);

```

**2.4** A partir de que ano a categoria "ACTRESS" deixou de existir? (Dica: procure a última cerimônia com essa categoria)

R: 1976

```
db.oscars.find({categoria:"ACTRESS"})
.sort({ano_cerimonia:-1})
.limit(1);

```

**2.5** Quais categorias existiam na primeira cerimônia (1928) e não existem mais hoje?

R:

**2.6** Liste todas as categorias que contêm a palavra "DIRECTING" no nome.

R: 4

```
db.oscars.find(
{categoria:{$regex:"DIRECTING",$options:"i"}},
{categoria:1,_id:0}
);
```

## Nível 3: Atores e Atrizes Famosos

### Natalie Portman

**3.1** Quantas vezes Natalie Portman foi indicada ao Oscar?

R: Ela foi indicada 3 vezes

```
db.oscars.countDocuments({
nome_do_indicado:"Natalie Portman"
});

```

**3.2** Quantos Oscars Natalie Portman ganhou?

R: Ela ganhou 1

```
db.oscars.countDocuments({
nome_do_indicado:"Natalie Portman",
vencedor:"true"
});

```

**3.3** Em quais anos e por quais filmes Natalie Portman foi indicada?

R: Ano: 2005, Filme: Closer
Ano: 2011, Filme: Black Swan
Ano: 2017, Filme : Jackie

```
db.oscars.find(
{nome_do_indicado:"Natalie Portman"},
{ano_cerimonia:1,nome_do_filme:1,_id:0}
);

```

**3.4** Liste todas as indicações de Natalie Portman mostrando: ano, categoria, filme e se venceu.

R: ano_cerimonia: 2005,
  categoria: 'ACTRESS IN A SUPPORTING ROLE',
  nome_do_filme: 'Closer',
  vencedor: 'false'
  
  ano_cerimonia: 2011,
  categoria: 'ACTRESS IN A LEADING ROLE',
  nome_do_filme: 'Black Swan',
  vencedor: 'true'

  ano_cerimonia: 2017,
  categoria: 'ACTRESS IN A LEADING ROLE',
  nome_do_filme: 'Jackie',
  vencedor: 'false'
```

db.oscars.find(
{nome_do_indicado:"Natalie Portman"},
{
ano_cerimonia:1,
categoria:1,
nome_do_filme:1,
vencedor:1,
_id:0
}
);

```

### Viola Davis

**3.5** Quantas vezes Viola Davis foi indicada ao Oscar?

R: 4

```
db.oscars.countDocuments({
nome_do_indicado:"Viola Davis"
});

```

**3.6** Quantos Oscars Viola Davis ganhou?

R: 1

```

db.oscars.countDocuments({
nome_do_indicado:"Viola Davis",
vencedor:"true"
});

```

**3.7** Por quais filmes Viola Davis foi indicada?

R: Filmes: Doubt, Fences, Ma Rainey's Black Bottom, The Help

```
db.oscars.distinct(
"nome_do_filme",
{nome_do_indicado:"Viola Davis"}
);

```

### Amy Adams

**3.8** Amy Adams já ganhou algum Oscar?

R: Não 

db.oscars.find({
nome_do_indicado:"Amy Adams",
vencedor:"true"
});


**3.9** Quantas vezes Amy Adams foi indicada sem ganhar?

R: Ela teve 6 Indicações

```

db.oscars.countDocuments({
nome_do_indicado:"Amy Adams",
vencedor:"false"
});
```

### Denzel Washington

**3.10** Denzel Washington já ganhou algum Oscar?

R: Sim, 2 vezes

```
db.oscars.find({
nome_do_indicado:"Denzel Washington",
vencedor:"true"
});

```

**3.11** Quantas vezes Denzel Washington foi indicado ao Oscar?

R: 9 Indicações

```

db.oscars.countDocuments({
nome_do_indicado:"Denzel Washington"
});
```

**3.12** Liste todos os Oscars que Denzel Washington ganhou (ano, categoria, filme).

R: Lista de vitórias:

 ano_cerimonia: 1990,
  categoria: 'ACTOR IN A SUPPORTING ROLE',
  nome_do_filme: 'Glory'

ano_cerimonia: 2002,
  categoria: 'ACTOR IN A LEADING ROLE',
  nome_do_filme: 'Training Day'

```
db.oscars.find(
{
nome_do_indicado:"Denzel Washington",
vencedor:"true"
},
{
ano_cerimonia:1,
categoria:1,
nome_do_filme:1,
_id:0
}
);
```

## Nível 4: Vencedores Históricos

**4.1** Quem ganhou o primeiro Oscar para Melhor Atriz (ACTRESS)? Em que ano e por qual filme?

R: A vencedora foi Janet Gaynor, no ano de 1928, pelo filme 7th Heaven.

```
db.oscars.find(
{
categoria:"ACTRESS",
vencedor:"true"
},
{
nome_do_indicado:1,
nome_do_filme:1,
ano_cerimonia:1,
_id:0
}
).sort({ano_cerimonia:1}).limit(1);
```

**4.2** Quem ganhou o primeiro Oscar para Melhor Ator (ACTOR)? Em que ano e por qual filme?

R: O vencedor foi Emil Jannings, em 1928, pelo filme The Last Command.

```
db.oscars.find(
{
categoria:"ACTOR",
vencedor:"true"
},
{
nome_do_indicado:1,
nome_do_filme:1,
ano_cerimonia:1,
_id:0
}
).sort({ano_cerimonia:1}).limit(1);

```

**4.3** Quantos vencedores existem ao todo na base de dados?

R: Na base de dados existem 2465 vencedores

```
db.oscars.countDocuments({
vencedor:"true"
});

```

**4.4** Liste todos os filmes que ganharam o Oscar de Melhor Filme (categoria "OUTSTANDING PICTURE" ou "BEST PICTURE").

R: Ano: 1963,
   Filme:'Lawrence of Arabia'

R: Ano: 1964,
  Filme: "Tom Jones"

  Ano: 1965,
  Filme: "My Fair Lady"

  Ano: 1966,
  Filme: "The Sound of Music"

  Ano: 1967,
  Filme: "A Man for All Seasons"

  Ano: 1968,
  Filme: "In the Heat of the Night"

  Ano: 1969,
  Filme: "Oliver"

  Ano: 1970,
  Filme: "Midnight Cowboy"

  Ano: 1971,
  Filme: "Patton"

  Ano: 1972,
  Filme: "The French Connection"

  Ano: 1973,
  Filme: "The Godfather"

  Ano: 1974,
  Filme: "The Sting"

  Ano: 1975,
  Filme: "The Godfather Part II"

  Ano: 1976,
  Filme: "One Flew over the Cuckoo's Nest"

  Ano: 1977,
  Filme: "Rocky"

  Ano: 1978,
  Filme: "Annie Hall"

  Ano: 1979,
  Filme: "The Deer Hunter"

  Ano: 1980,
  Filme: "Kramer vs. Kramer"

  Ano: 1981,
  Filme: "Ordinary People"

  Ano: 1982,
  Filme: "Chariots of Fire"

```

**4.5** Quantos filmes diferentes já ganharam o Oscar?

R:  1329

```
db.oscars.distinct(
"nome_do_filme",
{vencedor:"true"}
).length;

```

## Nível 5: Análise de Indicações

**5.1** Quais atores/atrizes foram indicados mais de uma vez? Liste o nome e o número de indicações.

R:  "Jack Nicholson indicado 12 vezes, Bette Davis indicada 11 vezes, Denzel Washington indicado 9 vezes, Al Pacino indicado 9 vezes, 

Marlon Brando indicado 8 vezes, Robert De Niro indicado 8, Cate Blanchett indicada 8, Jane Fonda indicada 7, Meryl Streep indicada 21"


```

db.oscars.aggregate([
{
$match:{
categoria:{
$regex:"ACTOR|ACTRESS",
$options:"i"
}
}
},
{
$group:{
_id:"$nome_do_indicado",
total:{$sum:1}
}
},
{
$match:{
total:{$gt:1}
}
},
{$sort:{total:-1}}
]);

```

**5.2** Qual ator ou atriz tem o maior número de indicações na história do Oscar?

R:  A atriz Meryl Streep foi a mais indicada, com 21 indicações 

```
db.oscars.aggregate([
{
$match:{
categoria:{
$regex:"ACTOR|ACTRESS",
$options:"i"
}
}
},
{
$group:{
_id:"$nome_do_indicado",
total:{$sum:1}
}
},
{$sort:{total:-1}},
{$limit:1}
]);

```

**5.3** Quais atores foram indicados mais de 3 vezes, mas nunca ganharam?

R: Amy Adams, indicacoes: 6, vitorias: 0, 

Mark Ruffalo, indicacoes: 4, vitorias: 0, 

Arthur Kennedy, indicacoes: 5, vitorias: 0,

Annette Bening, indicacoes: 5, vitorias: 0, 

Jane Alexander, indicacoes: 4, vitorias: 0, 

Barbara Stanwyck, indicacoes: 4, vitorias: 0,

Richard Burton, indicacoes: 7, vitorias: 0, 

Thelma Ritter,indicacoes: 6, vitorias: 0, 

Mickey Rooney, indicacoes: 4, vitorias: 0,

Deborah Kerr, indicacoes: 6, vitorias: 0, 

Saoirse Ronan, indicacoes: 4, vitorias: 0, 

Marsha Mason,indicacoes: 4, vitorias: 0,

Ed Harris, indicacoes: 4, vitorias: 0, 

Peter O'Toole, indicacoes: 8, vitorias: 0,

Claude Rains, indicacoes: 4, vitorias: 0, 

Rosalind Russell, indicacoes: 4, vitorias: 0,

Irene Dunne, indicacoes: 5, vitorias: 0,

Warren Beatty, indicacoes: 4, vitorias: 0,

Agnes Moorehead, indicacoes: 4, vitorias: 0,

Montgomery Clift, indicacoes: 4, vitorias: 0, 

```

**5.4** Encontre todos os artistas que foram indicados em categorias diferentes (ex: ator e diretor).

R: 'Tom Hanks',
  categorias: [
    'ACTOR IN A SUPPORTING ROLE',
    'ACTOR IN A LEADING ROLE'
  ],
  
  totalCategorias: 2


  'Paul Newman',
  categorias: [
    'ACTOR IN A LEADING ROLE',
    'ACTOR',
    'ACTOR IN A SUPPORTING ROLE'
  ],
  totalCategorias: 3

  'Roberto Benigni',
  categorias: [
    'DIRECTING',
    'ACTOR IN A LEADING ROLE'
  ],
totalCategorias: 2


'Scarlett Johansson',
  categorias: [
    'ACTRESS IN A LEADING ROLE',
    'ACTRESS IN A SUPPORTING ROLE'
  ],
  totalCategorias: 2

  'Michael Fassbender',
  categorias: [
    'ACTOR IN A SUPPORTING ROLE',
    'ACTOR IN A LEADING ROLE'
  ],
  totalCategorias: 2

  'Jamie Foxx',
  categorias: [
    'ACTOR IN A LEADING ROLE',
    'ACTOR IN A SUPPORTING ROLE'
  ],
   totalCategorias: 2

  'Joanne Woodward',
  categorias: [
    'ACTRESS IN A LEADING ROLE',
    'ACTRESS'
  ],
  totalCategorias: 2

  _id: "Peter O'Toole",
  categorias: [
    'ACTOR',
    'ACTOR IN A LEADING ROLE'
  ],
  totalCategorias: 2

  Jude Law',
  categorias: [
    'ACTOR IN A SUPPORTING ROLE',
    'ACTOR IN A LEADING ROLE'
  ],
    totalCategorias: 2

  'George Clooney',
  categorias: [
    'ACTOR IN A LEADING ROLE',
    'ACTOR IN A SUPPORTING ROLE',
    'DIRECTING'
  ],
  totalCategorias: 3
  
'Woody Harrelson',
  categorias: [
    'ACTOR IN A SUPPORTING ROLE',
    'ACTOR IN A LEADING ROLE'
  ],
  totalCategorias: 2

'Cher',
  categorias: [
    'ACTRESS IN A LEADING ROLE',
    'ACTRESS IN A SUPPORTING ROLE'
  ],
  totalCategorias: 2

   'Jessica Chastain',
  categorias: [
    'ACTRESS IN A LEADING ROLE',
    'ACTRESS IN A SUPPORTING ROLE'
  ],
  totalCategorias: 2

  'Anne Bancroft',
  categorias: [
    'ACTRESS IN A LEADING ROLE',
    'ACTRESS'
  ],

  'Melissa McCarthy',
  categorias: [
    'ACTRESS IN A SUPPORTING ROLE',
    'ACTRESS IN A LEADING ROLE'
  ],
  totalCategorias: 2

  'Juliette Binoche',
  categorias: [
    'ACTRESS IN A SUPPORTING ROLE',
    'ACTRESS IN A LEADING ROLE'
  ],
  'Laurence Olivier',
  categorias: [
    'ACTOR IN A LEADING ROLE',
    'DIRECTING',
    'ACTOR',
    'ACTOR IN A SUPPORTING ROLE'
  ],
  totalCategorias: 4

'Dustin Hoffman',
  categorias: [
    'ACTOR IN A LEADING ROLE',
    'ACTOR'
  ],
  totalCategorias: 2

  'Roberto Benigni',
  categorias: [
    'DIRECTING',
    'ACTOR IN A LEADING ROLE'
  ],
  totalCategorias: 2

   'Samantha Morton',
  categorias: [
    'ACTRESS IN A SUPPORTING ROLE',
    'ACTRESS IN A LEADING ROLE'
  ],
  totalCategorias: 2

  'Casey Affleck',
  categorias: [
    'ACTOR IN A SUPPORTING ROLE',
    'ACTOR IN A LEADING ROLE'
  ],
  totalCategorias: 2

  ```

**5.5** Quantos indicados têm exatamente 1 indicação na história?

R: 620 indicados têm exatamente 1 indicação na história.

```

db.oscars.aggregate([
{
$match:{
categoria:{
$regex:"ACTOR|ACTRESS",
$options:"i"
}
}
},
{
$group:{
_id:"$nome_do_indicado",
total:{$sum:1}
}
},
{
$match:{
total:1
}
},
{
$count:"quantidade"
}
]);

**5.6** Qual o maior números de indicados em um único ano? Essa é uma pergunta franca.  

R: O maior numeros de indicados foi de 186 em 1943.

```

db.oscars.aggregate([
{
$group:{
_id:"$ano_cerimonia",
total_indicados:{$sum:1}
}
},
{$sort:{total_indicados:-1}},
{$limit:1}
]);

```



## Nível 6: Análise de Filmes

### Toy Story

**6.1** A série de filmes Toy Story ganhou Oscars em quais anos?

R:  2011, ANIMATED FEATURE FILM
    
    2011, MUSIC (Original Song)

    2020, ANIMATED FEATURE FILM

```
db.oscars.find(
{
nome_do_filme:{
$regex:"Toy Story",
$options:"i"
},
vencedor:"true"
},
{
ano_cerimonia:1,
categoria:1,
_id:0
}
);

```

**6.2** Quantas indicações a franquia Toy Story recebeu no total?

R: Toy Story recebeu 11 indicaçoes

```

db.oscars.countDocuments({
nome_do_filme:{
$regex:"Toy Story",
$options:"i"
}
});
```

**6.3** Em quais categorias os filmes Toy Story foram indicados?

R: ANIMATED FEATURE FILM,
   BEST PICTURE,
   MUSIC (Original Musical or Comedy Score),
   MUSIC (Original Song),
   SOUND EDITING,
   WRITING (Adapted Screenplay),
   WRITING (Screenplay Written Directly for the Screen)
```

db.oscars.distinct(
"categoria",
{
nome_do_filme:{
$regex:"Toy Story",
$options:"i"
}
}
);
```

### Crash

**6.4** Em qual edição do Oscar o filme "Crash" concorreu?

R: Foi na edição 78

```
db.oscars.find
{
nome_do_filme:"Crash"
},
{
cerimonia:1,
ano_cerimonia:1,
_id:0
}
```


**6.5** Quantas indicações o filme "Crash" recebeu?

R: Crash recebeu 6 indicações.

```

db.oscars.countDocuments({
nome_do_filme:"Crash"
});
```

**6.6** "Crash" ganhou o Oscar de Melhor Filme?

R: Sim, em 2006.
```
db.oscars.find({
nome_do_filme:"Crash",
categoria:"BEST PICTURE",
vencedor:"true"
});
```


### Central do Brasil

**6.7** O filme "Central do Brasil" aparece no banco de dados?

R: Não
```
db.oscars.find({
nome_do_filme:"Central do Brasil"
});

**6.8** Se sim, quantas indicações "Central do Brasil" recebeu?

R: Não tem como saber, pois no banco de dados não tem Central do Brasil

---

## Nível 7: Operações de Atualização

**7.1** No campo "vencedor", altere todos os valores "true" (string) para true (booleano) e "false" (string) para false (booleano).

**7.2** Inclua no banco 3 filmes que nunca foram nomeados ao Oscar, mas que você acha que merecem. Use dados fictícios mas realistas.

**7.3** Adicione uma nova categoria chamada "BEST INTERNATIONAL FEATURE FILM" com alguns vencedores recentes (2020-2024).

**7.4** Corrija possíveis erros de digitação nos nomes dos filmes (ex: espaços extras, caracteres especiais desnecessários).

**7.5** Remova todos os registros com valor NULL no campo nome_do_filme.

---


## Nível 8: Análise Temporal

**8.1** Quantas indicações aconteceram por década? Agrupe por década (1920s, 1930s, etc.) e mostre o total.

**8.2** Em qual década houve o maior número de indicações?

**8.3** Como o número de categorias evoluiu ao longo dos anos? Mostre quantas categorias únicas existiam a cada década.

**8.4** Qual foi o ano com o maior número de indicações registradas?

**8.5** Calcule a taxa de crescimento de indicações comparando a primeira década com a última.

---


## Nível 9: Questões Históricas e Sociais

### Representatividade

**9.1** Sidney Poitier foi o primeiro ator negro a ser indicado ao Oscar. Em que ano ele foi indicado? Por qual filme?

**9.2** Sidney Poitier ganhou o Oscar nessa indicação?

**9.3** Quantos atores/atrizes negros foram indicados na categoria ACTOR ou ACTRESS antes de 1970?

**9.4** Liste todos os filmes dirigidos por mulheres que ganharam algum Oscar.

### Coincidências

**9.5** Denzel Washington e Jamie Foxx já concorreram ao Oscar no mesmo ano?

**9.6** Se sim, em qual ano e quem ganhou?

**9.7** Encontre casos onde o mesmo filme ganhou Oscar em múltiplas categorias na mesma cerimônia. Mostre o nome do filme e quantas categorias ele venceu.

---

## Nível 10: Análise Avançada

**10.1** Quais filmes ganharam o Oscar de Melhor Filme ("OUTSTANDING PICTURE" ou "BEST PICTURE") e Melhor Diretor na mesma cerimônia?

**10.2** Qual filme recebeu o maior número de indicações em uma única cerimônia?

**10.3** Qual filme teve a maior taxa de conversão (porcentagem de indicações que viraram vitórias)?

**10.4** Encontre atores que foram indicados em anos consecutivos. Liste o nome e os anos.

**10.5** Qual a média de indicações por cerimônia ao longo da história?

**10.6** Identifique "surpresas" - indicados em categorias principais (ACTOR, ACTRESS, BEST PICTURE) cujo filme só teve uma indicação.

---

## Nível 11: Desafios Complexos

**11.1** Crie um ranking dos 10 filmes mais premiados da história (que ganharam mais Oscars).

**11.2** Crie um ranking dos 10 artistas (atores/diretores) mais indicados da história, independente da categoria.

**11.3** Encontre "azarões" - artistas com mais de 5 indicações e 0 vitórias.

**11.4** Qual categoria tem a maior concentração de vitórias (menos vencedores diferentes ao longo do tempo)?

**11.5** Calcule a "competitividade" de cada categoria (média de indicados por cerimônia).

**11.6** Encontre filmes que foram indicados em uma categoria em um ano e ganharam em outra categoria em outro ano.

---

## Nível 12: Casos Práticos

### Cenário 1: Curadoria de Mostra de Cinema

Você está organizando uma mostra de cinema e precisa selecionar filmes.

**12.1** Liste os 20 filmes mais premiados do Oscar para sua mostra.

**12.2** Selecione 5 filmes de cada década (1930s até 2020s) que ganharam pelo menos um Oscar.

**12.3** Crie uma lista de "clássicos esquecidos" - filmes que ganharam Oscars, mas são de mais de 50 anos atrás.

### Cenário 2: Análise para Documentário

Você está produzindo um documentário sobre a história do Oscar.

**12.4** Identifique os 5 momentos mais importantes (cerimônias com mais premiações históricas).

**12.5** Liste todos os "primeiros" históricos (primeira mulher a ganhar melhor direção, primeiro ator negro, etc.) - use sua criatividade para encontrar esses marcos.

**12.6** Encontre casos de "injustiça" - filmes/atores muito indicados, mas que nunca ganharam.

### Cenário 3: Estatísticas para Apostas

Você trabalha para um site de apostas e precisa de estatísticas.

**12.7** Qual a probabilidade histórica de um filme indicado em 10 categorias ganhar Melhor Filme?

**12.8** Atores que ganharam Melhor Ator tendem a ter quantas indicações antes da primeira vitória?

**12.9** Qual categoria tem os vencedores mais "previsíveis" (mesmo artista/filme ganha múltiplas vezes)?

---

## Nível 13: Queries Criativas

**13.1** Encontre todos os filmes cujo nome começa com "The" e ganharam pelo menos um Oscar.

**13.2** Liste todos os indicados cujo nome contém um sobrenome composto (ex: "Mary-Louise Parker").

**13.3** Encontre todas as cerimônias onde houve empate (múltiplos vencedores na mesma categoria no mesmo ano).

**13.4** Crie uma query que simule uma "loteria" - selecione 5 filmes aleatórios que ganharam Melhor Filme.

**13.5** Encontre padrões nos nomes dos filmes vencedores (ex: quantos têm uma palavra só, duas palavras, etc.).

---

## Desafio Final: Dashboard Completo

**14.1** Crie UMA ÚNICA query de agregação que retorne um dashboard executivo com:
   - Total de indicações
   - Total de cerimônias
   - Total de vencedores
   - Categoria com mais indicações
   - Filme mais premiado
   - Ator/atriz mais indicado(a)
   - Década com mais premiações
   - Número de categorias únicas

---



Bons estudos!
