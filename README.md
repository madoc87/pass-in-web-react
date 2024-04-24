# pass.in

O pass.in é uma aplicação de gestão de participantes em eventos presenciais.

A ferramenta permite que o organizador cadastre um evento e abra uma página pública de inscrição.

Os participantes inscritos podem emitir uma credencial para check-in no dia do evento.

O sistema fará um scan da credencial do participante para permitir a entrada no evento.

## Requisitos

### Requisitos funcionais

- [  ]  O organizador deve poder cadastrar um novo evento;

- [  ]  O organizador deve poder visualizar dados de um evento;

- [  ]  O organizador deve poder visualizar a lista de participantes;

- [  ]  O participante deve poder se inscrever em um evento;

- [  ]  O participante deve poder visualizar seu crachá de inscrição;

- [  ]  O participante deve poder realizar check-in no evento;

### Regras de negócio

- [  ]  O participante só pode se inscrever em um evento uma única vez;

- [  ]  O participante só pode se inscrever em eventos com vagas disponíveis;

- [  ]  O participante só pode realizar check-in em um evento uma única vez;

### Requisitos não-funcionais

- [  ]  O check-in no evento será realizado através de um QRCode;


## Documentação da API (Swagger)

Para documentação da API, acesse o link: https://nlw-unite-nodejs.onrender.com/docs

## Banco de dados

Nessa aplicação vamos utilizar banco de dados relacional (SQL). Para ambiente de desenvolvimento seguiremos com o SQLite pela facilidade do ambiente.

### Diagrama ERD

<img src="erd.svg" width="600" alt="Diagrama ERD do banco de dados" />

### Estrutura do banco (SQL)

```sql
-- CreateTable
CREATE TABLE "events" (
    "id" TEXT NOT NULL PRIMARY KEY,
    "title" TEXT NOT NULL,
    "details" TEXT,
    "slug" TEXT NOT NULL,
    "maximum_attendees" INTEGER
);

-- CreateTable
CREATE TABLE "attendees" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "name" TEXT NOT NULL,
    "email" TEXT NOT NULL,
    "event_id" TEXT NOT NULL,
    "created_at" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT "attendees_event_id_fkey" FOREIGN KEY ("event_id") REFERENCES "events" ("id") ON DELETE RESTRICT ON UPDATE CASCADE
);

-- CreateTable
CREATE TABLE "check_ins" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "created_at" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "attendeeId" INTEGER NOT NULL,
    CONSTRAINT "check_ins_attendeeId_fkey" FOREIGN KEY ("attendeeId") REFERENCES "attendees" ("id") ON DELETE RESTRICT ON UPDATE CASCADE
);

-- CreateIndex
CREATE UNIQUE INDEX "events_slug_key" ON "events"("slug");

-- CreateIndex
CREATE UNIQUE INDEX "attendees_event_id_email_key" ON "attendees"("event_id", "email");

-- CreateIndex
CREATE UNIQUE INDEX "check_ins_attendeeId_key" ON "check_ins"("attendeeId");
```

---

# Anotações

## Componentes
`Componentes` são formas de se separar a nossa aplicação em vários blocos;

`Componentes` geralmente são criados em duas situações:

- <b>Primeiro:</b> Quando vemos um padrão de repetição

- <b>Segundo:</b> Quando conseguimos desconectar uma parte da interface da outra, porque elas não se conversão entre si, são elementos que funcionam quase que independentes um do outro, que eles possam existir sem outra parte do código, como por exemplo um cabeçalho que pode existir em várias páginas da mesma aplicação e ela não está diretamente ligada com a página que está sendo carregada. Então nessa aplicação o cabeçalho será um `componente` e a lista de eventos ou a lista de participantes será outro `componente`;

- `Componentes` são funções que retornam um HTML, `Componentes` podem ser comparados a elementos dentro do HTML;

- Elementos que se repetem na aplicação, como um padrão de repetição, como por exemplo os botões, uma linha com os dados do participante, etc;

- No React todo `componente` inicia com uma letra maiúscula;

- O React usa `className` no lugar de `class`, porque `class` é uma palavra reservada para o JavaScript;

- Todo componente, assim como as Tags no HTML eles podem receber um atributo, com exemplo:
    - A tag `<img>` pode receber vários atributos como `src`, `width`, etc.

#### PROPRIEDADES
Os Componentes no React podem receber tambem um atributo, que são chamados de `PROPRIEDADES`;

As Propriedades são declaradas da mesma forma que os atributos no HTML, então vamos criar um componente chamado `MeuBotão` e adicionar uma propriedade chamada `texto` com conteúdo `Clique aqui`, por exemplo:

<code>
//Criação do componente botão

    function MeuBotão() {
        return <button>Teste</button>
    }
</code>

Essa função acima irá criar o componente com um botão e o texto padrão dentro dele é `Teste`, para usar a propriedade que iremos adicionar a abaixo devemos usar a palavra `props` como argumento da função e depois passar o conteudo dela no lugar da palavra `Teste`, mas como `props` é uma variável dentro do JS para ela ser usada deve ser entre chaves, caso contrário ela será reconhecida como um texto simples. 

Depois de adicionar dentro do texto do botão a variável `props` precisamos detalhar qual atributo estamos querendo acessar e para isso adicionamos um ponto e o nome do atributo que nesse caso será `texto`, então o código ficará assim:

<code>
    //Alteração do componente para receber uma propriedade e usar ela como o texto do botão

    function MeuBotão(props) {
        return <button>{props.texto}</button>
    }
</code>

Com componente criado iremos agora usar o botão e passar o atributo nele que será usado para o nome:

<code>
    //Uso do componente

    <MeuBotão texto="Clique aqui" />
</code>

- Podemos criar quantas propriedades forem necessárias;

#### TypeScrip

- Em caso do uso de TypeScrip o código irá apresentar um erro, pois as `props` não foram tipadas, então para corrigir isso iremos adicionar no início do código as linhas abaixo:

<code>

    interface MeuBotaoProps {
        texto: String
    }
</code>

- Depois da declaração do tipo de conteudo o atributo `texto` vai receber (que no caso é um texto e por isso foi declarada como uma String), temos que adicionar no componente, adicionando depois da palavra `props` dois pontos e o nome que foi dado na declaração acima:

<code>
    
    function MeuBotão(props: MeuBotaoProps) {
        return <button>{props.texto}</button>
    }
</code>

- Isso traz uma inteligência para a escrita do código não permitindo que alguns erros simples sejam cometidos e facilitando pelo reconhecimento de como os Componentes funcionam, por exemplo no proximo uso do componente `MeuBotão` se o atributo (`props`) não for declarado o código apresentará um erro informando que está faltando uma propriedade obrigatória;

## Tailwind

- Para desenvolver o layout desse aplicação iremos utilizar o Tailwind CSS;

- Tailwind é um `utility class`;

### Children
`Children`, são propriedades enviadas dentro das tags, por exemplo
o componente `<NavLink>` é um Link do Menu na declaração dele vamos usar da seguinte forma:

<code>

    <NavLink>Eventos</NavLink>
</code>

- O texto eventos não está sendo passada como uma `props` do componente, mas como um texto dentro da tag, então não pode ser usada da mesma forma, para isso vamos usar o `Children`, associado com o `props`, pois é como um filho da tag `NavLink`, que está dentro dela, ficando assim:

<code>
    
    export function NavLink(props) {
        return (
            <a>{props.children}</a>
        )
    }
</code>

- Assim o texto que for enviado dentro da tag `<NavLink>` será adicionado ao componente;

## Extends ComponentProps
Como estender todas as propriedades de um elemento nativo do HTML e repassar todas elas para um elemento dentro do componente

Normalmente para repassar propriedades para um componente devemos enviar dentro do HTML e depois receber cada uma delas no componente, por exemplo, vamos manter o exemplo do `NavLink` anterior:

No HTML vamos declarar:

<code>
    
    <NavLink href="/meulink" title="Meu Titulo" target="_blank" />
</code>


E no componente vamos ter que receber essas `props`:

<code>
    
    export function NavLink(props) {
        return (
            <a props.href props.title props.target>{props.children}</a>
        )
    }
</code>

E fazer isso para todos os elementos do HTML seria muito trabalhoso, então para facilitar podemos usar todas as propriedades enviadas do HTML para o componente, usando uma importação do React chamada `ComponentsProps` importando ela no início do código e alterando como o componente recebe os atributos ficando assim:

<code>
    
    import { ComponentProps } from "react"
    
    export function NavLink(props) {
        return (
            <a {...props} className="font-medium text-sm">{props.children}</a>
        )
    }
</code>

o uso do `...props` vai trazer todos os elementos enviados no HTML na tag;


#### Ajuste do TypeScript 

No caso de estar usando o `TypeScript` no projeto o mesmo apresentará erro por não ter declarado a tipagem dos elementos, então no caso do `children`, vamos adicionar a tipagem como `string` e para o uso do ComponentProps vamos usar o `extends` ficando assim:

<code>

    import { ComponentProps } from "react"
    
    interface NavLinkProps extends ComponentProps<'a'> {
        children: string
    }
    
    export function NavLink(props: NavLinkProps) {
        return (
            <a {...props} className="font-medium text-sm">{props.children}</a>
        )
    }
</code>


Então iremos adicionar logo abaixo do `import` a `interface` com o nome que iremos usar no componente e estender para o `ComponentProps` e adicionar a tag que será usada no componente, no caso a tag `a` e assim o `TypeScrip` já reconhece todos os tipos que serão usados no componente vão ser os mesmo da tag HTML `<a>` e entao logo em seguida vamos declarar apenas o tipo da variável `children`. 

E por fim vamos adicionar na função do componente o nome que criamos ao declarar a `interface` antecedido por dois pontos;


## Estados no React

Estados React na mais sao do que variáveis, porém essas variáveis sao observáveis e quando o valor dessas variáveis é alterado, por qualquer motivo que seja o componente renderiza novamente, ou seja o componente é criado em tela novamente. 

Por exemplo toda vez que um usuário digitar um valor dentro de um `input`, esse valor vai ser mostrado em tela em tempo real. Isso pode ser feito adicionando um atributo do tipo `listening` como o `onChange`;
 
 Então para capturar o valor do `input` a função abaixo ela recebe um evento que vamos chamar de `event` e dentro desse evento existem várias informações e uma delas é o `target` e dentro do `target` tem o `value` que é o conteúdo do que está sendo digitado no `input`, então para acessar esse valor precisamos: 


<code>
[TSX]

    function nomeDaFuncao(event) {}
    console.log(event.target.value)
</code>

<code>
    [HTML]

    <input onChange={nomeDaFuncao} />
</code>



Como estamos usando o `TypeScrip` toda vez que criamos uma função temos que definir qual que é o formato da variavel `event`. Se passarmos o mouse no `onChange` do `input` ele fala que é um `ChangeEventHandler` e ao pressionar `Ctrl` e clicar em cima do `onChange` ele abre um outro arquivo mostrando esse evento e ao clicar novamente com o `Ctrl` pressionado no `ChangeEventHandler` ele irá mostrar qual o evento que ele envia que no caso é um `ChangeEvent`, então isso pode ser usado na função. Mas o `ChangeEvent` pode ser disparado por varios elementos, então precisamos passar pra ele qual que é o elemento que está disparando, então temos que adicionar um `<HTMLInputElement>`, então com isso estamos falando que quem está disparando o evento é um `input` assim ele sabe que existe o `value`, porque o `value` so existe dentro do um `input`;

<code>
    [TSX]

    function nomeDaFuncao(event: ChangeEvent<HTMLInputElement>) {}
    console.log(event.target.value)
</code>

<code>
    [HTML]

    <input onChange={nomeDaFuncao} />
</code>

Então agora que já estamos capturando o valor do digitado no `input` ele ainda não será mostrado porque o elemento é criado apenas umas vez, então vamos usar um conceito do React chamado `Estado` e é declarado com uma variável recebendo o `useState()` e ele retorna um array com duas posições, então ao invés de declarar como:

<code>
    
     const valorDoInput = useState('')
</code>


Vamos desestruturar esses arrays, onde a primeira posição recebemos o valor do input que vamos chamar de `valorDoInput` e na segunda posição vamos receber uma função para alterar esse valor do input que vamos chamar de `alteraOValorDoInput`. Então para alterar o valor do `input` vamos chamar a função `alteraOValorDoInput` e adicionar a variável `valorDoInput` no HTML da página entre chaves, com o código ficando assim:

<code>
     [TSX]

    const [valorDoInput, alteraOValorDoInput] = useState('')
    
    function nomeDaFuncao(event) {
        alteraOValorDoInput(event.target.value)
    }
</code>

<code>
    [HTML]

    <input onChange={nomeDaFuncao} />
    {valorDoInput}
</code>

Assim ao digitar no input o que for digitado aparece em tempo real onde a variável `valorDoInput` estiver, visto que sempre que o valor do input for alterado, ele chama a função `nomeDaFuncao` e ela recebe o `event` com todas as informações incluindo o valor do que está sendo digitado e chama a função `alteraOValorDoInput` que está recebendo apenas o valor do `input` e altera o valor da variável `valorDoInput` com a propriedade `useState`.

Ou seja, o ESTADO no React nada mais é que uma forma de `OBSERVAR`, `MONITORAR`, `ASSITIR` as mudanças de valor de uma variável seja ela qual for, como uma `String`, um `number`, um `array`, um `object`, etc em tempo real.


## useEffect()

O `useEffect` é uma forma do React disparar uma função somente quando a gente quiser, e isso é determinado apartir do segundo parametro que é passado para o `useEffect` que é um array. Esse array ele pode conter ou nao algumas variaveis.

<code>
    
    userEffect(() => {}, [page])
</code>


Nesse caso a primeiro parametro que é uma função só vai ser executada quando o segundo parametro/estado `page` for alterado, se qualquer outro parametro for alterado ele não vai ser executado.


## URL State 
O `URL State` é armazenar na URL da aplicação todo o estado que vem do input do usuario e que queremos persistir quando o usuario recarregar a pagina ou o usuario enviar o link para outra pessoa.


### OBS.

Caso o TypeScript não reconheça as novas tabelas do banco pode forçar ele reiniciar:

Na aba do arquivo TS pressionar o atalho `Ctrl` + `Shift` + `P` e digitar 

> TypeScript: Restart TS Server
