# Introdução a SQL

Todo desenvolvedor web que se respeite deve conhecer o SQL, entre os tópicos: [ActiveRecord](https://guides.rubyonrails.org/active_record_basics.html), [Doutrina](http://www.doctrine-project.org/), [Hibernar](http://hibernate.org/orm/) e muitos outros. Apesar disso, às vezes você tem que "sujar as mãos" e entrar no SQL real.

Esta é uma breve introdução na qual será pelas coisas fundamentais no SQL:

## Crie uma tabela

Para criar uma tabela em SQL, use a instrução `CREATE TABLE`. É preciso como parâmetros todas as colunas que queremos inserir, bem como seus tipos de dados.

Vamos criar uma tabela com o nome `months` em que haverá três colunas:

* id - em outras palavras, o número de série do mês (tipo inteiro ou int);
* name - nome do mês (string ou varchar(10) (10 caracteres - comprimento máximo da linha));
* days - número de dias em um mês específico (tipo inteiro ou int)

```CREATE TABLE months (id int, name varchar(10), days int);```

Além disso, ao criar tabelas, é costume adicionar a chamada chave primária. Esta é uma coluna cujos valores são únicos. Na maioria das vezes, a coluna principal é id, mas no nosso caso também pode ser nome, já que os nomes de todos os meses são únicos. Para mais informações, entre em [SQL PRIMARY KEY Constraint](https://www.w3schools.com/sql/sql_primarykey.asp).

## Entrada de dados

Agora vamos adicionar alguns meses ao nosso tabela, usando `INSERT`. Existem duas maneiras diferentes de usar `INSERT`:

O primeiro método não especifica os nomes das colunas, mas apenas leva os valores na ordem em que são indicados na tabela.

```INSERT INTO months VALUES (1,'January',31);```

O primeiro método é mais curto que o segundo, mas se quisermos adicionar colunas adicionais no futuro, todas as solicitações anteriores não funcionarão. Para resolver este problema, use o segundo método. Sua essência é que antes de inserir dados, especificamos os nomes das colunas.

```INSERT INTO months (id,name,days) VALUES (2,'February',29);```

Caso não especifiquemos uma das colunas, um NULO ou um valor padrão especificado será escrito em seu lugar, mas esta é uma história completamente diferente.

## Selecionar

Esta consulta é usada quando precisamos mostrar dados em uma tabela. Provavelmente o exemplo mais simples de usar `SELECT` seria a seguinte consulta:

```SELECT * FROM characters```

O resultado desta consulta será uma tabela com todos os dados na tabela de caracteres. O asterisco (*) significa que queremos mostrar todas as colunas as sem exceções. Uma vez que geralmente há mais de uma tabela em um banco de dados, precisamos especificar o nome da tabela a partir da qual queremos visualizar. Podemos fazer isso usando a palavra-chave `FROM`.

Quando você precisa apenas de algumas colunas da tabela, você pode especificar seus nomes separados por írgulas em vez de um asterisco.

```SELECT name, weapon FROM characters```

Além disso, às vezes precisamos classificar a saída. Para isso, usamos `ORDER BY` "nome da coluna". O `ORDER BY` tem dois modificadores: ASC (ascendente) (padrão) e DESC (descendente).

```SELECT name, weapon FROM characters ORDER BY name DESC```

## Local

Agora sabemos como mostrar apenas colunas específicas, mas e se quisermos incluir apenas algumas linhas específicas na saída? Para fazer isso, usamos `Where`. Esta keyword nos permite filtrar dados por uma condição específica.

Apenas listaremos personagens que usam uma pistola como arma.

``` SELECT * 
FROM characters
WHERE weapon = 'pistol';
```
## Operadores

As condições em WHERE podem ser escritas utilizando operadores lógicos (AND/OR) e operadores de comparação matemática (=, <, >, <=, >=, <>).

Por exemplo, temos um sinal que grava os 4 álbuns de música mais vendidos de todos os tempos. Vamos trazer apenas aqueles cujo gênero é rock, e as vendas foram inferiores a 50 milhões de cópias.

``` SELECT * 
FROM albums 
WHERE genre = 'rock' AND sales_in_millions <= 50 
ORDER BY released
```

## In/Between/Like

As condições em WHERE podem ser escritas usando mais alguns comandos, que são:

* `IN` - compara o valor na coluna com vários valores possíveis e retorna verdadeiro se o valor corresponder a pelo menos um valor
* `BETWEEN` - verifica se o valor está em um determinado intervalo
* `LIKE` - pesquisas por modelo
Por exemplo, podemos fazer uma consulta para exibir dados sobre álbuns no gênero pop ou soul:
```
SELECT * FROM albums WHERE genre IN ('pop','soul');
```

Se quisermos lançar todos os álbuns que foram lançados entre 1975 e 1985, podemos usar a seguinte entrada:
```
SELECT * FROM albums WHERE released BETWEEN 1975 AND 1985;
```
Além disso, se quisermos exibir todos os álbuns que têm a letra 'R' em seu título, podemos usar a seguinte entrada:
```
SELECT * FROM albums WHERE album LIKE '%R%';
```
O sinal % denota qualquer sequência de caracteres (0 caracteres também é considerado uma sequência).

Se quisermos exibir todos os álbuns cuja primeira letra no título é 'R', então o disco mudará ligeiramente:
```
SELECT * FROM albums WHERE album LIKE 'R%';
```
SQL também tem uma inversão. Por exemplo, tente escrever `NOT` você mesmo antes de qualquer expressão booleana na condição (`NOT BETWEEN`, e assim por diante).

## Funções

O SQL está cheio de funções incorporadas para a realização de várias operações. Mostraremos apenas os mais usados:

* `COUNT()` - retorna o número de linhas;
* `SOMA()` - retorna a soma de todos os campos com valores numéricos neles;
* `AVG()` - retorna o valor médio entre as cordas;
* `MIN()/MAX()` - retorna o valor mínimo/máximo entre as linhas;

Para exibir o ano de lançamento do álbum mais antigo, você pode usar a seguinte consulta na tabela:
```
SELECT MIN(released) FROM albums;
```

Observe que se você escrever uma consulta na qual, por exemplo, você precisa exibir o nome e a média de algo, você terá um erro na saída.

Digamos que escreva este pedido:
```
SELECT name, avg(age) FROM students;
```
Para evitar o erro, você deve adicionar a seguinte linha:
```
GROUP BY name
```
A razão para isso é que a entrada avg(idade) é agregada, e você precisa agrupar os valores pelo nome.

# Select nested

Nas etapas anteriores, fizemos  cálculos simples com dados. Se quisermos usar o resultado desses cálculos, muitas vezes precisamos usar as chamadas *nested queries*. Digamos que queremos listar o artista, o álbum, e o ano de lançamento do álbum mais antigo.

Você pode exibir essas colunas usando a seguinte consulta:
```
SELECT artist, album, released FROM albums;
```
Também sabemos como obter o primeiro ano disponível:
```
SELECT MIN(released) FROM album;
```
Você pode combinar essas consultas em `WHERE`:
```
SELECT artist,album,released 
FROM albums 
WHERE released = (
 SELECT MIN(released) FROM albums
);
```

## Joining tables

Em bancos de dados complexos, na maioria das vezes temos várias tabelas relacionadas. Por exemplo, temos duas tabelas: sobre videogames e sobre desenvolvedores.

Na tabela video_games há uma coluna developer_id, neste caso é a chamada `foreign_key`. Para facilitar o entendimento, developer_id é o elo entre duas tabelas.

Se quisermos exibir todas as informações sobre o jogo, incluindo informações sobre seu desenvolvedor, precisamos conectar a segunda tabela. Para fazer isso, você pode usar o `INNER JOIN`:
```
SELECT video_games.name, video_games.genre, game_developers.name, game_developers.country 
FROM video_games 
INNER JOIN game_developers 
ON video_games.developer_id = game_developers.id;
```

Este é provavelmente o exemplo mais simples de usar o JOIN. Existem várias outras opções para seu uso. Para mais informações, entre em [SQL Joins](https://www.w3schools.com/sql/sql_join.asp)

## Aliases

Se você der uma olhada no exemplo anterior, você notará que existem duas colunas com o mesmo nome: "nome". Muitas vezes isso pode ser confuso. A solução para este problema são os pseudônimos. Eles, por sinal, ajudam a tornar o nome da coluna mais bonito ou claro, se necessário.

Para atribuir um pseudônimo a uma coluna, você pode usar a keyword AS:
```
SELECT games.name, games.genre, devs.name AS developer, devs.country 
FROM video_games AS games 
INNER JOIN game_developers AS devs 
ON games.developer_id = devs.id;
```

## Update

Muitas vezes precisamos alterar os dados na tabela. No SQL, isso é feito usando UPDATE.

O uso do UPDATE inclui:

* Selecionando a tabela que contém o campo que queremos alterar;
* Escrever um novo valor;
* Usar ONDE para marcar um local específico em uma tabela.

Suponha que tenhamos uma mesa com a série de TV mais conceituada de todos os tempos. No entanto, temos um problema: Game of Thrones é rotulado como uma comédia e definitivamente precisamos mudar isso:
```
UPDATE tv_series 
SET genre = 'drama' 
WHERE name = 'Game of Thrones';
```

## Exclua registros de uma tabela

Excluir um registro de uma tabela via SQL é uma operação muito simples. Tudo o que é necessário é indicar o que exatamente queremos remover.
```
DELETE FROM tv_series 
WHERE id = 4;
```

OBS: Certifique-se de usar `WHERE` quando você excluir um registro da tabela. Caso contrário, você vai excluir todos os registros da tabela sem querer.

## Excluir tabelas

Se quisermos remover todos os dados da tabela, mas deixar a tabela em si, devemos usar o comando `TRUNCATE`:
```
TRUNCATE TABLE table_name;
```

Caso queiramos excluir a tabela em si, devemos usar o comando `DROP`:
```
DROP TABLE table_name;
```

## Conclusão

Nesta nota concluímos este tutorial SQL. Claro, isso não é tudo, e para o domínio completo você ainda precisa estudar muito, mas esta introdução lhe dará um impulso para um estudo mais aprofundado.

Tutoriais SQL mais detalhados podem ser encontrados clicando nos seguintes links:

* [Curso Codecademy SQL](https://www.codecademy.com/learn/learn-sql)
* [SQL Fiddle](http://sqlfiddle.com/): ferramenta online para testar consultas SQL
