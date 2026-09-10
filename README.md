# Checkpoint 4 — Bug Hunt StreamFIAP


## Identificação

**Grupo:** AuABugHunters

| Integrante | RM | Turma |
|---|---|---|
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

O ConteudoRepository é uma interface, então new ConteudoRepository() nem compila, porque não existe uma classe pronta escrita por nós. Quem cria essa classe é o Spring, quando a aplicação sobe, e ela já vem ligada ao banco configurado no application.properties. Quando o Spring cria o ConteudoController, ele vê o @Autowired e coloca o repository lá dentro sozinho. Se a gente usasse new, teria que montar a conexão com o banco na mão em cada controller. Com a injeção, o controller só diz do que precisa e o Spring entrega pronto. Os três controllers ainda usam o mesmo objeto.

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)

No JDBC, como no ProdutoDAO, a gente fazia tudo na mão: abria a conexão, escrevia o SQL, preenchia os parâmetros, lia o ResultSet linha por linha e fechava tudo no final. No projeto, o ConteudoRepository tem só 2 linhas e já sabe salvar, buscar, listar e apagar, porque o Spring cria esses métodos e escreve o SQL sozinho. O findByCategoria funciona sem código porque o Spring lê o nome do método ("buscar por categoria") e monta o WHERE categoria = ? automaticamente. Foi ele que usamos para corrigir o bug03. O JDBC ainda é melhor quando precisamos controlar o SQL de perto, como em consultas complicadas ou quando a velocidade é muito importante, porque no JPA a gente não vê o SQL que está rodando.

### 3. Exceções checked vs unchecked (Aula 11)

Uma exceção que faz extends Exception é checked: o compilador obriga a colocar throws ou try/catch. Por isso o código tinha throws ClassificacaoIndicativaException no Usuario.alugar e no AluguelController. Só que o GlobalExceptionHandler não tinha um método para essa exceção, então o Spring devolvia erro 500 sem mensagem nenhuma. Uma exceção que faz extends RuntimeException é unchecked: não obriga throws, e é a mais usada para regras de negócio. As outras 3 exceções do projeto já eram assim. Mudamos para RuntimeException, tiramos os throws e criamos um handler que devolve 403 com a mensagem "Usuário de 12 anos não pode assistir a Matrix (classificação 14 anos)". Só mudar o tipo não resolveria: sem o handler, continuaria dando 500.

### 4. Sobrescrita vs sobrecarga (Aula 7)

Sobrescrever (override) é criar na subclasse um método com o mesmo nome e os mesmos parâmetros do pai, para trocar o que ele faz. Sobrecarregar (overload) é criar um método com o mesmo nome, mas com parâmetros diferentes, e aí ele vira outro método. A Serie tinha calcularPrecoAluguel(double desconto): como tinha um parâmetro a mais, era sobrecarga. Então, quando o sistema chamava calcularPrecoAluguel() sem parâmetro, rodava o método do Conteudo, que devolvia 9,90, e a série saía com o preço errado. Com @Override, o compilador daria erro, porque o pai não tem esse método com parâmetro, e o bug nem compilaria. Por isso colocamos @Override e tiramos o parâmetro.

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)

No construtor fica o que o objeto precisa ter certo desde que nasce. Por isso a regra de duracaoMinutos <= 0 (bug12) ficou no construtor do Conteudo, que Filme, Série e Documentário chamam com super(...). Foi no construtor também que apareceram os campos vazios: o nome = nome do Usuario (bug04) e a Serie que não chamava super (bug05). No setter, a mesma regra deveria valer quando o valor muda depois; no nosso caso, o controller sempre cria o objeto pelo construtor, então a validação do construtor já barra os dados errados que chegam pela API. Já as regras que dependem da situação na hora, como ter créditos, o conteúdo estar disponível e a idade mínima, ficam nos métodos do model, no Usuario.alugar (bugs 09, 10 e 11). Validar em um lugar só não bastou porque o objeto pode ser criado ou alterado de vários jeitos. O bug05 mostra isso: a validação estava no Conteudo, mas a Serie não chamava o super e pulava a validação.

### 6. Abstração e interface (Aulas 8 e 9)

Conteudo é abstrata porque junta o que todo conteúdo tem em comum (título, categoria, duração, classificação e disponibilidade), e não faz sentido criar um "conteúdo" solto: sempre é um Filme, uma Série ou um Documentário. Ela também obriga cada tipo a dizer o seu preço, porque o calcularPrecoAluguel() é abstrato. Já Promocionavel é uma interface porque é algo que só alguns conteúdos fazem: entrar em promoção. Filme e Série implementam, o Documentário não. Se o Documentário passasse a ter promoção, só o Documentario.java mudaria: bastaria colocar implements Promocionavel e o método aplicarPromocao. O resto (Conteudo, Filme, Serie e os controllers) ficaria igual. Isso mostra que o sistema é fácil de aumentar sem mexer no que já funciona.

---

## Parte 4 — Espaço livre (opcional)

```text
