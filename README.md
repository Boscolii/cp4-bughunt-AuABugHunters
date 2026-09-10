# Checkpoint 4 — Bug Hunt StreamFIAP


## Identificação

**Grupo:** AuABugHunters
|---|---|---|
| Integrante | RM | Turma |
|Erick Gimenez|564748|2CCPY|
|Henrique Boscoli |563651 |2CCPY |
|Joao Henrique |563578 |2CCPY |
|Sergio Mirabelo |562161 |2CCPY |
|Tomazzo Canterucci |565566 |2CCPY |

| Campo | |
|---|---|
| **Total de bugs corrigidos** | 12 / 12 |
| **Total de ajustes de Clean Code** | 6 / 6 |

---

## Parte 1 — Bugs encontrados

| # | Sintoma observado (o que fiz/vi) | Causa raiz (arquivo e linha aproximada) | Correção aplicada | Conceito da disciplina |
|---|---|---|---|---|
| bug01 | Ao cadastrar um usuário com nome, o retorno da API e o banco apresentavam o campo nome como null. | Usuario.java, linha 22: nome = nome atribuía o parâmetro a ele mesmo. | Alterei para this.nome = nome. | Uso de this; atributo de instância e parâmetro do construtor. |
| bug02 | Ao cadastrar um usuário sem ID, o sistema não gerava identificador automaticamente e o salvamento podia falhar. | Usuario.java, linha 11: o campo id tinha somente @Id. | Adicionei @GeneratedValue(strategy = GenerationType.IDENTITY). | JPA; chave primária e geração automática de identificadores. |
| bug03 | Usuário com créditos suficientes não conseguia alugar; usuário sem saldo conseguia alugar e ficava com créditos negativos. | Usuario.java, linha 28: a comparação em temCreditosSuficientes estava invertida. | Corrigi a condição para this.creditos >= preco. | Operadores relacionais; regra de negócio e validação de estado. |
| bug04 | Foi possível alugar um conteúdo que já estava indisponível. | Usuario.java, método alugar: não havia verificação de disponibilidade. | Adicionei validação que lança ConteudoIndisponivelException. | Regras de negócio; condicionais; exceções. |
| bug05 | Usuário menor de idade recebia erro genérico, sem a mensagem da classificação indicativa. | ClassificacaoIndicativaException estendia Exception e não havia handler global para ela. | Transformei em RuntimeException e criei handler global com status 422 e mensagem clara. | Exceções checked e unchecked; tratamento global de exceções. |
| bug06 | Ao buscar conteúdo inexistente, a API retornava resposta vazia em vez de erro de conteúdo não encontrado. | ConteudoController.java, linhas 32 a 39: catch (Exception) ignorava a exceção e o método retornava null. | Removi o try/catch e deixei a exceção chegar ao handler global. | Tratamento de exceções; não silenciar erros. |
| bug07 | Ao buscar conteúdos de categoria existente, como FICCAO, a API podia retornar lista vazia. | ConteudoController.java, linha 47: comparação de String com ==. | Substituí a busca manual por findByCategoria(categoria). | Comparação de String; Spring Data JPA e consultas derivadas. |
| bug08 | Ao cadastrar série, dados herdados como título, categoria, duração e classificação não eram salvos; ela também ficava indisponível. | Serie.java, linhas 14 a 16: o construtor não chamava super(...). | Adicionei a chamada ao construtor de Conteudo e a disponibilidade como parâmetro. | Herança; construtores; chamada a super. |
| bug09 | A série usava preço padrão de R$ 9,90, em vez de R$ 4,90 por temporada. | Serie.java, linha 19: o método tinha parâmetro e sobrecarregava, em vez de sobrescrever, calcularPrecoAluguel(). | Removi o parâmetro e adicionei @Override. | Polimorfismo; sobrescrita e sobrecarga. |
| bug10 | Documentário era cobrado em R$ 9,90 em vez de ser gratuito. | Documentario.java não sobrescrevia calcularPrecoAluguel(), herdando o preço padrão. | Adicionei sobrescrita retornando 0.0. | Herança; polimorfismo; sobrescrita de métodos. |
| bug11 | O preço promocional de filme aumentava em vez de receber desconto. | Filme.java, linha 25: multiplicava o preço por 1.2, gerando acréscimo de 20%. | Alterei o cálculo para multiplicar por 0.8. | Operações aritméticas; regra de negócio; interface. |
| bug12 | Foi possível cadastrar conteúdo com duração igual a zero ou negativa. | Conteudo.java, construtor e setter não validavam duracaoMinutos. | Adicionei validação no setter e fiz o construtor utilizá-lo. | Encapsulamento; validação de dados; invariantes do objeto. |

## Parte 2 — Ajustes de Clean Code

| # | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
|---|---|---|---|
| clean01 | Conteudo.java e métodos de cadastro em ConteudoController.java. | Encapsulamento: duracaoMinutos era público e podia ser acessado diretamente. | Tornei duracaoMinutos privado e substituí acessos diretos pelo getter. |
| clean02 | Método alugar em Usuario.java. | Nomes significativos: c e p não explicavam suas funções. | Renomeei para conteudo e precoAluguel. |
| clean03 | Método alugar em Usuario.java. | Responsabilidade Única: a entidade misturava regra de negócio com impressão no console. | Removi a impressão de recibo do método de aluguel. |
| clean04 | ConteudoController.java, método calcularDescontoAntigo. | Código morto: havia regra antiga sem uso. | Removi o método legado não utilizado. |
| clean05 | Final de ConteudoController.java. | Código comentado e obsoleto. | Removi o bloco comentado relacionado a cupons. |
| clean06 | ConteudoController, UsuarioController e AluguelController. | Dependências explícitas e testabilidade: injeção por atributo dificulta testes. | Substituí a injeção por atributo por injeção via construtor e usei atributos final. |

---

## Parte 3 — Perguntas de reflexão

### 1. Injeção de dependência (Aula 13)

[Responder.]

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)

[Responder.]

### 3. Exceções checked vs unchecked (Aula 11)

[Responder.]

### 4. Sobrescrita vs sobrecarga (Aula 7)

[Responder.]

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)

[Responder.]

### 6. Abstração e interface (Aulas 8 e 9)

[Responder.]

---

## Parte 4 — Espaço livre (opcional)

```text
