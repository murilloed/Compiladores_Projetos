# Atividade prática: construção de um analisador léxico em C

## Entrega e pontuação:

Forma de entrega: repositório individual no GitHub, relatório em Markdown e apresentação prática
Valor: 10,0 pontos
Data de entrega: 28/08  - apresentar pitch e entrega em repos (o aluno deverar criar seu repos com os artefatos criados e liberar link via e-mail para o professor), total 2 pontos de 50% da P2, os outros 50% serão do projeto integrador.

## 1. Objetivo

Nesta atividade, você desenvolverá, em linguagem C, uma versão simplificada da primeira etapa de um compilador: a **análise léxica**.

O analisador léxico, também chamado de *scanner* ou *tokenizador*, lê um código-fonte caractere por caractere e agrupa esses caracteres em unidades chamadas **tokens**. Um token pode representar, por exemplo, uma palavra reservada, um identificador, um número, um operador ou um delimitador.

Ao concluir a atividade, você deverá ser capaz de:

- explicar o papel da análise léxica em um compilador;
- ler e processar arquivos em C;
- reconhecer padrões em sequências de caracteres;
- controlar posições de linha e coluna;
- detectar erros léxicos sem interromper toda a análise;
- organizar um programa C em funções e estruturas de dados.

---

## 2. Situação-problema

Você foi encarregado de implementar o analisador léxico de uma linguagem fictícia chamada **MiniC**.

Seu programa deverá receber um arquivo contendo código em MiniC, percorrê-lo do início ao fim e apresentar todos os tokens encontrados. Para cada token, deverão ser informados:

- a linha em que começa;
- a coluna em que começa;
- sua categoria;
- o lexema identificado.

Um **lexema** é o texto original que deu origem ao token. No comando `int idade = 18;`, por exemplo, `int`, `idade`, `=`, `18` e `;` são lexemas.

Seu analisador não deverá verificar se os comandos estão sintaticamente corretos, executar o programa ou gerar código de máquina. A tarefa é apenas separar e classificar os elementos do código-fonte.

---

## 3. Funcionamento esperado

O programa deverá ser executado pela linha de comando, recebendo o nome do arquivo-fonte:

```bash
minilexer programa.mc
```

Se nenhum arquivo for informado, apresente:

```text
Uso: minilexer <arquivo-fonte>
```

Se o arquivo não puder ser aberto, apresente uma mensagem clara e encerre o programa adequadamente.

---

## 4. Elementos da linguagem MiniC

### 4.1 Palavras reservadas

Reconheça as seguintes palavras reservadas:

```text
int
float
char
if
else
while
return
print
```

Use a categoria:

```text
PALAVRA_RESERVADA
```

### 4.2 Identificadores

Um identificador:

- deve começar com uma letra ou `_`;
- pode continuar com letras, algarismos ou `_`;
- deve ser diferenciado das palavras reservadas;
- pode possuir, no máximo, 31 caracteres.

Exemplos válidos:

```text
idade
_media
aluno2
valor_total
```

Use a categoria:

```text
IDENTIFICADOR
```

Se um identificador ultrapassar 31 caracteres, registre um erro léxico. Mesmo nesse caso, consuma todo o identificador antes de analisar o próximo elemento.

### 4.3 Números inteiros

Um número inteiro é formado apenas por algarismos.

Exemplos:

```text
0
15
2026
```

Use a categoria:

```text
NUMERO_INTEIRO
```

### 4.4 Números reais

Um número real válido possui uma parte inteira, um ponto e uma parte decimal.

Exemplos válidos:

```text
1.5
0.25
100.0
```

Use a categoria:

```text
NUMERO_REAL
```

Considere malformados números como:

```text
12.
1.2.3
```

### 4.5 Literais de caractere

Um literal de caractere deve ser delimitado por aspas simples e conter exatamente um caractere.

Exemplos válidos:

```c
'a'
'7'
'+'
```

Use a categoria:

```text
LITERAL_CARACTERE
```

Exemplos inválidos:

```c
''
'ab'
'x
```

Não é necessário implementar sequências de escape como `'\n'`.

### 4.6 Operadores

Reconheça os operadores abaixo:

| Um caractere | Dois caracteres |
|---|---|
| `+` | `==` |
| `-` | `!=` |
| `*` | `<=` |
| `/` | `>=` |
| `%` | `&&` |
| `=` | `\|\|` |
| `<` | |
| `>` | |
| `!` | |

Use a categoria:

```text
OPERADOR
```

Verifique primeiro se existe um operador de dois caracteres. Assim, `==` deve produzir um único token, e não dois tokens `=`.

### 4.7 Delimitadores

Reconheça os delimitadores:

```text
(  )  {  }  [  ]  ;  ,
```

Use a categoria:

```text
DELIMITADOR
```

### 4.8 Comentários

Ignore comentários de uma linha iniciados por `//`. Todo o conteúdo entre `//` e o fim da linha não deverá gerar tokens.

```c
int idade; // variável que armazena a idade
```

Nesse exemplo, apenas `int`, `idade` e `;` deverão aparecer na saída.

Não é necessário implementar comentários de múltiplas linhas `/* ... */`.

### 4.9 Espaços em branco

Espaços, tabulações e quebras de linha deverão separar tokens, mas não deverão aparecer na saída. A contagem de linha e coluna deve continuar correta.

### 4.10 Caracteres inválidos

Qualquer caractere que não pertença às categorias anteriores deverá gerar um erro léxico.

Para a entrada:

```c
int x = 10 @ 2;
```

o caractere `@` deverá produzir uma mensagem semelhante a:

```text
ERRO_LEXICO | linha 1, coluna 12 | símbolo inválido: @
```

Após registrar o erro, continue analisando o restante do arquivo.

---

## 5. Formato da saída

Apresente um token por linha, no seguinte formato:

```text
LINHA:COLUNA | CATEGORIA | LEXEMA
```

Exemplo:

```text
1:1  | PALAVRA_RESERVADA | int
1:5  | IDENTIFICADOR     | idade
1:11 | OPERADOR          | =
1:13 | NUMERO_INTEIRO    | 18
1:15 | DELIMITADOR       | ;
```

Ao final da análise, apresente:

```text
Total de tokens: 5
Total de erros léxicos: 0
```

Pequenas diferenças de alinhamento são permitidas, desde que todas as informações estejam presentes.

---

## 6. Exemplo completo

Considere o arquivo `exemplo.mc`:

```c
int idade = 18;
float media = 8.5;

// Verifica a aprovação
if (media >= 7.0) {
    print(media);
}

return 0;
```

Saída esperada:

```text
1:1  | PALAVRA_RESERVADA | int
1:5  | IDENTIFICADOR     | idade
1:11 | OPERADOR          | =
1:13 | NUMERO_INTEIRO    | 18
1:15 | DELIMITADOR       | ;
2:1  | PALAVRA_RESERVADA | float
2:7  | IDENTIFICADOR     | media
2:13 | OPERADOR          | =
2:15 | NUMERO_REAL       | 8.5
2:18 | DELIMITADOR       | ;
5:1  | PALAVRA_RESERVADA | if
5:4  | DELIMITADOR       | (
5:5  | IDENTIFICADOR     | media
5:11 | OPERADOR          | >=
5:14 | NUMERO_REAL       | 7.0
5:17 | DELIMITADOR       | )
5:19 | DELIMITADOR       | {
6:5  | PALAVRA_RESERVADA | print
6:10 | DELIMITADOR       | (
6:11 | IDENTIFICADOR     | media
6:16 | DELIMITADOR       | )
6:17 | DELIMITADOR       | ;
7:1  | DELIMITADOR       | }
9:1  | PALAVRA_RESERVADA | return
9:8  | NUMERO_INTEIRO    | 0
9:9  | DELIMITADOR       | ;

Total de tokens: 26
Total de erros léxicos: 0
```

---

## 7. Requisitos obrigatórios

Sua solução deverá:

- ser escrita em C;
- ler o arquivo caractere por caractere;
- receber o nome do arquivo pela linha de comando;
- controlar corretamente linha e coluna;
- diferenciar palavras reservadas de identificadores;
- reconhecer operadores de um e dois caracteres;
- ignorar espaços, tabulações, quebras de linha e comentários `//`;
- detectar e informar erros léxicos;
- continuar a análise após encontrar um erro;
- contar tokens e erros léxicos;
- evitar acessos fora dos limites dos vetores;
- ser organizada em funções;
- fechar o arquivo antes de encerrar;
- compilar sem erros e sem avisos relevantes.

Não utilize bibliotecas prontas de análise léxica, expressões regulares ou geradores como Lex e Flex.

---

## 8. Sugestão de organização do código

Você pode representar os tipos de token com uma enumeração:

```c
typedef enum {
    TOKEN_PALAVRA_RESERVADA,
    TOKEN_IDENTIFICADOR,
    TOKEN_NUMERO_INTEIRO,
    TOKEN_NUMERO_REAL,
    TOKEN_LITERAL_CARACTERE,
    TOKEN_OPERADOR,
    TOKEN_DELIMITADOR,
    TOKEN_ERRO
} TipoToken;
```

Também pode criar uma estrutura para guardar um token:

```c
typedef struct {
    TipoToken tipo;
    char lexema[64];
    int linha;
    int coluna;
} Token;
```

Algumas funções que podem ajudar:

```c
int ehPalavraReservada(const char *lexema);
int ehInicioIdentificador(char c);
int ehParteIdentificador(char c);
int ehDelimitador(char c);
void imprimirToken(const Token *token);
const char *nomeDoToken(TipoToken tipo);
```

Você pode adotar outra organização, desde que o código permaneça modular e legível.

### Atenção à leitura antecipada

Em algumas situações, será necessário ler o próximo caractere para decidir qual token existe:

- `/` pode ser divisão ou início de comentário;
- `=` pode ser atribuição ou parte de `==`;
- `>` pode aparecer sozinho ou como parte de `>=`;
- um ponto depois de algarismos pode indicar um número real.

Se o caractere lido antecipadamente não fizer parte do token atual, você poderá devolvê-lo ao fluxo com `ungetc` ou armazená-lo para o próximo processamento.

### Atenção à posição

- A primeira linha e a primeira coluna são numeradas com `1`.
- Ao encontrar `\n`, incremente a linha e reinicie a coluna.
- A posição exibida deve ser aquela em que o token começou.

---

## 9. Casos de teste obrigatórios

Crie arquivos `.mc` que testem, pelo menos:

1. todas as palavras reservadas;
2. identificadores válidos;
3. um identificador com mais de 31 caracteres;
4. números inteiros;
5. números reais;
6. números reais malformados;
7. todos os operadores;
8. todos os delimitadores;
9. comentários;
10. literais de caractere válidos e inválidos;
11. caracteres inválidos;
12. um arquivo vazio;
13. um arquivo contendo somente espaços e comentários;
14. tokens sem espaços entre eles, como:

```c
if(x>=10){x=x+1;}
```

Não se limite ao exemplo fornecido no enunciado. Seus testes deverão demonstrar que o analisador também funciona em situações de erro e em casos de fronteira.

---

## 10. Compilação e execução

Compile com GCC usando:

```bash
gcc -Wall -Wextra -pedantic -std=c11 minilexer.c -o minilexer
```

No Linux ou macOS:

```bash
./minilexer exemplo.mc
```

No Windows:

```powershell
.\minilexer.exe exemplo.mc
```

Corrija os avisos apresentados pelo compilador antes da entrega.

---

## 11. Entregáveis

Entregue:

- `minilexer.c`, contendo sua implementação;
- os arquivos `.mc` utilizados nos testes;
- `README.md` ou `README.txt`, com instruções de compilação e execução;
- um relatório curto, com até três páginas.

No arquivo README, informe:

- como compilar o programa;
- como executá-lo;
- quais decisões de implementação foram tomadas;
- quais limitações ainda existem;
- quais testes foram realizados.

No relatório, apresente:

- uma explicação resumida da análise léxica;
- a estratégia ou o algoritmo utilizado;
- exemplos de entrada e saída;
- as principais dificuldades encontradas;
- a divisão do trabalho, se a atividade for realizada em grupo.

---

## 12. Critérios de avaliação

| Critério | Pontuação |
|---|---:|
| Reconhecimento de palavras reservadas e identificadores | 1,5 |
| Reconhecimento de números inteiros e reais | 1,5 |
| Reconhecimento de operadores e delimitadores | 1,5 |
| Tratamento de comentários e espaços | 1,0 |
| Detecção e recuperação de erros léxicos | 1,5 |
| Controle correto de linha e coluna | 1,0 |
| Organização, modularização e legibilidade | 1,0 |
| Testes, instruções e relatório | 1,0 |
| **Total** | **10,0** |

Uma solução que não compile poderá ter sua pontuação limitada, mesmo que apresente partes conceitualmente corretas.

---

## 13. O que não precisa ser implementado

Seu analisador não precisa:

- verificar se parênteses ou chaves estão balanceados;
- verificar a ordem ou a sintaxe dos comandos;
- verificar tipos de variáveis;
- montar uma árvore sintática;
- gerar código intermediário ou código de máquina;
- executar o programa MiniC;
- reconhecer comentários `/* ... */`;
- reconhecer strings entre aspas duplas.

Essas tarefas pertencem a outras etapas de um compilador ou estão fora do escopo desta atividade.

---

## 14. Desafios opcionais

Depois de concluir todos os requisitos obrigatórios, você poderá implementar:

- comentários de múltiplas linhas;
- strings entre aspas duplas;
- sequências de escape em literais de caractere;
- números em notação científica, como `1.5e3`;
- uma tabela de símbolos com os identificadores encontrados;
- gravação dos tokens em arquivo;
- saída no formato CSV;
- uma opção `--resumo` com a quantidade de tokens por categoria.

Os desafios opcionais não substituem os requisitos obrigatórios.

---

## 15. Questões para responder

Inclua no relatório as respostas às seguintes questões:

1. Por que palavras reservadas e identificadores podem começar sendo reconhecidos pela mesma regra?
2. Por que operadores de dois caracteres devem ser verificados antes dos operadores de um caractere?
3. Qual é a diferença entre um erro léxico e um erro sintático?
4. Por que o analisador deve continuar trabalhando depois de encontrar um símbolo inválido?
5. Qual é o risco de não verificar o limite do vetor utilizado para armazenar um lexema?
6. Em qual etapa seria detectado o problema em `int = 10;`, considerando que todos os caracteres formam tokens válidos?
7. Qual seria a vantagem de armazenar os identificadores encontrados em uma tabela de símbolos?

---

## 16. Antes de entregar

Use esta lista para revisar seu trabalho:

- [ ] O programa compila com as opções solicitadas.
- [ ] O nome do arquivo é recebido pela linha de comando.
- [ ] Todos os tipos obrigatórios de token são reconhecidos.
- [ ] Comentários e espaços são ignorados corretamente.
- [ ] Linha e coluna estão corretas.
- [ ] Erros léxicos são informados sem interromper a análise.
- [ ] Não há escrita fora dos limites dos vetores.
- [ ] Os arquivos de teste cobrem casos válidos, inválidos e casos de fronteira.
- [ ] O README contém instruções completas.
- [ ] O relatório responde às questões propostas.

**Modalidade:** individual ou em dupla, conforme orientação do professor.  
**Prazo:** informado pelo professor.  
**Valor:** 10,0 pontos.
