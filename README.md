# Anotações Java 8

---
## 1 Blocos de Construção do Java
---

### 1.1 Classes
Uma classe é um elemento do código Java que utilizamos para representar objetos do mundo real. Dentro dela é comum declararmos atributos e métodos, que representam, respectivamente, as características e comportamentos desse objeto.

#### 1.1.1 Estrutura de uma Classe
Em classes, Atributos e Métodos são tratados como "membros"
```java
public class Cachorro extends Animal implements MamiferoInterface {

    private String nome; //Atributo -> Membro

    public String getNome() { //Método -> Membro
        return this.nome;
    }

    public void setNome(String nome) { //Método -> Membro
        this.nome = nome;
    }
}
```
Regras para declaração de atributos:
- Zero ou mais modificadores, como ``public`` ou ``private``
- Tipo do atributo
- Nome do atributo

Regras para declaração de métodos:
- Zero ou mais modificadores, como ``public`` ou ``private``
- Tipo do retorno do método
- Parênteses e chaves seguidos pelo nome do método, exemplo ``myMethod(){}``

#### 1.1.2 Variáveis em nível de Classe
 |          Tipo          | _Default value_ |
 |:----------------------:|:---------------:|
 | byte, short, int, long |       0         |
 |     float, double      |      0.0        |
 |         char           |    '\u0000'     |
 |        boolean         |      false      |
 |        Object          |      null       |

⚠️ **IMPORTANTE:** Esses valores default só acontecem quando a varíavel é de escopo de Classe! Em um escopo de método, ou bloco de código, é necessário atribuir um valor à variável, senão ocorrerá um erro de compilação!
```java
public class ErroExemplo {

    private String myString; //Valor default -> null

    {
        System.out.println(myString); // null
    }

    {
        String myString2;
        System.out.println(myString2); // ❌ Variable 'myString2' might not have been initialized
    }

    public void test() {
        String myString3;
        System.out.println(myString3); // ❌ Variable 'myString3' might not have been initialized
    }
}
```
#### 1.1.3 Ordem dos elementos
Os elementos que compõe uma classe possuem uma ordem correta para serem inseridos. Uma forma fácil de memorizar a ordem correta dos elementos, é se lembrando da sigla **PIC** **_(Package | Import | Class)_**

|        Elemento        | Obrigatório? |              Onde?              |
|:----------------------:|:------------:|:-------------------------------:|
|   `package` a.b.c.d    |     não      |         Primeira linha          |
|  `import` java.util.*  |     não      |       Logo após `package`       |
| `public class` MyClass |     sim      |       Logo após `import`        |
|      `int` myVar       |     não      | Qualquer lugar dentro da Classe |
|    `void` myMethod     |     não      | Qualquer lugar dentro da Classe |

#### 1.1.4 Métodos Construtores
O construtor é normalmente utilizado para fornecer valores iniciais para variáveis de instâncias definidas pela classe ou uma forma de inicialização de um objeto já formado, ou seja, um construtor inicializa um objeto.
O Java possui implicitamente em cada classe um construtor *default*:
```java
class MyClass{
  public MyClass(){} //Construtor default (implícito)
}
```
⚠️ Quando o construtor default é sobreescrito por algum outro com parâmetros, o default deixa de existir. Ele precisa ser declarado explícitamente, caso queira fazer o uso dele.
```java
class MyClass{
  public MyClass(){} //Construtor default (preciso deixar explícito)
  public MyClass(String message){
    System.out.println(message);
  }
}
```
Os modificadores de acesso de construtores são os mesmos presentes em um método:
- **public**: Um construtor declarado como público pode ser acessado por qualquer classe, independentemente de onde ela esteja localizada.
- **protected:** Um construtor protegido pode ser acessado apenas por Classes ou Subclasses do mesmo pacote ou pelo construtor de subclasses em diferentes pacotes. **Atenção!** Construtores marcados como ``protected``, só são acessados em instância (``new MyConstructor()``) em classes/subclasses do MESMO PACOTE!! Em classes de diferentes pacotes, em que ocorre a herança, o acesso ao construtor ``protected`` só pode ser feito via chamada ``super()`` e não mais em instância ``new()``.
- **default (ou pacote-private):** Se nenhum modificador de acesso for especificado, o construtor é considerado como tendo acesso padrão, o que significa que ele só pode ser acessado por classes no mesmo pacote.
- **private:** Um construtor declarado como privado não pode ser acessado por nenhuma outra classe, mesmo que esteja no mesmo pacote. Normalmente, construtores privados são usados em classes que implementam o padrão Singleton ou em classes utilitárias.

**Sobrecarga de Construtores**
Eu posso realizar a sobrecarga de construtores, assim como são feito nos métodos. 

```java
class MyClass{
  
  private String name;
  private Integer age;

  // -------------------------------------------------------------------------------------------
  public MyClass(){ // 1º
      //Formas de chamar outros construtores:
      this("defaultName", 0); // ✅ Posso chamar outros construtores (3º) usando a palavra reservada 'this'
      MyClass("defaultName", 0); // ❌ Não compila!
      new MyClass("defaultName", 0); // ❌ Compila, mas instanciei um objeto. Há uma forma correta e melhor de chamar o outro construtor utilizando a palavra 'this'
  }

  public MyClass(){ // 1º
      //Quando chamo outro construtor com a palavra 'this', ele PRECISA ser o primeiro comando, vindo antes dele somente espaços e comentários.
      this("defaultName", 0); // ✅ 'this()' veio primeiro 
  }

  public MyClass(){ // 1º
      System.out.println("Can I do this?");
      this("defaultName", 0); // ❌ Não compila! Call to 'this()' must be first statement in constructor body - 'this()' DEVE vir primeiro 
  }
  // -------------------------------------------------------------------------------------------
  
  public MyClass(String name){  // 2º
    this.name = name;
    this
  }

  public MyClass(String name, Integer age){  // 3º
    this.name = name;
    this.age = age;
  }
}
```
**⚠️ ATENÇÃO:** Repare que também é possível chamar outros construtores usando a palavra reservada ``this()``. Mas fique atento(a)! Essa chamada para outros construtores só funcionam se for o **primeiro argumento** e se estiver **exclusivamente dentro de um construtor**! Se for usado em um método, ocasiona um erro de compilação.

**Uso de ``super()`` nos Construtores**
O uso da palavra reservada `super()` para chamar o construtor da classe pai. Atente-se às regras:
- O primeiro comando em um construtor é uma chamada a outro com ``this()`` ou ``super()``.
- ``super()`` DEVE ser o primeiro comando de um construtor
- Se seu construtor não tiver a chamada explícita a ``super()``, o Java irá adicionar implicitamente como primeiro argumento.
- Se a classe Pai não tem um construtor sem argumento e a classe filha não definir esse construtor, o compilador lançará um erro, pois, ele exige uma chamada para o construtor Pai em cada construtor filho.

**Exemplo:**
  ```java
  public class FatherClass {

    String name; 

    public FatherClass(String name){
      this.name = name;
    }
    
  }
  ```
  ```java
  public class DaughterClass extends FatherClass{ // ❌ Não compila! Como a classe pai não tem um construtor default, a classe filha PRECISA chamar explicitamente o construtor definido na classe Pai
    
  }
  ```
  ```java
  public class DaughterClass extends FatherClass{ // ❌ Não compila! Como a classe pai não tem um construtor default, a classe filha PRECISA chamar explicitamente o construtor definido na classe Pai
    public DaughterClass(){
    }
  }
  ```
  ```java
  public class DaughterClass extends FatherClass{ // ✅ Compila corretamente, foi realizado a chamada ao construtor Pai
    public DaughterClass(){
      super("fatherName"); //Faço a chamada do construtor Pai
    } 
  }
  ```

#### 1.1.5 Blocos inicializadores de instância
Blocos inicializadores de instância são blocos de código (representados por `{}`) que são executados no momento em que um objeto é instanciado. 
Antes de usar os blocos inicializadores de instância, saiba que há uma ordem específica de execução de código dentro de um objeto no momento de sua instância.
**Lembre-se:**
- Atributos e blocos inicializadores de instância **são executados na ordem em que aparecem no arquivo.** ⚠️
- O construtor é executado somente após a execução do item anterior. Ou seja, **é o último a ser executado.** ⚠️

**Exemplo:** ✅
```java
public class Patinho {

  // Ordem de execução: 3º
  public Patinho(){  
    nome = "amarelinho";
    System.out.println("atribuição no construtor");
  }

  // Ordem de execução: 1º
  private String nome = "feio"; 
  
  // Ordem de execução: 2º
  { System.out.println("atributo setado"); }

  public static void main (String[] args){
    Patinho patinho = new Patinho();
    System.out.println(p.nome);
  }
}

---------------------------------------------------------
Output: 
atributo setado
atribuição no construtor
amarelinho
```
Como a execução é feita com base na ordem em que os elementos são inseridos na classe, é muito comum ocorrer erros de referência.
**Exemplo:** ❌
```java
public class Patinho {

  // Ordem de execução: 1º
  { System.out.println(nome); }  //❌ Err: Cannot reference a fielt before it is defined (NÃO COMPILA!)

  // Ordem de execução: 2º
  private String nome = "feio";
  
}
```

#### 1.1.6 Ordem de inicialização
Dando continuidade ao conteúdo anterior, como já pudemos ver o Java possui uma ordem de inicialização específica dependendo da forma como sua classe foi construída. 
Atente-se com as seguintes regras:
- **1º** Se tem superclasse, então ela será inicializada primeiro. 
   Exemplo: suponha a seguinte instância: ``new MyClassC()`` e ``myClassC extends MyClassB`` e ``MyClassB extends MyClassA``. Primeiro, será executado todo o código seguindo a regra detalhada nesta lista em ``MyClassA``, posteriormente em ``MyClassB`` e por fim em ``MyClassC``. 
- **2º** Declarações de variáveis ``static`` e blocos inicializadores ``static {}`` na ordem em que aparecem no arquivo. As variáveis e blocos estáticos executam primeiro em ordem de herança, caso tenha superclasse. 
   Exemplo: suponha a seguinte instância: ``new MyClassC()`` e ``myClassC extends MyClassB`` e ``MyClassB extends MyClassA``. Primeiro, será executado os campos estáticos em ``MyClassA``, posteriormente em ``MyClassB`` e por fim em ``MyClassC``, e depois voltará para ``MyClassA`` executar o restante dos códigos conforme a a regra detalhada nesta lista. O mesmo ocorrerá com ``MyClassB`` e por fim em ``MyClassC``.
- **3º** Declarações de variáveis de instância e inicializadores de instância ``{}`` na ordem em que aparecem no arquivo.
- **4º** O construtor. 

**Exemplo:** ✅
```java
public class InitializationOrderSimple { // --------- Execução na classe Pai primeiro

    public InitializationOrderSimple(){ //7º
        System.out.println(name+lastName+" Constructor()"); // Por último o construtor 
    }

    private String lastName;  //5º membros de classe por ordem de aparição

    {  //6º membros de classe por ordem de aparição
        lastName = "OrderSimple";
        System.out.println(lastName);
    }

    private static String name; //1º - Primeiro os membros estáticos por ordem de aparição

    static { //2º Membros estáticos por ordem de aparição
        name = "static - Initialization";
        System.out.println(name);
    }
}
```
```java
class Initialization2OrderSimpleTwo extends InitializationOrderSimple{
    public Initialization2OrderSimpleTwo(){ //10º
        System.out.println(name+lastName+" Constructor()"); // Por último o construtor 
    }

    private String lastName; //8º membros de classe por ordem de aparição

    { //9º membros de classe por ordem de aparição
        lastName = "OrderSimpleTwo";
        System.out.println(lastName);
    }

    private static String name; //3º - Primeiro os membros estáticos por ordem de aparição (e posteriormente por ordem de herança)

    static { //4º Membros estáticos por ordem de aparição
        name = "static - Initialization2";
        System.out.println(name);
    }


}
```
```java
public class Main {
    public static void main(String[] args) {
        Initialization3OrderSimpleThree initializationOrderSimple = new Initialization3OrderSimpleThree();
        /* Output
            static - Initialization
            static - Initialization2
            OrderSimple
            InitializationOrderSimple Constructor()
            OrderSimpleTwo
            Initialization2OrderSimpleTwo Constructor()
        */
    }
}
```
Repare que primeiro foram executados os blocos e membros estáticos por ordem de herança, e posteriormente foram executados os códigos restantes, respeitando a ordem descrita na lista de regras e respeitando a sequência de herança. 

**⚠️ Exemplo com pegadinha:**
```java
public class InitOrder {

    static {
        printString("firstStatic"); // 1º (Começo pelas declarações static)
    }

    static void printString(String myString){
        System.out.println(myString + " ");
    }

    InitOrder(){ // 5º Seguindo a ordem de membros de instância, o último é o Construtor
        printString("constructor");
    }

    { // 3º Seguindo a ordem de membros de instância
        printString("firstBlock");
    }

    static { // 2º (⚠️ Aqui eu instancio minha classe, cessando a execução - por momento - dos membros static e iniciando a execução dos membros de classe! )
        new InitOrder();
    }

    static { // 6º Finalizo a execução dos membros static
        printString("thirdStatic");
    }

    { // 4º Seguindo a ordem de membros de instância
        printString("secondBlock");
    }

    public static void main(String[] args) {}

    /*Output:
      - firstStatic 
      - firstBlock 
      - secondBlock 
      - constructor 
      - thirdStatic
    */

}

```
---

### 1.2 Classes x Arquivos
Um arquivo pode ter várias classes dentro dele, com o atributo `public` ou não.
Quando não há nenhuma classe com o atributo `public`, o nome não possui uma regra a ser seguida.
**Arquivo:** `QualquerCoisa.java` ✅
```java
class Animal{
  String attrAnimal;
}

class Animal12{
  String attrAnimal12;
}
```
Todavia se dentro do arquivo, qualquer classe possuir o atributo `public`, o nome do arquivo obrigatóriamente deve ser o nome desta classe. (OBS: caso um arquivo tenha MAIS de uma classe, somente  **UMA** classe poderá ter o atributo `public`)
**Arquivo:** `Animal12.java` ✅
```java
class Animal{
  String attrAnimal;
}
public class Animal12{
  String attrAnimal12;
}
```
Exemplo que não compila:
**Arquivo:** `Animal12.java` ❌ 
```java
public class Animal{  // ❌ Não compila, se possui o atributo "public" o nome do arquivo precisa ter o nome da classe
  String attrAnimal;
}
public class Animal12{
  String attrAnimal12;
}
```
---

### 1.3 Comentários
Em classes, Atributos e Métodos são tratados como "membros"
```java
(single-line) ✅
// Comentário em uma única linha

(multi-line) ✅
/*
 * Comentário em múltiplas linhas
 */

(multi-line inválido) ❌
/*
 * /* invalido */
 */ -> Erro de compilação, porque o comentário foi fechado na linha acima.

(Javadoc) ✅
/**
 * Comentário Javadoc
 * @author Yhonathan 
 */
```
---

### 1.4 Executando código com o método main()
Para executar um código em Java, ele precisa ter em sua estrutura o que chamamos de "método main()".
**Arquivo:** `Zoologico.java`
```java
public class Zoologico {

    // O Método main() precisa ter a seguinte estrutura:
    public static void main(String[] args) { //Membro
        System.out.println(args[0]);
        System.out.println(args[1]);
        System.out.println(args[2]);
    }
}
```
Com a classe e o método devidamente construídos, a execução pode ser feita via console:
```cmd
$ javac Zoologico.java 
$ java Zoologico "O Zoológico abriu! As atrações do dia são:" Girafa Elefante
```
- O comando `javac` irá compilar o arquivo `Zoologico.java` e criar um novo arquivo chamado `Zoologico.class`, que será em bytecode e posteriormente interpretado pela JVM.
- O comando `java` irá compilar o arquivo `Zoologico.java` e criar um novo arquivo chamado `Zoologico.class`, que será em bytecode e posteriormente processado pela JVM.

Outro possível exemplo de se criar o método main(), é utilizando o que chamamos de _varargs_:
**Arquivo:** `Zoologico.java`
```java
public class Zoologico {

    // O Método main() precisa ter a seguinte estrutura:
    public static void main(String... args) { //Membro
        System.out.println(args[0]);
        System.out.println(args[1]);
        System.out.println(args[2]);
    }
}
```
E usando como base o mesmo exemplo dos comandos inseridos no console, o output deste programa seria:
```cmd
$ O Zoológico abriu! As atrações do dia são:
$ Girafa 
$ Elefante
```
---

### 1.5 Importar pacotes de Classes
Um pacote (ou _package_) é uma unidade de organização de código que congrega classes, interfaces e exceções relacionadas.
Em java, todo programa possui um import _default_ que é implícito e não há necessidade de declará-lo, o `java.lang`.
Para importarmos um pacote dentro das nossas classes java, utilizamos a palavra reservada **`import`**.
```java
import java.util.Random; //Importei a classe "Random" do pacote "java.util"

public class Sorteio {

    public static void main(String... args) {
        System.out.println(new Random().nextInt(10)); //Imprimo um número inteiro entre 0 e 9
    }
}
```

#### 1.5.1 Wildcards
Outra forma possível de fazer um _import_, é fazendo o uso do que chamamos de **_wildcards_**.
```java
import java.util.*; //Importei todas as classes definidas dentro "java.util"

public class Sorteio {

    public static void main(String... args) {
        System.out.println(new Random().nextInt(10)); //Imprimo um número inteiro entre 0 e 9
    }
}
```
Exemplos que não compilam quando feito o uso incorreto de *wildcards*:
```bash
Considerando a seguinte estrutura de pacotes:

├── java
  ├── util
    ├── concurrent
    |  ├── CompletableFuture.java
    |
    ├── Random.java
    ├── ArrayList.java
    ├── HashMap.java
```
```java
//Imagine que queiramos acessar o objeto CompletableFuture, presente em java.util.concurrent.CompletableFuture

import java.*; // ❌ Isso não existe, não é possível utilizar wildcards (no contexto de uso do CompletableFuture) para importar subpacotes. É preciso ir até o último nível de pacotes para importar as classes.
import java.*.*; // ❌ Isso não existe, não é possível utilizar dois wildcards no mesmo _import_.
//Exemplos corretos para esse cenário:
import java.util.concurrent.*; // ✅ 
import java.util.concurrent.CompletableFuture; // ✅ 

public class ImportExample {

    public static void main(String... args) {
        CompletableFuture<String> completableFuture = new CompletableFuture<>();
    }
}
```

#### 1.5.2 Conflitos de Nome
Pode acontecer de classes possuírem o mesmo nome, e isso ocasionar um conflito de nome na hora de realizar os _imports_.
```java
import java.util.*; 
import java.sql.*; 

public class Conflitos{ 
  Date data; // ❌ Err: The type Date is ambiguous
}
```
Outro exemplo:
```java
import java.util.Date; 
import java.sql.Date; 

public class Conflitos{ 
  Date data; // ❌ Err: The import java.sql.Date collides with another import statement
}
```
Esses erros ocorreram porque a Classe ``Date`` está presente nos dois pacotes, como apresentado. Para resolver esse tipo de ambiguidade, é necessário deixar explícito toda a estrutura de pacote de qual tipo você deseja utilizar. Seja na hora do _import_, ou na declaração do atributo:
```java
//Exemplo na declaração do import
import java.util.Date; 
import java.sql.*; 

public class Conflitos{ 
  Date data; // ✅
}
```
ou
```java
//Exemplo na declaração do atributo (note que nesse caso, não é necessário fazer o import).
public class Conflitos{ 
  java.util.Date utilDate; // ✅
  java.sql.Date sqlDate; // ✅
}
```

#### 1.5.3 Criando e Compilando novos Pacotes
E quando queremos organizar nosso código os separando por pacotes? Para isso, criamos as pastas e adicionamos as nossas classes dentro dela.
Exemplo 01: `C:\temp\pacotea\ClasseA.java`
Em Java, para dizer que nossa "ClasseA" está dentro do "pacotea", fazemos o uso da palavra reservada `package`
```java
package pacotea;  

public class ClasseA{ 
}
```
Exemplo 02: `C:\temp\pacoteb\ClasseB.java`
Nesse exemplo, criamos uma ClasseB presente em um diferente pacote e faremos o _import_ da ClasseA para o nosso escopo de código
```java
package pacoteb;  
import pacotea.ClasseA;

public class ClasseB{
  public static void main(String[] args){
    ClasseA classA = new ClasseA();
    System.out.println("Funcionou!");
  } 
}
```
Agora, considerando que temos a seguinte estrutura de arquivos:
```bash
├── temp
  ├── pacotea
  | ├── ClasseA.java
  |
  ├── pacoteb
  | ├── ClasseB.java
```
Como fazemos para compilar e executar nosso código?
Da seguinte forma:
```cmd
$ cd C:\temp
$ javac pacotea/ClasseA.java pacoteb/ClasseB.java
$ java pacoteb.ClasseB
```
⚠️ **IMPORTANTE:** Repare que para compilar, usando `javac`, o acesso à classe foi feito utilizando " / " (barra). Enquanto na execução, usando o comando `java`, o acesso foi feito por " . " (ponto).
⚠️ **IMPORTANTE:** Se as classes estão no mesmo pacote, não há necessidade de declarar o `import`.

---

### 1.6 Objetos
Um objeto é a representação (instância) de uma classe. A instância ocorre por meio do uso da palavra reservada `new`. 

```java
Object myObject = new Object(); //myObject se tornou uma instância de Object
```

---

### 1.7 Tipos primitivos
O cálculo para saber os valores possíveis que estão dentro de cada tipo, é 2 elevado ao número de bits, dividindo por 2.
Exemplo com o byte, ele tem 8 bits, portanto 2⁸ = 256. Dividindo 256 pela metade em valores negativos e positivos, obtemos -128 até 127 (Incluso o 0).

|  Tipo   | Tamanho |                                Range de valores                                |     Exemplo      |
|:-------:|:-------:|:------------------------------------------------------------------------------:|:----------------:|
| boolean |  1 bit  |                                  true, false                                   |       true       |
|  byte   | 8 bits  |                                  -128 até 127                                  |        10        |
|  short  | 16 bits |                               -32,768 até 32,767                               |      10000       |
|  char   | 16 bits |             characters representation of ASCII values (0 até 255)              |       'a'        |
|   int   | 32 bits |                        -2,147,483,648 até 2,147,483,647                        |     1000000      |
|  long   | 64 bits | -9,223,372,036,854,775,808 até 9,223,372,036,854,775,807 com terminação em "L" |   100L ou 100l   |
|  float  | 32 bits |                  até 7 dígitos decimais com terminação em "F"                  | 10.30F ou 10.30f |
| double  | 64 bits |                 até 16 dígitos decimais com terminação em "D"                  | 24.25D ou 24.25d |

#### 1.7.1 Uso do underline ( _ ) para melhorar a leitura de números
A partir do Java 7, foi adicionado o suporte ao o uso do uderline ( _ ) na atribuição de variáveis que armazenam tipos númericos.
**Exemplo:** ✅
```java
  { 
    int million = 1_000_000;
    int million2 = 1_000___000;
  }
```
Mas tenha cuidado, pois há formas incorretas de fazer a utilização do underline.
**Exemplo:** ❌ 
```java
  { 
    //Exemplos que NÃO COMPILAM
    int wrongMillion = _1000000; //❌ Não pode começar com _
    int wrongMillion2 = 1000000_; //❌ Não pode terminar com _
    float wrongMillion3 = 1000000_.00; //❌ Não pode ter _ antes do .
    float wrongMillion4 = 1000000._00; //❌ Não pode ter _ depois do .
  }
```
#### 1.7.2 Ecrevendo números Binários | Octal | HexaDecimal
Há também a possibilidade de escrever números usando diferentes bases.
**Exemplo (Binário):** ✅
Quando faço uma atribulção começando por `0b`, isso significa que irei trabalhar com números binários. Portanto, o que vier após isso precisa ser apenas valores ``0`` e ``1``
```java
  { 
    System.out.println(0b10); // ✅ Output: 2
    System.out.println(0b101); // ✅ Output: 5
    System.out.println(0b1014); // ❌ Err: integer number too large (inseri outros dígitos além de 0 e 1)
  }
```
**Exemplo (Octal):** ✅
Octal é um sistema numérico de base 8, então significa que o dígito pode ser de ``0`` a ``7``, você não pode usar o dígito 8 (e 9 também) no sistema numérico octal. Para isso, realizamos a atribuição começando pelo número ``0``.
```java
  { 
    System.out.println(07); // ✅ Output: 7
    System.out.println(010); // ✅ Output: 8
    System.out.println(011); // ✅ Output: 9
    System.out.println(08); // ❌ Err: integer number too large (passou do range permitido)
    System.out.println(018); // ❌ Err: integer number too large (passou do range permitido)
  }
```
**Exemplo (HexaDecimal):** ✅
A declaração hexaDecimal começa com ``0X`` ou ``0x`` e os caracteres possíveis são ``[0-9A-Fa-f]``
```java
  { 
    System.out.println(0xf); // Output: 15
    System.out.println(0x10); // Output: 16
    System.out.println(0xg); // ❌ Err: Hexadecimal numbers must contain at least one hexadecimal digit
    System.out.println(0x10j); // ❌ Err: Hexadecimal numbers must contain at least one hexadecimal digit
  }
```

---

### 1.8 Variáveis
#### 1.8.1 Declarando Múltiplas Variáveis
Em Java, é possível declarar múltiplas variáveis.
**Exemplo:** ✅
```java
  { 
    int x, y; // ✅ Separando variáveis do mesmo tipo por vírgula ( , ) 

    int z = 1, w = 2; // ✅ Separando variáveis do mesmo tipo por vírgula ( , ) e atribuindo valores individuais a cada uma.

    //Quantas foram inicializadas
    int a, b, c = 1; // ✅ Foi atribuído valor apenas para a variável " c ".  

    int d; int e; // ✅ Houve o ; no final de cada setença, portanto, é permitido repetir o tipo "int".
  }
```
Mas tenha muito cuidado e atenção ao declarar variáveis do tipo, pois alguns erros podem surgir.
**Exemplo:** ❌
```java
  { 
    int x, String y; // ❌ Para que as variáveis possam ser separadas por vírgula ( , ) elas precisam ter o mesmo tipo. 
    
    int z = 1, int w = 2; // ❌ Houve uma repetição no tipo da variável após a vírgula. 

    int d; e; // ❌ Houve o ; no final da primeira setença, porém, não foi explícito qual o tipo da segunda variável " e ", ocasionando um erro de compilação.
  }
```

#### 1.8.2 Identificadores
Ao nomear suas variáveis, é preciso se atentar as seguintes regras:
- O nome deve iniciar com ``LETRA (a-z)`` ou ``$`` ou ``_``
- Nos caracteres seguintes também podem haver ``NÚMEROS``
- Palavras reservadas não podem ser usadas (lembre-se: Java é case-sensitive)
**Exemplo:** ✅
```java
  { 
    //Os seguintes exemplos atendem a regra de nomenclatura
    String identificadorOk;
    String $identificadorOk2;
    String _identificadorOk3;
    String __$$identificadorEstranhoMasOk4;
  }
```
Fique sempre atento aos possíveis erros comuns ao nomear suas variáveis.
**Exemplo:** ❌
```java
  { 
    //Os seguintes exemplos atendem a regra de nomenclatura
    String 3MyString; // ❌ Err: Iniciou com número.
    String MyString@gmail; // ❌ Err: tem caracterer especial. @ não está em {Dígitos numéricos, Alfabeto(a-z), $ e _ }
    String *$MyString; // ❌ Err: tem caracterer especial. * não está em {Dígitos numéricos, Alfabeto(a-z), $ e _ }
    String void; // ❌ Err: "void" é uma palavra reservada.
  }
```

#### 1.8.3 Escopo de variáveis
As variáveis sempre estão atreladas a um escopo. Seja um escopo de instância, escopo de classe ou simples escopos locais.
- Variáveis de Classe: da declaração até o fim do programa.
- Variáveis de Instância: da declaração até o objeto ser coletado.
- Variáveis Locais: escopo desde a declaração até o fim do bloco.
```java
public class VariableScope {

    static String classScope; //Variável de escopo de classe. (Ela não depende de instância).
    
    Boolean isIstanceScope = true; //Escopo de instância.
    
    public void myMethod(String paramScope) { //É uma variável que apesar de ser um parâmetro, está dentro do escopo local do método.
        String myVarMethodScope; //Escopo local
    }
}
```
---

### 1.9 Garbage Collector
_Garbage Collector_ (GC) é uma forma de realizar o gerenciamento automático de memória. Podemos dizer, que é um processo capaz de localizar objetos utilizados (ou não) na memória heap, remover os objetos não utilizados e compactá-los após a liberação de espaço. Muitas implementações de um Garbage Collector, possuem as seguintes operações básicas descritas a seguir:
- Alocação de memória do objeto;
- Buscar objetos em uso (Mark);
- Liberar objetos que não estão em uso (Sweep);
- Prevenção de fragmentação (Compact).

Sobre seu uso no Java 8, existe um método que pode ser usado de forma programática:
```java
System.gc()
```
Porém o seu uso no código, não reflete em uma garantia na execução do GC. ⚠️ **É uma singela sugestão.** ⚠️

**E quando os Objetos estão elegíveis para serem excluídos da memória?**
- Quando o  Objeto não possui mais nenhuma referência apontando para ele.
- Todas as referências ao objeto estão fora do escopo de execução.

**Método ``finalize()`` - Você precisa saber:**
Quando o Garbage Collector é executado, ele chama um método na classe chamado `finalize()`
```java
public class MyObject(){
  protected void finalize(){
    System.out.println("método finalize chamado!";
  }
}
```
Atenção aos detalhes sobre o método ``finalize()``:
- Ele é declarado originalmente na classe Object e, portanto, herdado por todas as classes Java (como ``toString()``, ``equals()`` e ``hashCode()``);
- Ele é chamado quando o GC tenta coletar o objeto;
- Ele **PODE** ser chamado ou não;
- Ele nunca é chamado mais de uma vez.

O contrato – recomendações da linguagem Java – de ``finalize()`` informa que ele só deve ser executado se e quando a JVM determinar que não existe mais qualquer meio pelo qual o objeto seja referenciado por qualquer thread.

---

## 2 Operadores e Instruções do Java
---

### 2.1 Entendendo os operadores
Operador em java é um símbolo que é usado para executar operações. Eles são separados pelas seguintes categorias:
- Operador Unário,
- Operador aritmético,
- Operador shift,
- Operador relacional,
- Operador bit a bit (bitwise),
- Operador lógico,
- Operador ternário e
- Operador de atribuição.
  
A seguir, haverá uma **tabela com a ordem de precedência (de cima pra baixo)** detalhando os operadores existentes no Java. 
OBS: Operadores em **negrito** são obrigatórios e cobrados na certificação Java 8.

|              Operador               |                                                          Símbolo                                                          |
|:-----------------------------------:|:-------------------------------------------------------------------------------------------------------------------------:|
|        Post-unary operators         |                                                     **x++**, **x--**                                                      |
|         Pre-unary operators         |                                                     **++x**, **--x**                                                      |
|        Other unary operators        |                                                  **+**, **-**, ~, **!**                                                   |
| Multiplication / Division / Modulus |                                                   **\***, **/**, **%**                                                    |
|       Addition / Subtraction        |                                                       **+**, **-**                                                        |
|           Shift operators           |                                                        <<, >>, >>>                                                        |
|        Relational operators         |                                         **<**, **>**, **<=**, **>=**, instanceof                                          |
|          Equal / Not equal          |                                                      **==**, **!=**                                                       |
|          Logical operators          |                                                   **&**, **^**, **\|**                                                    |
|   Short-circuit Logical Operators   |                                                     **&&**, **\|\|**                                                      |
|          Ternary operators          | boolean **e ? e1 : e2**  (_if(e){e1}else{e2}_) <br/> ou boolean **x ? x1 : y ? y2 : z** (_if(x){x1}elseIf(y){y1}else{z}_) |
|        Assignment operators         |                               **=**, **+=**, **-=**, *=, /=, %=, &=, ^=, !=, <<=, >>=, >>>=                               |

#### ⚠️ 2.1.1 Diferença entre operadores lógicos e operadores lógicos de curto circuito (short-circuit)
Os operadores ``&&``e ``||`` "cortam o circuito", o que significa que não avaliam o lado direito se não for necessário.

Os operadores ``&`` e ``|``, quando usados como operadores lógicos, sempre avaliam ambos os lados.

Existe apenas um caso de curto-circuito para cada operador, e são eles:
- ``false && ...`` - Não é necessário saber qual é o lado direito porque o resultado só pode ser ``false`` independente do valor ali.
- ``true || ...`` - Não é necessário saber qual é o lado direito porque o resultado só pode ser ``true`` independente do valor ali.

Em suma, um curto-circuito acontece porque o resultado fica claro antes mesmo da avaliação completa da expressão, e o resultado é retornado. A avaliação de curto-circuito evita trabalho desnecessário e leva a um processamento eficiente.

#### ⚠️ 2.1.2 Diferença entre operadores lógicos e operadores lógicos de curto circuito (short-circuit)
Os operadores de incremento e decremento são operadores unários usados para adicionar ou subtrair 1 do valor de uma variável numérica. O operador de incremento é simbolizado por **++**, e o de decremento, por **--**
Exemplo:
```java
public class NumericalIncrementDecrement(){

  private void exampleCases(){
    int counter = 0;
    System.out.println(counter);   // 0
    System.out.println(++counter); // 1 (Pre-unary operators - Realizam a alteração no valor antes dele ser processado)
    System.out.println(counter);   // 1
    System.out.println(counter--); // 1 (Post-unary operators - Realizam a alteração no valor após ele ser processado)
    System.out.println(counter);   // 0
  }

    private void calculationExample(){
    int x = 3;
    int y = ++x * 5 / x-- + --x;
          //  4 * 5 / 4   +   2
    System.out.println(x); //2   
    System.out.println(y); //7
  }
}
```
#### ⚠️ 2.1.3 Overflow e Underflow
Simplificando, overflow e underflow acontecem quando atribuímos um valor que está fora do intervalo do tipo de dados declarado da variável.

Se o valor (absoluto) for muito grande, chamamos de overflow; se o valor for muito pequeno, chamamos de underflow. 
**Exemplo Overflow**:
```java
//Para underflow, você precisa deixar explícito o cast de valores primitivos
long t = 192301398193810323; // ❌ Não compila, isso porque ele é interpretado como um número inteiro muito longo. É preciso deixar explícito que o valor é um long e não um inteiro. Para isso, o overflow é realizado a partir do uso da letra "l ou L". 
long t = 192301398193810323L; // ✅ Compila corretamente
//E se o número fosse menor, como "30"? Nesse caso, a atribuição seria feita sem nenhum problema, sem precisar adicionar a letra "L" ao final!
long lg = 30; //✅ compila, o casting é feito automaticamente.
```
**Exemplo Underflow**:
```java
//Para underflow, você precisa deixar explícito o cast de valores primitivos
int u = (int) 1.0;
int z = (int) 9f;
short y = (short) 1921222; //O valor atribuido será 20678
// Isso ocorre porque o número "1921222" na escala de bits, vale: 111010101000011000110
// e o short suporta até 16bits, pegando somente os primeiros 16 bits: 0101000011000110
// Se o número fosse negativo, haveria uma inversão nos valores:       1010111100111010
```
**Exemplo de casting com Compound assignment operator**:
```java
//Outro exemplo interessante, é com o uso de Compount assignment operator, onde este realiza o casting automaticamente.
long x = 10;
int j = 5;
//Alguns exemplos:
j = j * x; // (Simple assignment operator) ❌ Não compila! Isso porque a operação resulta em um long, e o casting deveria ser explícito.
j = j * (int) x; // ✅ (Casting) Dessa forma, o programa executaria normalmente.
j *= x; // (Compound assignment operator) ✅ Compila, pois dessa forma o casting é feito automaticamente.
```

#### ⚠️ 2.1.4 Diferença entre operadores de igualdade .equals() e ==
Em geral, os operadores equals() e “==” em Java são usados ​​para comparar objetos para verificar a igualdade, mas aqui estão algumas das diferenças entre os dois: 
1. **== (Operador de Igualdade)**:
   - O operador ``==`` em Java é usado para comparar referências de objetos.
   - Quando você usa ``==`` para comparar objetos, ele verifica se as duas referências apontam para o mesmo objeto na memória.
   - Se as referências apontarem para o mesmo objeto, a expressão será avaliada como ``true``; caso contrário, será avaliada como ``false``.
   - Este operador não compara os conteúdos dos objetos, apenas verifica se as referências apontam para o mesmo local na memória.

2. **.equals() (Método de Comparação de Conteúdo)**:
   - O método ``.equals()`` é usado para comparar o conteúdo de dois objetos.
   - A implementação padrão do método ``equals`` na classe ``Object`` compara as referências dos objetos, mas muitas classes em Java sobrescrevem esse método para comparar o conteúdo dos objetos.
   - Por exemplo, a classe ``String`` sobrescreve o método ``equals`` para comparar o conteúdo das strings.

Exemplo:
```java
// Você pode ter 2 strings, por exemplo, com o mesmo conteúdo, mas sendo objetos diferentes:
String a = new String(“Teste”);
String b = new String(“Teste”);
String c = a; // apontam para o mesmo endereço
a == b; // false - (verifica endereço de memória)
a == c; // true - (verifica endereço de memória)
a.equals(b); // true - (verifica conteúdo)

// -------------------------------------------

// O `==` só testa conteúdo no caso dos tipos primitivos.
//Exemplo:
int x = 5;
int y = 5;
x == b; //true
```
---


### 2.2 Promoção Numérica
Um detalhe muito importante que temos que estar atentos para a prova, são as promoções númericas. Isso é, os tipos de dados são promovidos antes que haja a execução de uma operação. Para isso, vamos nos atentar as regras:
  1. Se dois valores têm diferentes tipos de dados, o Java irá automaticamente promover o valor com o tipo menor para o maior.Isso é, se um dos valores é **inteiro** o outro é **ponto flutuante**, O Java irá automaticamente promover o valor **inteiro** para **ponto flutuante**.
  2. Tipos de dados menores, nomeadamente **byte**, **short** e **char**, são primeiro promovidos para **int** sempre que são usados com algum operador aritmético binário, mesmo que nenhum dos operandos seja um **int**. Após todas as promoções efetuadas e todos os operandos com o mesmo tipo de dados, o valor resultante terá o mesmo tipo de dados dos operandos promovidos.

Exemplo:
```java
public class NumericalPromotion(){

  private void promotionCases(){

      //Exemplo inteiro para ponto flutuante
      int i = 10; // i é um inteiro
      double d1 = 2.5; // d1 é um ponto flutuante
      double result = d1 * i; // i é "promovido" para ponto flutuante, para que seja realizado o cálculo.
      // ---------------------------------------------

      //Exemplo inteiro para long
      int x = 1; // x é um inteiro
      long y = 33; // y é um long
      long result1 = x * y; // x é "promovido" para ponto long, para que seja realizado o cálculo.
      // ---------------------------------------------

      //Exemplo float para double
      double j = 39.21; // j é um double
      float k = 2.5; // k é um float
      double result2 = j * k; // k é "promovido" para double, para que seja realizado o cálculo.
      // ---------------------------------------------

      //Exemplo short para int
      short s1 = 10; // j é um double
      short s2 = 3; // k é um float
      int result3 = s1 / s2; // s1 e s2 são "promovidos" para int, para que seja realizado o cálculo.
      // ---------------------------------------------

       //Exemplo short -> int, int -> float e float -> double
      short ss1 = 14; // ss1 é um short
      float f1 = 13; // f1 é um float
      double dd1 = 30; // dd1 é um double
      double result4 = ss1 * f1 / dd1; // ss1 é "promovido" para int, para que seja realizado o cálculo. Agora como int, ss1 será "promovido" para float para realizar o cálculo, e posteriormente esses valores serão "promovidos" para double respeitando o cálculo".
      // ---------------------------------------------

      //⚠️ OBS: Note que nesse último exemplo, na atribuição do float eu não usei "13f" e no double não utilizei "30.0".
      // Isso ocorre porque o Java faz automaticamente a atribuição correta para tipos primitivos. Ou se o float fosse quebrado (13.1) ai também precisaria deixar explícito o uso de "f" no final, senão seria tratado como double.
      // Se isso fosse feito com tipos de referência (Float e Double) a atribuição deveria ser, 
      // respectivamente: "13f" e "30.0"
  }

}
```
---

### 2.3 Comando Switch
O "Switch case" é uma estrutura de decisão que facilita a compreensão do código ao diminuir a quantidade de blocos de if/else encadeados, visando descomplicar a manutenabilidade.
Sua construção é feita da seguinte forma:
```java
switch(variableToTest){
  case constantExpression:
    // ......
    break;
  case constantExpression2:
    // ......
    break;
  default:
    // ......
    break;
}
```
**⚠️ Pontos de atenção:**
- **Um controle de decisão switch deve ter 0 ou mais `cases`**;
- Um `case` **deve obrigatoriamente ser uma constante, do mesmo tipo** de `variableToTest`
- códigos `break` são opcionais;
- código `default` é opcional e pode aparecer em qualquer lugar da instrução switch

**⚠️ Tipos suportados:**
Os tipos suportados como `variableToTest` são unicamente: 
- ``byte`` and ``Byte``    
- ``short`` and ``Short``   
- ``char`` and ``Character`` 
- ``int`` and ``Integer``   
- ``String``       
- Enum values     

Exemplos de erros comuns:
```java
public class ConditionalTest(){

  private void switchCasesTests(String var1, final String var2){
    String name = "Ana";
    final String STATE = "SP";
    int id = 0;

    switch(var1){ // ✅ Compila, é uma variável e é do tipo String
      case "Teste": // ✅ Compila, é uma constante e é do mesmo tipo da variável de decisão (var1 - String)
        // ......
        break;
      case name: // ❌ Não compila. Apesar de ter o mesmo tipo, o valor não é uma constante. Precisaria estar marcada como `final` ou ser "hardCoded".
        // ......
        break;
      case STATE: // ✅ Compila, é uma variável marcada como final (constante) e é do mesmo tipo (String)
        // ......
        break;
      case var2: // ❌ Não compila. Apesar de ter o mesmo tipo, e estar marcada como `final` no parâmetro do método, o valor não é uma constante (definida) e sim, é um valor indefinido (não sabemos o que virá). 
        // ......
        break;
      case 5: // ❌ Não compila. Apesar de ser constante, não é do mesmo tipo (é `int` e deveria ser `String`).
        // ......
        break;
      case 'J': // ❌ Não compila. Apesar de ser constante, não é do mesmo tipo (é `char` e deveria ser `String`).
        // ......
        break;
      case java.time.DayOfWeek.SUNDAY: // ❌ Não compila. Apesar de ser constante, não é do mesmo tipo (é um Enum e deveria ser `String`).
        // ......
        break;
      default:
        // ......
        break;
    }
  }
}
```
---

### 2.4 Controle de Fluxo Avançado
O Controle de fluxo avançado é feito com o uso de labels opcionais, que atuam como "rótulos" sobre os tipos `if`, `while`, `do while`, `for`, `switch` e são referenciados através dos comandos `break` e `continue`. 

|    Tipo    |            Labels opcionais            |               Break               |             Continue             |
|:----------:|:--------------------------------------:|:---------------------------------:|:--------------------------------:|
|    `if`    |    <font color="green"> Sim </font>    |  <font color="red"> Não </font>   |  <font color="red"> Não </font>  |
|  `while`   |    <font color="green"> Sim </font>    | <font color="green"> Sim </font>  | <font color="green"> Sim </font> |
| `do while` |    <font color="green"> Sim </font>    | <font color="green"> Sim </font>  | <font color="green"> Sim </font> |
|   `for`    |    <font color="green"> Sim </font>    | <font color="green"> Sim </font>  | <font color="green"> Sim </font> |
|  `switch`  |    <font color="green"> Sim </font>    | <font color="green"> Sim </font>  |  <font color="red"> Não </font>  |

#### 2.4.1 Incluindo de Labels opcionais
```java
public class OptionalLabel(){

  //Exemplo de soma que desconsidera números pares da matriz. 
  private void exampleCases(){
    int oddSum = 0;
    int totalSum = 0;
    int[][] myComplexArray = {{5, 2, 1, 3}, {3, 9, 8, 9}, {5, 3, 12, 7}};
    OUTER_LOOP: for(int[] mySimpleArray : myComplexArray){ //Label opcional OUTER_LOOP para o loop externo
      INNER_LOOP: for(int i = 0; i < mySimpleArray.length; i++){ //Label opcional INNER_LOOP para o loop interno    
        totalSum += mySimpleArray[i]; //Soma na variável total
        if(mySimpleArray[i] % 2 == 0){
          break OUTER_LOOP; //Se for um número par, quebre o loop externo e pare a iteração.
        }else{
          oddSum += mySimpleArray[i]; //Se for impar, some na variavel de números ímpares
          continue INNER_LOOP; //Continue a iteração do loop interno
        }
          System.out.println("Atual value: " + mySimpleArray[i]); // ❌ Essa linha resulta em um erro de compilação: "unreachable statement".
          // Isso ocorre porque não é possível ter código após o "continue INNER_LOOP", já que ele dará sequência de onde parou.
          // Esse é apenas um exemplo de como funciona o uso do break e continue com labels, geralmente um continue redirecionaria para um loop externo, para não ser redundante. 
      }
    }
    if(oddSum != totalSum){ //Se a soma de números ímpares for diferente da soma total, estoure uma exception (Significa que foram inseridos números pares)
      throw new BadRequestException("Even numbers are not allowed!");
    }
  }

}
```
---

## 3 Core Java API
---

### 3.1 Classe String
``String`` em Java é um objeto (_wrapper-class_) comumente utilizado para armazenar uma sequencia de caracteres.
Algumas características:
- Uma String é um objeto imutável que não pode ser modificado após sua criação.
- Em Java, todas as strings são imutaveis
   - Exemplo:
      ```java
        String s1 = "1"; 
        String s2 = s1.concat("2"); //Foi atribuido um novo valor a s2 com base no valor de s1, todavia o valor de s1 se mantém intacto. Ele não foi alterado.
        s2.concat("3"); //O valor de s2 não foi alterado, e o resultado desta operação não foi atribuido a nenhuma variável.
        System.out.println(s2); // Resultado: 12
      ``` 
- Se um dos operantes anteriores a uma string for um '+' ocorrerá uma concatenação, avaliada da esquerda para a direita.
    - Exemplo:
      ```java
        String s1 = "1"; 
        String s2 = "2"; 
        System.out.println(5 + 5 + s1+s2); // Resultado: 1012
      ``` 
#### 3.1.1 Pool de Strings
Graças à imutabilidade das Strings em Java, a JVM pode otimizar a quantidade de memória alocada para elas armazenando apenas uma cópia de cada String literal no pool . Esse processo é chamado de _interning_ (internamento). Quando criamos uma variável String e atribuímos um valor a ela, a JVM procura no pool uma String de igual valor. **Se encontrado, o compilador Java simplesmente retornará uma referência ao seu endereço de memória, sem alocar memória adicional.** Caso não seja encontrado, será adicionado ao pool (internado) e sua referência será retornada.

#### 3.1.2 String Literal vs Objeto String
Quando criamos um  objeto String usando o  operador ``new()`` , ele sempre cria um novo objeto na memória *heap*. Por outro lado, se criarmos um objeto usando a sintaxe literal de String, por exemplo, ``"myString"``, ele poderá retornar um objeto existente do pool de String, se já existir. Caso contrário, ele criará um novo objeto String e o colocará no conjunto de strings para reutilização futura.

Em um nível superior, ambos são objetos String , mas a principal diferença vem do ponto em que o operador ``new()`` sempre cria um novo objeto String, enquanto a String literal  quando declarada duas ou mais Strings iguais em diferentes etapas de um programa, a mesma referência será utilizada, ou seja, é feito um reaproveitamento de uma String internada no **Pool de Strings**.
Exemplo:
```java
  String first = "myLiteralString"; 
  String second = "myLiteralString"; 
  System.out.println(first == second); // True

  String third = new String("myInstantiatedString");
  String fourth = new String("myInstantiatedString"); 
  System.out.println(third == fourth); // False
``` 
---

### 3.2 StringBuilder
``StringBuilder`` em Java representa uma **sequência mutável de caracteres**, diferente da classe String que cria uma sequência imutável de caracteres.

**Características StringBuilder:**
- Armazena caracteres especificados pela sua capacidade, caso ela seja excedida, é aumentada para acomodar os caracteres adicionais;
- Não precisa alocar novos objetos quando realiza uma concatenação;
- Não são sincronizadas;
- Não são seguras para threads;

Uma vantagem sobre a classe String é a concatenação de strings. Pois quando concatena strings com StringBuilder é invocado o método ``append()``. Esse método **aloca novas strings concatenadas para o mesmo objeto**, ou seja, cada vez que concatena strings **não são criadas cópias dos objetos como é realizado pelo método ``concat()`` da classe String**, contribuindo para um melhor desempenho do sistema.

Exemplo:
```java
  String myFirstString = "myFirstString"; 
  String mySecondString = myFirstString.concat("+mySecondString"); 
  System.out.println(myFirstString); // myFirstString
  System.out.println(mySecondString); // myFirstString+mySecondString


  String myFirstStringBuilder = new StringBuilder("myFirstStringBuilder");
  String mySecondStringBuilder = myFirstStringBuilder.append("+myFirstStringBuilder"); //Alterei o valor de myFirstStringBuilder. Ambas as variáveis apontam para o mesmo objeto.
  System.out.println(myFirstStringBuilder); // myFirstStringBuilder+myFirstStringBuilder
  System.out.println(mySecondStringBuilder);// myFirstStringBuilder+myFirstStringBuilder
``` 
**⚠️ Métodos exlusivos de StringBuilder:**
(...) - Representa que aceita qualquer tipo de valor (boolean, String, int...)
- ``append(...)``: Adiciona a representação de qualquer tipo de dados ao final do StringBuilder. Isso é, realiza uma concatenação do valor fornecido ao valor atual.
- ``insert(int offset, ...)``: Insere a representação de qualquer tipo de dados em uma posição específica no StringBuilder.
- ``delete(int start, int end)``: Remove os caracteres entre as posições start e end (exclusive) do StringBuilder.
- ``deleteCharAt(int index)``: Remove o caractere na posição index do StringBuilder.
- ``replace(int start, int end, String str)``: Substitui os caracteres entre as posições start e end (exclusive) pelo conteúdo da string str.
- ``reverse()``: Inverte a sequência de caracteres no StringBuilder.

#### 3.2.1 StringBuffer
``StringBuffer`` em Java é uma classe que faz parte do pacote ``java.lang`` e é usada para criar strings mutáveis. Assim como ``StringBuilder``, ``StringBuffer`` fornece métodos para manipulação eficiente de strings (possui os mesmos métodos), mas com a diferença fundamental de ser *thread-safe*. Isso significa que os métodos da classe ``StringBuffer`` **são sincronizados e podem ser usados com segurança em ambientes onde várias threads podem acessar e modificar a mesma instância de StringBuffer simultaneamente, não havendo problema de concorrência e leitura suja**.

#### 3.2.2 String vs StringBuilder vs StringBuffer 
Há algumas diferenças entre essas três classes, são elas:
- String é imutável, enquanto StringBuffer e StringBuilder são classes mutáveis.
- StringBuffer é thread-safe e sincronizado, enquanto StringBuilder não. É por isso que StringBuilder é mais rápido que StringBuffer.
- O operador de concatenação de strings (+) usa internamente a classe StringBuffer ou StringBuilder.
- Para manipulações de String em um ambiente não multithread, devemos usar StringBuilder, caso contrário, usar a classe StringBuffer.

---
### 3.3 Array
*Arrays* são usados para armazenar vários valores em uma única variável, em vez de declarar variáveis ​​separadas para cada valor.

#### 3.3.1 Formas de declarar um Array:
```java
 //Declaração múltipla:
  int[] myArr, myArr2; //Quando o colchete está logo após o tipo, TODAS as minhas variáveis se tornam arrays;
  int myArr3[], myInt; //Se o colchete vem após o nome da varíavel, somente a variável com colchete será um array, as demais serão do mesmo tipo declarado.
  []int myWrongArr, myWrongArr2; // ❌ Não compila! Err: unexpected token.

 //-------------------------
 
 //Declaração única
  int[] myArray; // ✅ Os colchetes podem vir logo após o tipo da varíavel
  int [] myArray2; // ✅ Os colchetes podem vir entre o tipo da varíavel e o nome
  int myArray3[]; // ✅ Os colchetes podem vir logo após o nome da varíavel
  int myArray4 []; // ✅ Os colchetes podem vir com espaços  após o nome da varíavel
  []int myWrongArray5; // ❌ Não compila! Err: unexpected token.
``` 

#### 3.3.2 Formas de declarar um Array com *VarArgs*:
Em Java, o *VarArgs* permite que um método aceite um número indefinido de parâmetros de determinado tipo, tratando-o como um array qualquer. Sua declaração é feita dentro do parâmetro de um método, e os três pontos `...` precisam obrigatoriamente estar entre o tipo da variável e o nome dela, independente de espaços, da seguinte forma `method(Type... variableName)`
Exemplo:
```java 
  public static void main(String... args){}  // ✅ É permitido, está entre o tipo e a variável.
  public static void main(String ... args){} // ✅ É permitido, está entre o tipo e a variável.
  public static void main(String ...args){}  // ✅ É permitido, está entre o tipo e a variável.
  public static void main(...String args){}  // ❌ Não compila! Está antes do tipo.
  public static void main(String args...){}  // ❌ Não compila! Está depois da variável.
``` 
**⚠️Pontos de atenção adicionais sobre o VarArgs**:
- *VarArgs* deve OBRIGATORIAMENTE ser o ÚLTIMO parâmetro de um método.
   ```java 
  public static void main(String... args){}  // ✅ É o único parâmetro do método.
  public static void main(String firstArg, String ...args){}  // ✅ É o último parâmetro do método. 
  public static void main(String ...args, String secondArg){}  // ❌ Não compila! Err: Vararg parameter must be the last in the list
  public static void main(String firstArg, String ...args, String thirdArg){}  // ❌ Não compila! Err: Vararg parameter must be the last in the list
  ``` 
- Não pode haver mais de um *VarArgs*
  ```java 
  public static void main(String... args){}  // ✅ É o único parâmetro do método.
  public static void main(String ...args, String ...args2){}  // ❌ Não compila! Err: Vararg parameter must be the last in the list
  ``` 
**⚠️Forma de invocar um método construído com VarArgs**:
  ```java 
  public int returnArgsSize(String... args){
    return args.length;
  }
  
  //Chamada do método
  returnArgsSize("String1", "String2", "String3"); // ✅ Returns 3
  returnArgsSize(new String[2]); // ✅ Returns 2
  returnArgsSize({"String1", "String2"}); // ❌ Erro de compilação! Array initializer is not allowed here. VarArgs aceita um array como parâmetro de forma automática, porém inicializar um array no parâmetro não é permitido!
  ``` 
#### 3.3.3 Formas de inicializar um Array:
```java
  int[] numbers = new int[3]; // ✅ Esse valor atribuido ao tamanho do array é um valor estático e imutável. A partir desse momento, ele sempre terá capacidade 3.
  // Tamanho -> [0][1][2], todos com valor default 0 até que sejam inicializados.
  int[] numbers2 = new int[] { 10, 20, 30 }; // ✅ Uma forma alternativa de declarar um array com valores iniciais e fixos.
  int[] numbers3 = { 10, 20, 30 } // ✅ Uma forma alternativa de declarar um array com valores iniciais e fixos.
  int[] numbers4 = new int[3] { 10, 20, 30 }; // ❌ Não compila
  int[] numbers5 = new int[]; // ❌ Não compila! Err: array dimension missing. É preciso passar um valor inicial para o array.

  //Inicializando um array com base em uma lista ----------------------------------------------------------------------------
  List<Integer> myNumberList = new ArrayList<>();
  myNumberList.add(10);
  myNumberList.add(20);
  myNumberList.add(30);

  Obejct[] myNumberArrayAsObject = myNumberList.toArray(); // ✅ O método toArray() retorna um Object[];
  Integer[] myNumberArray = myNumberList.toArray(new Integer[0]); // ✅ Passo como parâmetro 'new <tipo>[0]' se eu quiser obter uma cópia exata do que tem na minha lista de tipo específico, pois somente o método toArray() retorna o tipo Object.
``` 
#### 3.3.4 Propriedade Length:
As vezes recebemos uma array como argumento e precisamos saber o tamanho, para isso usamos array.length.
Exemplo:
```java
  int[] numbers = {10, 20, 30, 40, 50};
  System.out.println(numbers.length); //Output: 5
  
  //Para acessar o último elemento de um array
  System.out.println(numbers[numbers.length-1]); //Output: 50
``` 
#### 3.3.5 Ordenação de Arrays:
As vezes recebemos uma array como argumento e precisamos saber o tamanho, para isso usamos array.length.
Exemplo:
```java
  import java.util.Arrays;
  
  //Ordenação de números inteiros
  int[] myArray = {10, 1, 7, 5};
  Arrays.sort(myArray); //Esse método faz a ordenação e altera o valor presente dentro da variável 'myArray', não sendo necessário reatribuí-lo.
  for(int arr: myArray) System.out.print(arr + " "); //myArray = 1 5 7 10
  
  //------------------------------
  
  //Ordenação de Strings
  String[] myStringArray = {"z", "1", "a", "9"};
  Arrays.sort(myStringArray); //Em Strings, a ordenação ocorre em ordem alfabética, começando por números e indo em para letras; 
  for (String arr: myStringArray) System.out.print(arr + " "); // myStringArray = 1 9 a z
``` 
#### 3.3.6 Busca em Arrays com binarySearch():
A busca binária é uma busca otimizada em comparação com a busca linear. ``Arrays.binarySearch()`` é o método mais simples e eficiente para encontrar um elemento em uma matriz classificada em Java, só que para isso, **o array obrigatóriamente tem que estar ordenado**.
A seguir, uma tabela com os possíveis cenários de uso do método binarySearch() e o resultado da operação.

|                    Cenário                    |                                                                                                                                                                                       Resultado                                                                                                                                                                                        |
|:---------------------------------------------:|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|   **Elemento encontrado no array ordenado**   |                                                                                                                                                                         Retorna o índice do elemento no array                                                                                                                                                                          |
| **Elemento não encontrado no array ordenado** | (**-** _(ponto de inserção)_ **- 1**). _O ponto de inserção_ é definido como o índice em que o elemento seria inserido no array. Exemplo, no array {1, 3, 5}, se eu procurar pelo valor 4, ele deveria estar depois do 3 e antes do 5, respeitando a ordenação. Então ele deveria estar no índice [2] {1, 3, 4, 5}. Dessa forma, o resultado da busca binária seria (- 2 - 1): **-3**. |
|             **Array desordenado**             |                                                                                                                                                                                 Resultado imprevisível                                                                                                                                                                                 |
|      **Array com elementos duplicados**       |                                                                                                                                                Não há garantia de que será encontrado (geralmente traz o índice da primeira ocorrência)                                                                                                                                                |

Exemplo:
```java
  import java.util.Arrays;
  
  //Exemplo com números ordenados
  int[] myArr = {1, 3, 5};
  System.out.println(Arrays.binarySearch(myArr, 1)); //Output: 0 (O elemento foi encontrado no array, no índice [0])
  System.out.println(Arrays.binarySearch(myArr, 0)); //Output: -1 (O elemento deveria ser inserido na primeira posição do array, logo, (- 0 - 1): -1.
  System.out.println(Arrays.binarySearch(myArr, 5)); //Output: 2 (O elemento foi encontrado no array, no índice [2])
  System.out.println(Arrays.binarySearch(myArr, 4)); //Output: -3 (O elemento deveria ser inserido na terceira posição do array (índice [2]), logo, (- 2 - 1): -3.

  //Exemplo com Strings ordenadas
  String[] myArr2 = {"a", "c", "e"};
  System.out.println(Arrays.binarySearch(myArr2, "d")); //Output: -3 (O elemento deveria ser inserido na terceira posição do array (índice [2]), logo, (- 2 - 1): -3.
  System.out.println(Arrays.binarySearch(myArr2, "e")); //Output: 2 (O elemento foi encontrado no array, no índice [2])

  //Exemplo com números desordenados
  int[] myArr3 = {5, 1, 9};
  System.out.println(Arrays.binarySearch(myArr3, 5)); //Output: -3 (Imprevisível)
  System.out.println(Arrays.binarySearch(myArr3, 6)); //Output: -3 (Imprevisível)
``` 
---

### 3.4 Matriz (Arrays multidimensionais)
Uma matriz é um vetor de vetores, todos de mesmo tamanho. Em Java, é possível criar um vetor de vetores onde cada elemento tem um tamanho diferente. 

#### 3.4.1 Formas de declarar um Array Multidimensional:
```java
 //Declaração múltipla:
  int myArr[][], myArr2[][];  // ✅ myArr[][] & myArr2[][] são 2D 
  int[] myArr3[], myArr4[];   // ✅ myArr3[][] & myArr4[][] são 2D
  int[] []myArr5[], myArr6[]; // ✅ myArr5[][][] & myArr6[][][] são 3D - Isso ocorre porque todo colchete antes do nome da primeira variável é atribuido a todos na sequencia. 
  int[] myArr7[], myArr8[][]; // ✅ myArr7[][] & myArr8[][][] são 2D & 3D. 
  int []myArr9, []myArr10;    // ❌ Não compila! O colchetes não deve vir antes do nome da variável myArr10, só funcionou antes de myArr9 porque está declarado logo após o tipo da variável (int) e é interpretado como uma atribuição a todas as variáveis na sequência, e não exclusivamente ao myArr9.

 //-------------------------
 
 //Declaração única
  int[][] myArray;    // ✅ Os colchetes podem vir logo após o tipo da varíavel
  int [] [] myArray2; // ✅ Os colchetes podem vir entre o tipo da varíavel e o nome
  int myArray3[][];   // ✅ Os colchetes podem vir logo após o nome da varíavel
  int myArray4 [][];  // ✅ Os colchetes podem vir com espaços  após o nome da varíavel
  int[] myArray5[];   // ✅ Os colchetes podem vir separados, logo após o tipo e também após o nome da variável.
  []int myWrongArray6; // ❌ Não compila! Err: unexpected token.
  []int myWrongArray7[]; // ❌ Não compila! Err: unexpected token.

``` 

#### 3.4.2 Formas de inicializar um Array:
```java
  int[][] myMultArr = new int[3][2]; // ✅ Esse valor atribuido ao tamanho do array é um valor estático e imutável. A partir desse momento, ele sempre terá capacidade de 3 arrays que possuem capacidade 2.
  // Tamanho -> {[[0][1]], [[0][1]], [[0][1]]} todos com valor default 0 até que sejam inicializados.
  //             [  0  ]   [  1  ]   [  2  ]
 
  int[][] myMultArr2 = new int[][] {{1, 4}, {3}, {9, 8, 7}}; // ✅ Uma forma alternativa de declarar um array com valores iniciais e fixos e tamanhos diferentes
 
  int[][] myMultArr3 = {{1, 4}, {3}, {9, 8, 7}}; // ✅ Uma forma alternativa de declarar um array com valores iniciais e fixos e tamanhos diferentes
  
  int[][] myMultArr4 = new int[2][]; // ✅ É obrigatório inicializar uma matriz com valores no primeiro colchetes, representando a quantidade de arrays que caberiam dentro dele, e criar arrays de valores customizados.
  myMultArr4[0] = new int[5];
  myMultArr4[1] = new int[3];

  int[][] myMultArr4 = new int[][]; // ❌ Não compila! Err: array dimension missing. Embora seja permitido deixar indefinido o tamanho das últimas dimensões, o tamanho da primeira é obrigatório, assim como no array simples.

  int[][] myMultArr4 = new int[][3]; // ❌ Não compila! O contrário não é possível realizar, pois não deve ser possível declarar o tamanho dos arrays internos sem ter a quantidade dos externos.

``` 
---
### 3.5 ArrayList
O Java, por padrão, possui uma série de recursos prontos (*APIs*) para que possamos
tratar de estrutura de dados, também chamados de coleções (*collections*).
Podemos dizer que ``ArrayList`` é uma classe para coleções, a qual você pode realizar várias operações, como: adicionar e retirar elementos, ordenálos, procurar por um elemento específico, apagar um elemento específico, limpar o ArrayList, etc. 

#### 3.5.1 Formas de declarar um ArrayList:
```java
  //Declarando com um tipo específico
  List<String> myListOfStrings = new ArrayList<>(); //Declarando como List (interface) e instânciando a implementação (ArrayList) e deixando o tipo interno como String;
  myListOfStrings.add("abc"); // ✅
  myListOfStrings.add("xyz"); // ✅
  myListOfStrings.add(123);   // ❌ Não compila! Err: incompatible types. Isso ocorre porque deixei explícito qual seria o tipo do Array.
  
  //-----------------------------------

  //Declaração para lista genérica
  List myGenericList = new ArrayList(); //Declarando sem passar um tipo específico no generics, podendo armazenar qualquer tipo.
  myGenericList.add("abc"); // ✅
  myGenericList.add(123);   // ✅
  myGenericList.add(true);  // ✅

  List<Object> myGenericList2 = new ArrayList<>(); //Declarando passando Object como tipo específico no generics, podendo armazenar qualquer tipo.
  myGenericList.add("xyz"); // ✅
  myGenericList.add(321);   // ✅
  myGenericList.add(false); // ✅

  //-----------------------------------

  //Declaração com tamanho inicial
  List myList = new ArrayList(10); //A lista possui tamanho inicial 10, mas permanece dinâmico. Se necessário esse valor poderá ser ultrapassado sem nenhum problema.

  //-----------------------------------

  //Declaração com base em uma collection existente
  List myList2 = new ArrayList(myGenericList); //myList2 foi criada com o mesmo conteúdo de myGenericList, mas poderia ser qualquer outra classe que implemente 'Collection' dentro do parâmetro.

  //-----------------------------------
``` 

#### 3.5.2 Ordenar um ArrayList:
Diferente do ``array[]`` que utilizamos ``Arrays.sort()`` para ordenar, no ArrayList ou qualquer outra Collection nós utilizamos ``Collections.sort()`` também de ``java.util``.
```java
  List<Integer> myListOfNumbers = new ArrayList<>(); //Declarando como List (interface) e instânciando a implementação (ArrayList) e deixando o tipo interno como String;
  myListOfNumbers.add(5);
  myListOfNumbers.add(1);
  myListOfNumbers.add(2);

  Collections.sort(myListOfStrings); //Realizo a ordenação da minha lista

  myListOfStrings.forEach(System.out::println); // 1 2 5
```

#### 3.5.2 Ordenar um ArrayList:
Diferente do ``array[]`` que utilizamos ``Arrays.sort()`` para ordenar, no ArrayList ou qualquer outra Collection nós utilizamos ``Collections.sort()`` também de ``java.util``.
```java
  List<Integer> myListOfNumbers = new ArrayList<>(); //Declarando como List (interface) e instânciando a implementação (ArrayList) e deixando o tipo interno como String;
  myListOfNumbers.add(5);
  myListOfNumbers.add(1);
  myListOfNumbers.add(2);

  Collections.sort(myListOfStrings); //Realizo a ordenação da minha lista

  myListOfStrings.forEach(System.out::println); // 1 2 5
```

#### 3.5.3 Conversão Array & List:
A conversão de List para Array pode ser feita através do método ``toArray()`` de um ArrayList e a conversão de Array para List pode ser feita através do método ``Arrays.asList()``. Porém, a conversão gera uma lista e um array de tamanho fixo!
```java
  //De List para Array ----------------------------------------------------------------------------
  List<Integer> myNumberList = new ArrayList<>();
  myNumberList.add(10);
  myNumberList.add(20);
  myNumberList.add(30);

  Obejct[] myNumberArrayAsObject = myNumberList.toArray(); // ✅ O método toArray() retorna um Object[];
  Integer[] myNumberArray = myNumberList.toArray(new Integer[0]); // ✅ Passo como parâmetro 'new <tipo>[0]' se eu quiser obter uma cópia exata do que tem na minha lista de tipo específico, pois somente o método toArray() retorna o tipo Object.

  //De Array para List ----------------------------------------------------------------------------
  String[] myStringArray = {"abc", "xyz"}
  List<String> myListBasedOnArray = Arrays.asList(array); // ✅ Me gera uma lista de tamanho fixo.
  myListBasedOnArray.set(0, "aaa"); // Ok, alterei um valor existente que não impacta no tamanho.
  myListBasedOnArray.remove(0); // ❌ throws UnsupportedOperation Exception!
```

---
### 3.6 Datas e Horas com LocalDate, LocalTime e LocalDateTime
O Java 8 reconheceu a diferença tipos de uso de data e hora e definiu classes diferentes para cada um deles no novo pacote ``java.time``. ``LocalDate``, ``LocalTime`` e ``LocalDateTime`` são, portanto, classes separadas, embora sejam relacionadas e intercambiáveis, facilitando assim o tratamento de data e hora para um conjunto incontável de cenários.

**Objetivos de cada classe:**
- ``java.time.LocalDate``: uma instância LocalDate contém uma data sem fuso horário, no sistema de calendário ISO-8601 1 . LocalDate tem o formato padrão '_**AAAA-MM-DD**_' como em '**2016-12-12**'.
- ``java.time.LocalTime``: Um LocalTime mantém a hora no sistema de calendário **ISO-8601**, sem qualquer informação de data ou fuso horário associada a ele. O formato é normalmente '**_HH:mm:ss_**' como em '**12:10:35**'. LocalTime pode ter precisão de até nanossegundos (após o último segundo) com o formato ' **_HH:mm:ss.nnnnnnnn_** ' como em ' **12:10:35.123456789** '.
- ``java.time.LocalDateTime``: representa uma data e hora **sem fuso horário** no **formato ISO-8601**. Seu formato típico é ' _**AAAA-MM-DDTHH:mm:ss**_ '. (Observe o '**T**' separando dias de horas) como em ' **2016-12-12T12:10:35** '. LocalDateTime também pode ter um componente de nanossegundo de segundo, como em LocalTime.

**⚠️ Pontos de atenção:**
**Nenhuma das três classes podem ser instânciadas com o uso da palavra reservada** ``new``. Isso ocorre porque essas classes possuem construtores privados, portanto, o único meio de instânciá-las é com o uso de seus métodos próprios, como por exemplo ``LocalDateTime.now()`` que retorna uma instância referente à data e horário atual.

#### 3.6.1 Tabela de métodos permitidos em cada tipo
| Método()                               | LocalDate? | LocalTime? | LocalDateTime? |
|:---------------------------------------|:----------:|:----------:|:--------------:|
| ``plusYears()`` & ``minusYears()``     |     ✅     |     ❌    |      ✅       |
| ``plusMonths()`` & ``minusMonths()``   |     ✅     |     ❌    |      ✅       |
| ``plusWeeks()`` & ``minusWeeks()``     |     ✅     |     ❌    |      ✅       |
| ``plusDays()`` & ``minusDays()``       |     ✅     |     ❌    |      ✅       | 
| ``plusHours()`` & ``minusHours()``     |     ❌     |     ✅    |      ✅       |
| ``plusMinutes()`` & ``minusMinutes()`` |     ❌     |     ✅    |      ✅       |
| ``plusSeconds()`` & ``minusSeconds()`` |     ❌     |     ✅    |      ✅       |
| ``plusNanos()`` & ``minusNanos()``     |     ❌     |     ✅    |      ✅       |

#### 3.6.2 Utilizando java.time.Period para manipulação de datas
A classe ``java.time.Period`` usa as unidades ano, mês e dia para representar um período de tempo. Com um ``Period`` criado, ele pode ser passado como parâmetro nos métodos de ``LocalDate`` e ``LocalDateTime`` para manipulação de data.
```java
  import java.time.Period;
  
  public class PeriodCases{
    void example(){
      
      //Métodos e formas de declarar
      Period annually = Period.ofYears(1);
      Period monthly = Period.ofMonths(3);
      Period everyThreeWeeks = Period.ofWeeks(3);
      Period everyTwoDays = Period.ofDays(2);
      Period everyAYearMonthAndDay = Period.of(1, 12, 23); //1 Year, Month 12, day 23

      //------------------------------------------------------------------------------------

      //⚠️ Métodos encadeados não funcionam! Somente o último contabiliza!
      Period wrong = Period.ofYears(1).ofWeeks(1); //Será somente um período de semanas, pois ele foi o último a ser chamado!
      //O código a seguir é uma representação do que foi feito acima:
      Preriod wrong = Period.ofYears(1);
      wrong = Period.ofWeeks(1); //Por isso somente o último método a ser chamado foi contabilizado!
    }
  }
```
A manipulação de ``LocalDateTime`` e ``LocalDate`` utilizando ``Period``, pode ocorrer por meio dos métodos ``plus()`` ou ``minus()``.
```java
  LocalDate date = LocalDate.of(2023, 1, 20);
  LocalTime time = LocalTime.of(6, 15);
  LocalDateTime dateTime = LocalDateTime.of(date, time);

  Period annually = Period.ofYears(1);

  System.out.prinln(date.plus(period)); // 2024-01-20 - Adicionei 1 ano utilizando o método 'plus()' passando o Period como parâmetro.
  System.out.prinln(dateTime.plus(period)); // 2024-01-20T06:15 - Adicionei 1 ano utilizando o método 'plus()' passando o Period como parâmetro.
  System.out.prinln(dateTime.plus(period)); // ❌ Err: UnsupportedTemporalTypeException - Não é possível utilizar period para dateTime, já que os períodos são referentes a anos, meses, semanas e dias, sendo incompatível com o padrão trabalhado em dateTime (Horas, Minutos, Segundos e Nanos)
```
---
## 4 Métodos e Encapsulamento
---
### 4.1 Design de Métodos
Um método em Java é equivalente a uma função, subrotina ou procedimento em outras linguagens de programação. Não existe em Java o conceito de métodos globais. Todos os métodos devem sempre ser definidos dentro de uma classe.
Os métodos possuem uma assinatura que deve ser respeitada, sendo essa:
```java
  public final void nap(int minutes) throws InterruptedException{
      //take a nap
  }
```
- ``public``: access modifier
- ``final``: optional specifier
- ``void``: return type
- ``nap``: method name
- ``(int minutes)``: list of parameters (parentheses required)
- ``throws InterruptedException``: exception (optional)
- ``{ }``: method body (required ``{}``)

**⚠️ Partes da declaração de um método**

| Elemento                 | Valor no exemplo: nap()         | Obrigatório? |
|:-------------------------|:--------------------------------|:------------:|
| Modificador de Acesso    | ``public``                      |      ❌       |
| Especificador            | ``final``                       |      ❌       |
| Tipo de retorno          | ``void``                        |      ✅       |
| Nome                     | ``nap``                         |      ✅       |
| Parênteses de Parâmetros | ``()``                          |      ✅       |
| Lista de exceptions      | ``throws InterruptedException`` |      ❌       |
| Corpo                    | ``{ }``                         |      ✅       |

Exemplo:
```java
  public void myMethod(String myParam) throws InterruptedException{} // ✅ Não precisa ter corpo, somente as chaves {} são obrigatórias.
  public void myMethod2(String myParam){} // ✅ Não precisa ter lista de exceptions que o método lança, se ele não lançar uma Exception rastreavel.
  public void myMethod3(){} // ✅  Parâmetros não são obrigatórios, somente os parênteses () são obrigatórios.
  void myMethod4(){} // ✅ Modificador de acesso não é obrigatório          
  
  public void myMethod4{} // ❌ Não compila! É preciso ter os parênteses de parâmetros no método!
  public void myMethod5() // ❌ Não compila! É preciso ter as chaves de corpo do método!
  public myMethod6(){} // ❌ Não compila! É preciso ter o tipo do retorno do método!
  public void (){} // ❌ Não compila! O método precisa ter um nome!

```

**⚠️ Modificadores de Acesso** (Do maior privilégio pro menor)
- **public:** pode ser invocado em qualquer classe
- **protected:** em classes do mesmo pacote ou subclasses
- **Default:** apenas em classes do mesmo pacote (**⚠️ Atenção!** Não existe a keyword 'Default', essa é a descrição do comportamento Default na ausência dos 3 modificadores de acesso. Ex: ``void test(){}``. O modificador ``default`` só existe dentro de interfaces!)
- **private:** apenas dentro da classe onde foi criado

Exemplo:
```java
  public void myMethod(){}    // ✅ 
  private void myMethod2(){}   // ✅ 
  protected void myMethod3(){} // ✅ 
  void myMethod4(){} // ✅          
  default void myMethod5(){} // ❌ Não compila! A Keyword default só pode ser utilizada dentro de interfaces.
  void public myMethod6(){}  // ❌ Não compila! O tipo do método DEVE vir antes do nome do método.
```


**⚠️ Especificadores opcionais** (Somente os que estão em negrito são cobrados na prova);
- **static** (Métodos estáticos são métodos de Classe);
- **abstract** (Métodos que **NÃO possuem corpo** ``{}``. **Só podem ser declaradas dentro de classes abstratas!**);
- **final** (Métodos que possuem corpo ``{}``, mas subclasses **NÃO podem** sobreescrever esse método.);
- synchronized;
- native;
- strictfp.

**⚠️ Nome dos métodos**
Ao nomear seus métodos, é preciso se atentar as seguintes regras (iguais as das variáveis):
- O nome deve iniciar com ``LETRA (a-z)`` ou ``$`` ou ``_``
- Nos caracteres seguintes também podem haver ``NÚMEROS``
- Palavras reservadas não podem ser usadas (lembre-se: Java é case-sensitive)

---
### 4.2 Constantes e Membros Estáticos
#### 4.2.1 Modificador final
Em java, utilizamos o modificador `final` para **definir constantes.**
Esse modificador pode ser utilizado em vários contextos, são eles:
- **Classes:** Uma classe com este modificador não pode ser estendida, isto é, não pode ter classes que herdam dela. Isso é importante para garantir que uma determinada implementação não tenha seu comportamento modificado. Isso tem muito a ver com a **imutabilidade**.
  ```java
  public final class MyImmutableClass{ // Declaro minha classe como final
      // ....
  }

  class MySimpleClass extends MyImmutableClass{ // ❌ Erro de compilação! Cannot inherit from final 'MyImutableClass'
      // ....
  }
  ```
- **Métodos:** É um método que não pode ser sobrescrito nas subclasses. Use para garantir que um determinado algoritmo não possa ser modificado pelas subclasses.
  ```java
  public class MyClassWithConstantMethod{

      public final void myFinalMethod(){ // Declaro meu método como final 
          System.out.println("I am immutable!");
      }
  }

  class MySimpleClass extends MyClassWithConstantMethod{ 

      @Override
      public final void myFinalMethod(){ // ❌ Erro de compilação! 'myFinalMethod()' cannot override 'myFinalMethod()' in 'MyClassWithConstantMethod'; overridden method is final
          System.out.println("I can't override a final method :/");
      }
  }
  ```
- **Atributos:** Um atributo final de uma classe pode ter seu valor atribuído uma única vez, seja na própria declaração ou no construtor. Use isso para garantir que um valor ou referência de objeto não vai mudar. Voltamos à **imutabilidade**.
  ```java
  public class MyClassWithConstantAttribute{

    private static final String WRONG_EXAMPLE; // ❌ Erro de compilação! Variable 'WRONG_EXAMPLE' might not have been initialized. Atributos final PRECISAM ser inicializados! Seja no momento em que foi declarado, ou em blocos de códigos inicializadores.
    private static final Double PI = 3.14; // Declaro meu atributo como final

    public static void main(String []args){
      PI = 4.0; // ❌ Erro de compilação! Cannot assign a value to final variable 'PI'
    }
  }
  ```
- **Variáveis:** Use para garantir que você não está modificando o valor indevidamente.
Link interessante para consulta sobre níveis de acesso
  ```java
  public class MyClassWithConstantVariable{

    public static void main(String []args){
      final Double PI = 3.14; // Declaro minha variável como final
      PI = 4.0; // ❌ Erro de compilação! Cannot assign a value to final variable 'PI'
    }
  }
  ```

#### 4.2.2 Modificador static
Ele muda o **escopo** de um método ou atributo. Com o ``static``, ao invés deles pertencerem à instância do objeto, eles pertencem à classe. Não é possível importar uma classe static! Esse modificador pode ser utilizado em vários contextos, são eles:
- **Imports:** Em Java, o "import estático" é uma funcionalidade que permite que você importe atributos e métodos estáticos de uma classe diretamente, sem precisar instanciá-la.
Suponha que você tenha uma classe chamada `MathUtils` com vários membros estáticos, como constantes e métodos:
  ```java
    public class MathUtils {
      public static final double PI = 3.14159265359;
      
      public static int add(int a, int b) {
          return a + b;
      }
    }
  ```
  Com o uso de import estático, você pode importar os membros estáticos da classe MathUtils diretamente:
  ```java
    import static com.example.MathUtils; // ❌ Não posso fazer um import estático para classes, somente para os membros dentro dela!
    import static com.example.MathUtils.PI; // ✅ Posso fazer o import estático específicando qual membro desejo usar
    import static com.example.MathUtils.*; // ✅ Assim como também posso fazer o import de todos os membros estáticos presente dentro da minha classe MathUtils

    public class Main {
        public static void main(String[] args) {          
            System.out.println("PI: " + PI); // 3.14159265359
            System.out.println("Sum: " + add(10, 5)); // 15
        }
    }

  ```
- **Classes:** As classes estáticas são classes que são declaradas dentro de outra classe, mas são marcadas com o modificador static. Elas são usadas principalmente para fins de organização, encapsulamento e para evitar a necessidade de criar uma instância da classe externa para acessá-las.
  ```java
  public class OuterClass {
    private static int outerField = 10;

    public static class StaticNestedClass {
        public void displayOuterField() {
            System.out.println("Value of outerField: " + outerField);
        }
    }

    public static void main(String[] args) {
        // Criando uma instância da classe estática
        OuterClass.StaticNestedClass nestedObject = new OuterClass.StaticNestedClass();
        nestedObject.displayOuterField();
    }
  }
  ```
- **Blocos de código (Inicializadores):** Blocos inicializadores estáticos em Java são usados para inicializar variáveis estáticas (membros estáticos) de uma classe. Eles são executados quando a classe é carregada pela primeira vez, antes mesmo de qualquer instância da classe ser criada. Esses blocos são usados para executar código de inicialização que precisa ser executado uma única vez para toda a classe.
  ```java
  public class MyClassWithStaticBlock{

      private static final int NUM_SECONDS_PER_HOUR;

      //Bloco de inicialização é executado uma única vez a partir do momento em que minha classe for referenciada no programa.
      static{ // ✅ Declarei meu bloco inicializador estático, que fará uma atribuição ao meu atributo estático e constante.
        int numSecondsPerMinute = 60;
        int numMinutesPerHour = 60;
        NUM_SECONDS_PER_HOUR = numSecondsPerMinute * numMinutesPerHour; 
      }
  }

  ```
  Os blocos inicializadores estáticos são úteis quando você precisa realizar a inicialização de variáveis estáticas de maneira mais complexa do que uma simples atribuição, ou quando precisa executar código que não se encaixa bem em um construtor.
  <br>
- **Métodos:** Os métodos static podem ser chamados sem uma instância. São ótimos como utilitários.
  ```java
    package com.example.MathUtils;

    public class MathUtils {
      public static int add(int a, int b) { // ✅ Declaro meu método estático
          return a + b;
      }
    }

    //----------------------------------
    package com.example.MyClass;

    public class MyClass {
      public static void main(String[] args) {          
          System.out.println("Sum: " + MathUtils.add(10, 5)); // 15 (Realizo a chamada para o método estático, sem ter precisado instanciar a classe MathUtils).
      }
    }
  ```
- **Atributos:** Os atributos ``static`` possuem o mesmo valor para todas as instâncias de um objeto (dentro de uma mesma JVM, dentro de um mesmo ClassLoader).
  ```java
    package com.example.MathUtils;

    public class MathUtils {
        public static final double PI = 3.14159265359;
        public static int myLuckyNumber = 7;
    }

    //----------------------------------
    package com.example.MyClass;

    public class MyClass {
      public static void main(String[] args) {          
          System.out.println(MathUtils.PI); // 3.14159265359 (Realizo a chamada para o atributo estático, sem ter precisado instanciar a classe MathUtils).
          System.out.println(MathUtils.myLuckyNumber); // 7
          MathUtils.myLuckyNumber = 3; //É um membro estático e não final, portanto consigo alterar seu valor golbalmente.
          System.out.println(MathUtils.myLuckyNumber); // 3
      }
    }
  ```
https://pt.stackoverflow.com/questions/17015/qual-o-uso-de-uma-variável-estática-ou-final-em-java

---
### 4.3 Parâmetros e Retorno de Métodos
Em Java a passagem de parâmetros é sempre uma cópia. Se for um tipo primitivo, é uma cópia do valor. Se for um Objeto, é uma cópia da referência.
Exemplo
  ```java
    public class Main {
      public static void main(String[] args) {
          //Com tipo primitivo
          int myPrimitiveNumber = 0;
          newNumber(myPrimitiveNumber);
          System.out.println(myPrimitiveNumber); // ✅ Permanece 0. O valor não foi alterado, porque o método manipulou somente uma cópia que foi enviada para o parâmetro.

          //Wrapper classes dos tipos primitivos
          //⚠️ Quando você usa Wrapper Class para tipos primitivos, a passagem de argumentos ainda é feita por valor (cópia da referência ao objeto), mas os wrapper classes se mantém imutáveis. Qualquer alteração em um objeto imutável cria uma nova instância com os valores alterados, em vez de modificar o objeto original.
          String myLiteralString = "Literal";
          newString(myLiteralString);
          System.out.println(myLiteralString); // ✅  Permanece "Literal". A String possui um comportamento semelhante aos tipos primitivos.

          Integer myObjectNumber = 0;
          System.out.println(myObjectNumber);  // ✅  Permanece 0. Por ser uma classe semelhante a um tipo primitivo, o comportamento esperado é o mesmo.
          newNumber(myObjectNumber);

          //Com Objetos mutáveis
          StringBuilder myMutableStringBuilder = new StringBuilder("my value");
          newStringBuilder(myMutableStringBuilder);
          System.out.println(myMutableStringBuilder); // ✅ "my value - has been added." Por se tratar de um objeto mutável, foi realizada a alteração direto pela referência fornecida, alterando as propriedades do objeto.
      }

      public static void newNumber(int num){
          num = 10;
      }

      public static void newNumber(Integer num){
          num = 10;
      }

      public static void newString(String string){
          string = "new value";
      }

      public static void newStringBuilder(StringBuilder string){
          string.append(" - has been added");
      }

    }
  ```

  ---
### 4.4 Sobrecarga de Métodos (*overload*)
A sobrecarga de método em Java permite que você defina vários métodos com o mesmo nome em uma classe, desde que tenham diferentes listas de parâmetros. Fique atento, pois você não pode criar dois métodos com a mesma assinatura e nomes de parâmetro, mesmo que os tipos de retorno sejam diferentes!
```java
  public class OverloadMethods {
    public void myMethod(int numMiles){}  // ✅
    public int myMethod(int numMiles){    // ❌ Não compila! Err: 'myMethod(int)' is already defined in OverloadMethods. Apesar do tipo do retorno ser diferente, o contrato (nome e parâmetros) são do mesmo tipo.
        return 1;
    }  
    
    public void myMethod(short numFeet){}         // ✅
    public static void myMethod(short numFeet){}  // ❌ Não compila! Err: 'myMethod(int)' is already defined in OverloadMethods. Apesar do método ser estático, o contrato (nome e parâmetros) são os mesmos.
    
    public void myMethod(int[] lengths){}   // ✅
    public void myMethod(int... lengths){}  // ❌ Não compila! Err: 'myMethod(int)' is already defined in OverloadMethods. Apesar do método ser estático, o contrato (nome e parâmetros) são os mesmos.

    public boolean myMethod(){return false;}  // ✅
    public void myMethod(int numMiles, short numFeet){} // ✅
    public void myMethod(short numFeet, int numMiles){}  // ✅

    // ---------------- Sobrecarga de tipos de referência
    public void myMethod(Integer numMiles){}  // ✅ Posso ter sobrecarga utilizando o tipo Integer, mesmo tendo declarado um com tipo int, pois o Java faz o Autoboxing automaticamente de um pra outro.
    public void myMethod(Object numMiles){}  // ✅ O Java vai procurar um tipo específico, se não encontrar ele "sobe" para os mais genéricos até encontrar um compatível com o parâmetro passado.
  }
```
**⚠️ Ordem utilizada pelo java**

| Regra                      | Exemplo                                                  | 
|:---------------------------|:---------------------------------------------------------|
| Match pelo tipo exato      | `public String myMethod`**(int i, int j)**`{}`           |
| Tipos primitivos "maiores" | `public String myMethod`**(long i, long j)**`{}`         |
| Autobox do tipo            | `public String myMethod`**(Integer i i, Integer j)**`{}` |
| Varargs                    | `public String myMethod`**(int... nums)**`{}`            |

Exemplo:
```java
  public class OverloadMethods2 {
    public String myMethod(String s){
      return "1";
    }
    public String myMethod(String ...s){
      return "2";
    }
    public String myMethod(Object o){
      return "3";
    }
    public String myMethod(String s, String t){
      return "4";
    }
  
    public static void main(String[] args){
      System.out.println(myMethod("a")); // 1
      System.out.println(myMethod("a", "b")); // 4
      System.out.println(myMethod("a", "b", "c")); // 2
      System.out.println(myMethod(new Integer(2))); // 3
    }
  }
```
**⚠️ Problema conversão dupla**
Pode ocorrer um problema de conversão dupla (ocasionado pela fé de que o Java irá converter tudo automaticamente kkkk)
```java
  public class OverloadMethods2 {
    
    public String myMethod(Long l){}
    
    public String myMethod(Long ...l){}
  
    public static void main(String[] args){
      //O Java não encontra um método com int (primitivo) e realiza o AutoBox para Integer (wrapper class), que por sua vez também não encontra nenhum tipo equivalente, ocasionando um erro de compilação.
      myMethod(4); // ❌ Não compila! O Java aceitaria se o método fosse um long (tipo primitivo) .
      
      myMethod(4L); // ✅ Chama myMethod(Long l)
    
    }
  }
```
---
### 4.4 Sobrescrita de Métodos (*override*)
A sobrescrita (ou *override*) está diretamente relacionada à orientação a objetos, mais especificamente com a herança. Com a sobrescrita, conseguimos especializar os métodos herdados das superclasses, alterando o seu comportamento nas subclasses por um mais específico.
Se atente às regras:
- **Os métodos das classes pai e filha devem ter a mesma assinatura (tipo, nome e parâmetros)**
  Exemplo:
  ```java
  class A {
      public int calc(int a, int b) {
          return a + b;
      }
  }

  class B extends A {
      public double calc(int a, int b) { // ❌ Não compila! 'calc(double, double)' in 'B' clashes with 'calc(double, double)' in 'A'; attempting to use incompatible return type
          return a + b;
      }

      //✅ Esse exemplo iria compilar e funcionará como sobrescrita, pois possui a mesma assinatura (retorno, nome e parâmetros)
      public int calc(int a, int b) {  
          return a + b;
      }

      //✅⚠️ Compila, porém não é uma sobreescrita do método! (tipos diferentes)
      public int calc(long a, long b) {  
          return a + b;
      }

      // ✅⚠️ Compila, porém não é uma sobreescrita do método! (nome diferente)
      public int calculation(int a, int b) {  
          return a + b;
      }
  }
  ```
  
- **Na classe filha, o método deve ter a mesma acessibilidade ou maior.** Exemplo, ClassFather possui um método com modificador protected. A classe filha que extende essa classe, ao sobreescrever esse método só poderá usar modificadores equivalente (protected) e public, modificadores a baixo disso ocasionariam um erro de compilação. Err: cannot override inherance() in br.com.YourClass attempting to assign weaker access privileges;
- **Na filha, o método não pode lançar uma checked exception nova ou mais abrangente que alguma exceção no pai**
  Exemplo:
  ```java
  class A {
      public void methodNotThrows(){ 
          //Esse método não lança uma exception 
      }

      public void methodThrows() throws IOException{ //Lança uma exception específica.
          throw new IOException();
      }

  }

  class B extends A {

      public void methodNotThrows() throws Exception{ // ❌ Não compila! Foi adicionado uma exception nova em um método que não lança nada. 'methodNotThrows()' in 'B' clashes with 'methodNotThrows()' in 'A'; overridden method does not throw 'java.lang.Exception'
        //Esse método não deveria lançar uma exception 
      }

      //------------------------------------------------------------------------

      public void methodThrows() throws Exception { // ❌ Não compila! 'methodThrows()' in 'B' clashes with 'methodThrows()' in 'A'; overridden method does not throw 'java.lang.Exception'
          throw new Exception();
      }

      //✅ Esse exemplo poderia, já que estoura uma exception mais específica.
      public void methodThrows() throws RuntimeException {
          throw new RuntimeException();
      }

      //✅ Esse exemplo poderia, eu posso não estourar nenhuma exception na classe filha.
      public void methodThrows(){
          //I chose not to throw any exceptions! :)
      }
  }
  ```
- **Se o método retorna valor, este deve ser da mesma classe ou uma subclasse do método na classe pai (covariant return types).** Exemplo, ClassFather possui um método com tipo de retorno Object e DaughterClass possui o mesmo método só que com um retorno de subclasse de Object, como String. O contrário disso ocasiona um erro de compilação.
- **Métodos privados não são sobreescritos, e sim possui identidade única.** Isso ocorre porque métodos privados são únicos dentro do seu escopo de classe, e portanto, não são herdados ou transferidos por meio de herança. 
  Exemplo:
  ```java
  class A {
      private String returnAnyValue() {
          return "random string";
      }
  }

  class B extends A {
      private int returnAnyValue() { // ✅ Não tem problema trocar a assinatura, porque não há relação entre esse método e o método na classe pai. Eles são privados e únicos dentro do seu próprio escopo.
          return Math.random();
      }
  }
  ```
- **Métodos marcados como ``final`` NÃO PODEM ser sobreescritos.** Diferente de métodos marcados com ``private``, que apenas não são sobreescritos mas compilam caso tenha alguma assinatura diferente, métodos marcados com ``final`` não podem ser sobreescritos (literalmente), ocasionando um erro de compilação. 
  Exemplo:
  ```java
  class A {
      public final int sum(int a, int b) {
          return a + b;
      }
  }

  class B extends A {
      public int sum(int a, int b) { // ❌ Erro de compilação! Err: 'sum(int, int)' cannot override 'sum(int, int)' in 'A'; overridden method is final
          return a + b;
      }
  }
  ```
- **Se o método está marcado como static na classe pai, então deve ser static na classe filha. Da mesma forma não pode ser static na classe filha se não for static na classe pai.** Além disso, valores estáticos na classe pai não são sobrescritos na classe filha, porque membros estáticos são membros únicos de classe! 
  - **Exemplos que não compilam:**
    ```java
    class Bear {
      public static void sneeze(){ System.out.println("Bear is sneezing");}
      public void hibernate(){System.out.println("Bear is hibernating");}
    }

    class Panda extends Bear {
      public void sneeze(){ System.out.println("Panda is sneezing");} // ❌ Não compila! Instance method 'sneeze()' in 'Panda' cannot override static method 'sneeze()' in 'Bear'
      public static void hibernate(){System.out.println("Bear is hibernating");} // ❌ Não compila! Static method 'hibernate()' in 'Panda' cannot override instance method 'hibernate()' in 'Bear'
    }
    ```
  - **Exemplos de *hiding* com membro estático**
    ```java
    class Marsupial {
      public static boolean isBiped(){ //Realizo uma declaração estática que será única quando realizada a chamada deste método. Esse valor não é alterado na sobreescrita, porque é um membro estático!
        return false;
      }
      public void getMarsupialDescription(){
        System.out.println("Marsupial walks on two legs: " + isBiped());
      }
    }
    ```
    ```java
    class Kangaroo extends Marsupial {
      public static boolean isBiped(){ // Não sobrescreve o retorno da classe pai, porque membros estáticos são membros de classe e não de instância!
        return true;
      }
      public void getKangarooDescription(){
        System.out.println("Kangaroo walks on two legs: " + isBiped());
      }
    }
    ```
    ```java
    class Main{
      public static void main(String[] args){
        Kangaroo kangaroo = new Kangaroo();
        kangaroo.getMarsupialDescription(); // ✅⚠️ Marsupial walks on two legs: false -> Não houve a sobrescrita do valor do método!
        kangaroo.getKangarooDescription(); //Kangaroo walks on two legs: true
      }
    }
    ```
  - **Mesmo exemplo só que com *override* sem o membro estático**
    ```java
    class Marsupial {
      public boolean isBiped(){ 
        return false;
      }
      public void getMarsupialDescription(){
        System.out.println("Marsupial walks on two legs: " + isBiped());
      }
    }
    ```
    ```java
    class Kangaroo extends Marsupial {
      public boolean isBiped(){
        return true;
      }
      public void getKangarooDescription(){
        System.out.println("Kangaroo walks on two legs: " + isBiped());
      }
    }
    ```
    ```java
    class Main{
      public static void main(String[] args){
        Kangaroo kangaroo = new Kangaroo();
        kangaroo.getMarsupialDescription(); // ✅⚠️ Marsupial walks on two legs: true -> Esse valor foi alterado pela sobrescrita do método!
        kangaroo.getKangarooDescription(); //Kangaroo walks on two legs: true
      }
    }
    ```
---
### 4.5 Classes e Métodos abstratos (*abstract*)
A abstração de dados é o processo de ocultar certos detalhes e mostrar apenas informações essenciais ao usuário. A palavra-chave ``abstract`` é um modificador de não acesso, usado para classes e métodos:
- **Classe abstrata:** é uma classe restrita que não pode ser utilizada para criar objetos (para acessá-la deve ser herdada de outra classe). Regras:
  - Classes abastratas não podem ser instanciadas diretamente (``new AbstractClass()``);
  - Elas podem ter um número qualquer de métodos concretos (com corpo ``{}``) e abstratos (sem corpo ``{}`` - incluindo ZERO);
  - Não podem ser marcadas como ``private`` ou ``final``;
  - Quando estendem outra classe abstrata herdam todos os métodos abstratos com se fossem delas.
  - A primeira classe concreta (sem ser ``abstract``) na hierarquia de classes deve conter implementação para todos os métodos abstratos.

  Exemplos:
  ```java
  public abstract class A { // Declaro minha classe abstrata ✅
  }
  ```
  ```java
  public abstract class A { // Declaro minha classe abstrata ✅
    public abstract String getName();
  }

  class B extends A{ // ❌ Não compila! Ao extender uma classe abstrata com métodos abstratos, deve-se OBRIGATORIAMENTE implementar seus métodos! Err: Class 'B' must either be declared abstract or implement abstract method 'getName()' in 'A'
  }
  ```
  ```java
  public abstract class A { 
    public static void main(String... args){
      A a = new A(); // ❌ Não compila! Não posso instanciar classes abstratas! Err: 'A' is abstract; cannot be instantiated
    }
  }
  ```
  ```java
  public final abstract class A { // ❌ Não compila! Uma classe não pode ser final e abstrata ao mesmo tempo, pois um princípio fere o outro. Final impede que seja herdada, enquanto abstract necessita que seja. Err Illegal combination of modifiers: 'final' and 'abstract'
  }
  ```
- **Método abstrato:** só pode ser usado em uma classe abstrata e não possui corpo. O corpo é fornecido pela subclasse (herdado de). Regras:
  - Métodos abtratos só podem ser definidos em clases abstratas;
  - Eles não podem ser ``private`` ou ``final``;
  - Não deve ter implementação na classe abstrata onde foi definido. (Isso é, se ele for abstrato ele não deve ter um body ``{}``);
  - Ao implementar um método abstrato em uma subclasse devem ser seguidas as mesmas regras de sobrescrita de método.
  
  Exemplos:
  ```java
  public abstract class B { 
    public abstract int calc(int a, int b); // ✅ Método abstrato e sem corpo dentro de uma classe abstrata.
  }
  ```
  ```java
  public class B { 
    public abstract int calc(int a, int b); // ❌ Não compila! Err: Abstract method in non-abstract class
  }
  ```
  ```java
  public abstract class B { 
    public abstract int calc(int a, int b){ // ❌ Não compila! Err: Abstract methods cannot have a body
         return a * b;
    } 
  }
  ```
  ```java
  public abstract class B { 
    public abstract final int calc(int a, int b); // ❌ Não compila! Um método não pode ser final e abstrato ao mesmo tempo, pois um princípio fere o outro. Final impede que seja sobrescrito, enquanto abstract necessita que seja. Err Illegal combination of modifiers: 'final' and 'abstract'
  }
  ```
  ---
### 4.6 Interfaces
A ``interface`` é um recurso muito utilizado em Java, bem como na maioria das linguagens orientadas a objeto, para “obrigar” a um determinado grupo de classes a ter métodos ou propriedades em comum para existir em um determinado contexto, contudo os métodos podem ser implementados em cada classe de uma maneira diferente.
Definindo uma interface:
- Interfaces não podem ser instanciadas diretamente. ( ❌ ``new MyInterface()``);
- Não é obrigatório ter métodos;
- Não pode ser marcada como final (porque por padrão ela é abstract);
- Todas têm acesso ``public`` ou ``default`` e são ``abstract``. Modificadores diferente disso, ocasionam erro de compilação.
- Todos os MÉTODOS que não são ``default``, são ``public`` e ``abstract`` por definição;
- Uma classe pode implementar mais de uma interface por vez, por meio do uso da palavra `implements`. (``MyClass implements MyInterfaceA, MyInterfaceB, MyInterfaceC{}``);
- Uma interface é composta por atributos constantes (``final``), contratos de métodos e métodos ``default`` (com implementação).
  
Exemplo de interface:
  ```java
  public abstract interface MyInterface { 
    public static final int BRAZILIAN_COUNTRY_CODE = 55;
    public abstract String getBrazilianPhoneNumber();
  }
  ```
Agora se formos remover todas as informações desnecessárias e redundantes, que já são interpretadas por padrão pelo Java, nossa interface ficaria da seguinte forma:
  ```java
  public interface MyInterface { 
    int BRAZILIAN_COUNTRY_CODE = 55;
    String getBrazilianPhoneNumber();
  }
  ```
    
Exemplos que não compilam:
  ```java
  private final interface MyInterface { // ❌ Não compila devido o 'private' e o 'final' declarados. 
    private void dig(int depth); // ❌ Não compila devido o 'private'
    protected abstract double depth(); // ❌ Não compila devido o 'protected'
    public final void surface(); // ❌ Não compila devido o 'final'
  }
  ```
#### 4.6.1 Herança de Interfaces
Assim como nas classes, é possível realizar herança por meio do uso de interfaces. As regras são:
- Interfaces que estendem de outra interfacem herdam todos os métodos abstratos como se fosse delas;
- A primeira classe concreta que implementa uma interface, ou herda de uma classe abstrata que implementa uma interface deve prover implementação para todos os métodos abstratos herdados;
- Interfaces DEVEM estender somente de outras interfaces. Se uma interface estender de uma classe ou vice-versa, ocasiona um erro de compilação;
- Diferente das classes, interfaces são adaptadas para aceitar herança múltipla.
  ```java
  public interface Herbivore { 
    public void eatPlants(); 
  }
  ```
  ```java
  public interface Omnivore {
    public void eatPlants(); // ✅ Não tem problema eu ter um mesmo método em diferentes interfaces, no final a implementação será a mesma para ambos. O único cenário que ocasionaria algum erro, seria se o TIPO do retorno fosse diferente.
    public void eatMeats();  
  }
  ```
  ```java
  public interface Bear extends Herbivore, Omnivore { // ✅ Herança múltipla é permitido em interfaces 
  }
  ```
- Classes que implementam múltiplas interfaces ou interfaces que herda mais de uma interface tem que se atentar ao contrato dos métodos presentes nas interfaces. Se o contrato for o mesmo e o tipo de retorno for diferente, ocasiona um erro de compilação!.
  ```java
  public interface Herbivore { 
    public void eatPlants(); 
  }
  ```
  ```java
  public interface Omnivore {
    public int eatPlants(); // ❌ Esse método compila, mas ocasionará um erro ao ser implementado/herdado junto com o eatPlants() de Herbivore, por ter tipo de retono diferente.
    public void eatMeats();  
  }
  ```
  ```java
  public interface Bear extends Herbivore, Omnivore { // ❌ 'eatPlants()' in 'Omnivore' clashes with 'eatPlants()' in 'Herbivore'; methods have unrelated return types
  }
  ```
  ```java
  public class Bear implements Herbivore, Omnivore { // ❌ 'eatPlants()' in 'Omnivore' clashes with 'eatPlants()' in 'Herbivore'; methods have unrelated return types
  }
  ```
#### 4.6.2 Atributos de Inteface
Como ja dito, as interfaces podem possuir atributos (contantes) em sua composição. Os atributos possuem algumas regras:
1. Devem ser ``public``, ``static`` e ``final``. (Não é preciso deixar explícito, o Java já realiza isso).
2. O valor DEVE ser atribuído logo na declaração, senão ocasiona um erro de compilação.

Exemplo que compila ✅ 
```java
  public interface MyInterface{ 
    int MAXIMUM_DEPTH = 100; // ✅ Compila, o Java ja trata implicitamente os atributos 'public' 'static' e 'final'.
    final static boolean UNDERWATER = true; // ✅ Compila
    public static final String TYPE = "Submersive"; // ✅ Compila
  }
```
Exemplo que NÃO compila ❌ 
```java
  public interface MyInterface{ 
    private int MAXIMUM_DEPTH = 100; // ❌ Não Compila! O acesso não pode ser diferente de 'public'. (O default de interface é 'public').
    protected abstract static boolean UNDERWATER = true; // // ❌ Não Compila! O acesso não pode ser diferente de 'public'. E não existe uso do 'abstract' em atributos.
    public static String TYPE; //  ❌ Não Compila! O atributo DEVE ser inicializado, pois ele é implicitamente 'final' e requer uma inicialização.
  }
```
#### 4.6.2 Métodos default
A partir do Java 8, se tornou possível criar métodos com implementação em interfaces. Para isso, fazemos o uso da palavra reservada ``default`` na declaração do método. Métodos ``default`` possuem uma série de regras para uso, são elas:
- Só podem ser declarados em interfaces;
- Métodos marcados com a palavra chave ``default`` DEVEM ter um corpo ``{}``.
- Métodos ``default`` não são ``static``, ``final`` ou ``abstract``;
- Como possuem implementação própria, eles podem ser usados ou sobrescritos por uma classe que implementa a interface;
- São e DEVEM ser ``public``.

Exemplo que compila ✅ 
```java
  public interface MyInterface{ 
    String getName();

    default String getCity(){ //✅ Declaração de um método default, com implementação. 'public' é opcional, pois ja vem adicionado implicitamente.
      return "São Paulo";
    }

    public default String getCountry(){ //✅ Declaração de um método default, com implementação.
      return "Brazil";
    }
  }
```
Exemplos que NÃO compilam ❌ 
```java
  public interface MyInterface{ 
    default String getName(); // ❌ Não compila! Foi declarado como 'default' e não possui implementação. 

    String getCity(){ // ❌ Não compila! Não foi declarado como 'default' e possui implementação. 
      return "São Paulo";
    }

    private default String getCountry(){ // ❌ Não compila! Foi declarado como default, mas o modificador de acesso não pode ser diferente de 'public' 
      return "Brazil";
    }
  }
```
Exemplo de métodos default com herança múltipla:  
```java
  public interface Walk{ 
    public default int getSpeed(){ 
      return 5;
    }
  }
```
```java
  public interface Run{ 
    public default int getSpeed(){ 
      return 10;
    }
  }
```
```java
  //❌ Exemplo de herança que NÃO compila!
  public class Cat implements Walk, Run{  //❌ Não compila! As duas interfaces possuem uma implementação default para o método getSpeed(). Err: Cat inherits unrelated defaults for getSpeed() from types Walk and Run
    public static void main(String... args){
      System.out.println(new Cat().getSpeed());
    }
  }
```
```java
  //✅ Exemplo de herança que compila!
  public class Cat implements Walk, Run{
    
    public int getSpeed(){ // ✅ Compila corretamente, eu sobrescrevi os métodos que conflitam em ambas as interfaces resolvendo o problema.
      return 1;
    }
    
    public static void main(String... args){
      System.out.println(new Cat().getSpeed());
    }
  }
```
#### 4.6.3 Métodos estáticos (``static``)
Também é possível criarmos métodos com implementações, fazendo o uso da palavra ``static``.
São regras para esse uso:
- Métodos ``static`` DEVEM ser públicos (``public``) e DEVEM possuir um corpo ``{}``.
- Não são herdados. Logo, devem sempre ser referenciados usando o nome da interface. Exemplo: ``MyInterface.myStaticMethod();``.

Exemplo:
```java
  interface I{ 
    default int getAge(){
      return 30;
    }

    static String getName(){
      return "my name is I";
    }
  }

  interface I2 extends I{ 
    default void printName(){
      int age = getAge();        // ✅ Consigo fazer uma chamada direta ao método getAge() pois herdei de I, e não é um membro estático.
      String name = getName();   // ❌ Não compila! Método estático não é herdado, portanto não é possível chamá-lo assim.
      String name = I.getName(); // ✅ Maneira correta de chamar um método estático, passando o nome da interface antes.
    }
  }
```
