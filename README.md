# # Sistema de Biblioteca em C

Projeto acadêmico desenvolvido em **seis versões incrementais**, em que cada versão acrescenta um conceito da linguagem C e reaproveita o que já funcionava. O objetivo não é chegar rápido ao programa final, mas entender **como cada conteúdo estudado resolve um problema real** do sistema.

O resultado final é um sistema de console capaz de cadastrar livros e usuários, listar e buscar registros, além de controlar empréstimos e devoluções com regras completas de validação.

---

## Evolução do projeto

| Versão | Arquivo | Conteúdo em foco | O sistema passa a conseguir... |
|:------:|---------|------------------|--------------------------------|
| 1 | `biblioteca_v1.c` | Vetores, repetições e decisões | Controlar códigos e quantidades em estoque |
| 2 | `biblioteca_v2.c` | Strings | Exibir uma descrição compreensível de cada livro |
| 3 | `biblioteca_v3.c` | Funções | Separar cadastro, listagem, busca e cálculos em operações |
| 4 | `biblioteca_v4.c` | Structs | Manter todos os dados de um livro na mesma entidade |
| 5 | `biblioteca_v5.c` | Ponteiros | Permitir que funções alterem livros e usuários originais |
| 6 | `biblioteca_v6.c` | Integração | Cadastrar, buscar, emprestar e devolver com regras completas |

> **Regra de ouro:** só avance para a próxima versão quando a atual compilar sem erros, executar corretamente e passar nos testes mínimos.

---

## Regras e limites do protótipo

- No máximo **3 títulos** de livros e **3 usuários** distintos (cada título pode ter mais de 3 exemplares).
- Cada código de livro ou usuário é um **inteiro positivo e único**.
- Os dados textuais do livro (título, autor, ano e tema) ficam em **uma única string**, separados por ponto e vírgula:
  `O Hobbit; J. R. R. Tolkien; 1937; Fantasia`
- Os dados pessoais do usuário também ficam em uma única string.
- Cada usuário pode ter **no máximo um livro emprestado** por vez.
- Mensagens de erro **nunca alteram** os dados do sistema.

---

## Estruturas de dados (versão final)

```c
typedef struct {
    int codigo;
    char descricao[TAM_TEXTO];
    int estoque;
} Livro;

typedef struct {
    int codigo;
    char dados[TAM_TEXTO];
    int codigoLivroEmprestado;   // -1 indica "nenhum livro emprestado"
} Usuario;

Livro livros[MAX_LIVROS];
Usuario usuarios[MAX_USUARIOS];
```

---

## Menu do sistema

```
1 - Cadastrar livro
2 - Cadastrar usuário
3 - Listar livros
4 - Buscar livro
5 - Emprestar livro
6 - Devolver livro
0 - Sair
```

Uma opção inválida repete o menu e a opção `0` encerra o programa.

---

## Funções principais

| Função | Responsabilidade |
|--------|------------------|
| `int buscarLivro(Livro livros[], int qtd, int codigo)` | Devolve o índice do livro ou `-1` se não existir |
| `int buscarUsuario(Usuario usuarios[], int qtd, int codigo)` | Devolve o índice do usuário ou `-1` se não existir |
| `int emprestarLivro(Livro *livro, Usuario *usuario)` | Realiza o empréstimo. Retorna `1` (sucesso) ou `0` (falha) |
| `int devolverLivro(Livro *livro, Usuario *usuario)` | Realiza a devolução. Retorna `1` (sucesso) ou `0` (falha) |
| `void alterarEstoque(Livro *livro, int valor)` | Altera o estoque do livro original |
| `void registrarEmprestimo(Usuario *usuario, int codigoLivro)` | Registra o livro emprestado ao usuário |
| `void encerrarEmprestimo(Usuario *usuario)` | Libera o usuário (`codigoLivroEmprestado = -1`) |

---

## Regras de negócio

### Cadastro
- Não cadastrar mais de 3 livros nem mais de 3 usuários.
- Não aceitar códigos repetidos, negativos ou iguais a zero.
- Não aceitar estoque negativo.

### Empréstimo
Só acontece quando:
- o livro existe;
- o usuário existe;
- o estoque do livro é maior que zero;
- o usuário não possui outro livro emprestado.

Quando realizado: o **estoque do livro diminui 1** e o **usuário recebe o código do livro**.

### Devolução
Só acontece quando:
- o usuário existe;
- o usuário possui um livro emprestado;
- o livro existe;
- o código do livro corresponde ao empréstimo registrado no usuário.

Quando realizada: o **estoque aumenta 1** e o **código no usuário volta para `-1`**.

> As duas alterações (livro e usuário) devem acontecer **juntas**. Nunca pode existir uma atualização parcial.

### Falhas
Se qualquer regra falhar, o sistema mostra uma mensagem, **não altera nenhum dado** e volta ao menu.

---

## Como compilar e executar

Pré-requisito: um compilador C, como o [GCC](https://gcc.gnu.org/).

```bash
gcc biblioteca_v6.c -o biblioteca -Wall
./biblioteca        # Linux / macOS
biblioteca.exe      # Windows
```

---

## Roteiro de testes (versão 6)

| # | Teste | Resultado esperado |
|:-:|-------|--------------------|
| 1 | Cadastrar livro `101` (estoque 2) e usuário `1` | Cadastros aceitos; usuário inicia com `-1` |
| 2 | Emprestar livro `101` ao usuário `1` | Estoque vira 1; usuário registra `101` |
| 3 | Novo empréstimo para o mesmo usuário | Recusado; nada é alterado |
| 4 | Devolver livro `101` pelo usuário `1` | Estoque volta a 2; usuário volta a `-1` |
| 5 | Devolução repetida | Recusada; estoque 2 e usuário `-1` |
| 6 | Emprestar livro com estoque zero | Recusado; nada é alterado |
| 7 | Usar códigos inexistentes em busca, empréstimo e devolução | Mensagens claras; menu continua funcionando |

---

## Estrutura do repositório

```
.
├── biblioteca_v1.c
├── biblioteca_v2.c
├── biblioteca_v3.c
├── biblioteca_v4.c
├── biblioteca_v5.c
├── biblioteca_v6.c
└── README.md
```

---

## Próximos passos: sugestão para a Versão 7

**Persistência de dados em arquivos.**

- **Problema resolvido:** hoje todos os dados são perdidos quando o programa fecha.
- **Funcionalidade nova:** salvar livros e usuários em arquivo ao sair e carregá-los ao iniciar.
- **Novo conteúdo de C:** manipulação de arquivos (`fopen`, `fread`, `fwrite`, `fclose`) e, opcionalmente, alocação dinâmica (`malloc`, `realloc`) para remover o limite fixo de 3 registros.
- **Aproveitamento do que existe:** as structs `Livro` e `Usuario` podem ser gravadas diretamente em arquivo, e as funções de cadastro, busca, empréstimo e devolução continuam iguais.

---

## Conceitos praticados

`vetores` · `strings (fgets)` · `funções` · `escopo` · `structs` · `typedef` · `ponteiros` · `passagem por endereço` · `operador ->` · `validação de entrada` · `menu com laço`

---

## Licença

Projeto desenvolvido para fins educacionais.
