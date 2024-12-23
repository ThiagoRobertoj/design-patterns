# Abstract Factory
## Intenção
Abstract Factory é um padrão de projeto criacional que permite produzir famílias de objetos relacionados sem especificar suas classes concretas.
## Problema
Imagine que você está criando um simulador de loja de móveis. Seu código consiste em classes que representam:
1. Uma família de produtos relacionados, como: Cadeira + Sofá + Mesa de Centro.
2. Várias variantes dessa família, como Moderno, Vitoriano.
Você precisa criar objetos de móveis individuais que combinem com outros objetos da mesma família. Por exemplo, um sofá estilo Moderno não combina com cadeiras estilo Vitoriano.
Além disso, você não quer modificar o código existente ao adicionar novos produtos ou famílias de produtos ao programa.
## Solução
A primeira coisa que o padrão Abstract Factory sugere é declarar explicitamente interfaces para cada produto distinto da família de produtos (ex: cadeira, sofá ou mesa de centro). Então você pode fazer todas as variantes dos produtos seguirem essas interfaces. Por exemplo, todas as variantes de cadeira podem implementar a interface Cadeira; todas as variantes de mesa de centro podem implementar a interface MesaDeCentro, e assim por diante.

O próximo passo é declarar a fábrica abstrata—uma interface com uma lista de métodos de criação para todos os produtos que fazem parte da família de produtos (por exemplo criarCadeira, criarSofa e criarMesaDeCentro). Esses métodos devem retornar tipos abstratos de produtos representados pelas interfaces que extraímos previamente:Cadeira,Sofá, MesaDeCentro e assim por diante.

Agora, e o que fazer sobre as variantes de produtos? Para cada variante de uma família de produtos nós criamos uma classe fábrica separada baseada na interface FabricaAbstrata. Uma fábrica é uma classe que retorna produtos de um tipo em particular. Por exemplo, a classe FabricaMobilhaModerna só pode criar objetos CadeiraModerna, SofaModerno, e MesaDeCentroModerno.

O código cliente trabalha com fábricas e produtos por meio de interfaces abstratas, permitindo a troca de fábrica sem modificar o código cliente.

Digamos que o cliente quer que uma fábrica produza uma cadeira. O cliente não precisa estar ciente da classe fábrica, e nem se importa que tipo de cadeira ele receberá. Seja ela um modelo Moderno ou no estilo Vitoriano, o cliente precisa tratar todas as cadeiras da mesma maneira, usando a interface abstrata Cadeira. Com essa abordagem, a única coisa que o cliente sabe sobre a cadeira é que ela implementa o método sentar de alguma maneira. E também, seja qual for a variante da cadeira retornada, ela sempre irá combinar com o tipo de sofá ou mesa de centro produzido pelo mesmo objeto fábrica.

Há mais uma coisa a se clarificar: se o cliente está exposto apenas às interfaces abstratas, o que realmente cria os objetos fábrica então? Geralmente, o programa cria um objeto fábrica concreto no estágio de inicialização. Antes disso, o programa deve selecionar o tipo de fábrica dependendo da configuração ou definições de ambiente.
##  Estrutura
- Produtos Abstratos: Interfaces para produtos distintos relacionados.
- Produtos Concretos: Implementações específicas de produtos abstratos.
- Fábrica Abstrata: Declara métodos para criar produtos abstratos.
- Fábricas Concretas: Implementam métodos de criação para produtos específicos.
- Cliente: Usa fábricas e produtos por meio de interfaces abstratas.
## Pseudocódigo
Este exemplo ilustra como o padrão Abstract Factory pode ser usado para criar elementos UI multiplataforma sem ter que ligar o código do cliente às classes UI concretas, enquanto mantém todos os elementos criados consistentes com um sistema operacional escolhido.

É esperado que os mesmos elementos UI de um aplicativo multiplataforma se comportem de forma semelhante, mas que se pareçam um pouco diferentes nos diferentes sistemas operacionais. Além disso, é seu trabalho garantir que os elementos UI coincidam com o estilo do sistema operacional atual. Você não vai querer que seu programa renderize controles macOS quando é executado no Windows.

A interface da fábrica abstrata declara um conjunto de métodos de criação que o código cliente pode usar para produzir diferentes tipos de elementos de UI que coincidam com o SO particular. Fábricas concretas correspondem a sistemas operacionais específicos e criam os elementos de UI que correspondem com aquele SO em particular.

Funciona assim: quando a aplicação inicia, ela checa o tipo de sistema operacional que está sendo utilizado. A aplicação usa essa informação para criar um objeto fábrica de uma classe que corresponde com o sistema operacional. O resto do código usa essa fábrica para criar elementos UI. Isso previne que elementos errados sejam criados.

Com essa abordagem, o código cliente não depende de classes concretas de fábricas e elementos UI desde que ele trabalhe com esses objetos através de suas interfaces abstratas. Isso também permite que o código do cliente suporte outras fábricas ou elementos UI que você possa adicionar no futuro.

Como resultado, você não precisa modificar o código do cliente cada vez que adicionar uma variação de elementos de UI em sua aplicação. Você só precisa criar uma nova classe fábrica que produza esses elementos e modificar de forma sutil o código de inicialização da aplicação de forma que ele selecione aquela classe quando apropriado.

```
// A interface fábrica abstrata declara um conjunto de métodos
// que retorna diferentes produtos abstratos. Estes produtos são
// chamados uma família e estão relacionados por um tema ou
// conceito de alto nível. Produtos de uma família são
// geralmente capazes de colaborar entre si. Uma família de
// produtos pode ter várias variantes, mas os produtos de uma
// variante são incompatíveis com os produtos de outro variante.
interface GUIFactory is
    method createButton():Button
    method createCheckbox():Checkbox


// As fábricas concretas produzem uma família de produtos que
// pertencem a uma única variante. A fábrica garante que os
// produtos resultantes sejam compatíveis. Assinaturas dos
// métodos fabrica retornam um produto abstrato, enquanto que
// dentro do método um produto concreto é instanciado.
class WinFactory implements GUIFactory is
    method createButton():Button is
        return new WinButton()
    method createCheckbox():Checkbox is
        return new WinCheckbox()

// Cada fábrica concreta tem uma variante de produto
// correspondente.
class MacFactory implements GUIFactory is
    method createButton():Button is
        return new MacButton()
    method createCheckbox():Checkbox is
        return new MacCheckbox()


// Cada produto distinto de uma família de produtos deve ter uma
// interface base. Todas as variantes do produto devem
// implementar essa interface.
interface Button is
    method paint()

// Produtos concretos são criados por fábricas concretas
// correspondentes.
class WinButton implements Button is
    method paint() is
        // Renderiza um botão no estilo Windows.

class MacButton implements Button is
    method paint() is
        // Renderiza um botão no estilo macOS.

// Aqui está a interface base de outro produto. Todos os
// produtos podem interagir entre si, mas a interação apropriada
// só é possível entre produtos da mesma variante concreta.
interface Checkbox is
    method paint()

class WinCheckbox implements Checkbox is
    method paint() is
        // Renderiza uma caixa de seleção estilo Windows.

class MacCheckbox implements Checkbox is
    method paint() is
        // Renderiza uma caixa de seleção no estilo macOS.


// O código cliente trabalha com fábricas e produtos apenas
// através de tipos abstratos: GUIFactory, Button e Checkbox.
// Isso permite que você passe qualquer subclasse fábrica ou de
// produto para o código cliente sem quebrá-lo.
class Application is
    private field factory: GUIFactory
    private field button: Button
    constructor Application(factory: GUIFactory) is
        this.factory = factory
    method createUI() is
        this.button = factory.createButton()
    method paint() is
        button.paint()


// A aplicação seleciona o tipo de fábrica dependendo da atual
// configuração do ambiente e cria o widget no tempo de execução
// (geralmente no estágio de inicialização).
class ApplicationConfigurator is
    method main() is
        config = readApplicationConfigFile()

        if (config.OS == "Windows") then
            factory = new WinFactory()
        else if (config.OS == "Mac") then
            factory = new MacFactory()
        else
            throw new Exception("Error! Unknown operating system.")

        Application app = new Application(factory)
```
## Aplicabilidade
- Use o Abstract Factory quando seu código precisa trabalhar com diversas famílias de produtos relacionados, mas que você não quer depender de classes concretas daqueles produtos-eles podem ser desconhecidos de antemão ou você simplesmente quer permitir uma futura escalabilidade.
-  O Abstract Factory fornece a você uma interface para a criação de objetos de cada classe das famílias de produtos. Desde que seu código crie objetos a partir dessa interface, você não precisará se preocupar em criar uma variante errada de um produto que não coincida com produtos já criados por sua aplicação.
-  Considere implementar o Abstract Factory quando você tem uma classe com um conjunto de métodos fábrica que desfoquem sua responsabilidade principal.
-  Em um programa bem desenvolvido cada classe é responsável por apenas uma coisa. Quando uma classe lida com múltiplos tipos de produto, pode valer a pena extrair seus métodos fábrica em uma classe fábrica solitária ou uma implementação plena do Abstract Factory.

## Prós
- Você pode ter certeza que os produtos que você obtém de uma fábrica são compatíveis entre si.
- Você evita um vínculo forte entre produtos concretos e o código cliente.
- Princípio de responsabilidade única. Você pode extrair o código de criação do produto para um lugar, fazendo o código ser de fácil manutenção.
- Princípio aberto/fechado. Você pode introduzir novas variantes de produtos sem quebrar o código cliente existente.
## Contras
- O código pode tornar-se mais complicado do que deveria ser, uma vez que muitas novas interfaces e classes são introduzidas junto com o padrão.

# Abstract Factory em Python
O Abstract Factory é um padrão de projeto criacional, que resolve o problema de criar famílias inteiras de produtos sem especificar suas classes concretas.

O Abstract Factory define uma interface para criar todos os produtos distintos, mas deixa a criação real do produto para classes fábrica concretas. Cada tipo de fábrica corresponde a uma determinada variedade de produtos.

O código cliente chama os métodos de criação de um objeto fábrica em vez de criar produtos diretamente com uma chamada de construtor (usando operador new). Como uma fábrica corresponde a uma única variante de produto, todos os seus produtos serão compatíveis.

O código cliente trabalha com fábricas e produtos somente através de suas interfaces abstratas. Ele permite que o mesmo código cliente funcione com produtos diferentes. Você apenas cria uma nova classe fábrica concreta e a passa para o código cliente.

- Exemplos de uso: O padrão Abstract Factory é bastante comum no código Python. Muitas frameworks e bibliotecas o utilizam para fornecer uma maneira de estender e personalizar seus componentes padrão.
- Identificação: O padrão é fácil de reconhecer pelos seus métodos, que retornam um objeto fabrica. Em seguida, a fábrica é usada para criar subcomponentes específicos.

## Exemplo conceitual
Este exemplo ilustra a estrutura do padrão de projeto Abstract Factory. Ele se concentra em responder a estas perguntas:
- De quais classes ele consiste?
- Quais papeis essas classes desempenham?
- De que maneira os elementos do padrão estão relacionados?
```
 main.py: Exemplo conceitual
from __future__ import annotations
from abc import ABC, abstractmethod


class AbstractFactory(ABC):
    """
    A interface Fábrica Abstrata declara um conjunto de métodos que retornam diferentes produtos abstratos. Esses produtos são chamados de família e estão relacionados por um tema ou conceito de alto nível. Os produtos de uma mesma família geralmente conseguem colaborar entre si. Uma família de produtos pode ter várias variantes, mas os produtos de uma variante são incompatíveis com os produtos de outra.f
    another.
    """
    @abstractmethod
    def create_product_a(self) -> AbstractProductA:
        pass

    @abstractmethod
    def create_product_b(self) -> AbstractProductB:
        pass


class ConcreteFactory1(AbstractFactory):
    """
    Fábricas Concretas produzem uma família de produtos que pertencem a uma única variante. A fábrica garante que os produtos resultantes sejam compatíveis. Vale notar que as assinaturas dos métodos da Fábrica Concreta retornam um produto abstrato, enquanto, dentro do método, um produto concreto é instanciado.
    """

    def create_product_a(self) -> AbstractProductA:
        return ConcreteProductA1()

    def create_product_b(self) -> AbstractProductB:
        return ConcreteProductB1()


class ConcreteFactory2(AbstractFactory):
    """
    Cada Fábrica Concreta possui uma variante de produto correspondente.
    """

    def create_product_a(self) -> AbstractProductA:
        return ConcreteProductA2()

    def create_product_b(self) -> AbstractProductB:
        return ConcreteProductB2()


class AbstractProductA(ABC):
    """
    Cada produto distinto de uma família de produtos deve ter uma interface base. Todas as variantes do produto devem implementar essa interface.
    """

    @abstractmethod
    def useful_function_a(self) -> str:
        pass


"""
Produtos Concretos são criados pelas Fábricas Concretas correspondentes.
"""


class ConcreteProductA1(AbstractProductA):
    def useful_function_a(self) -> str:
        return "The result of the product A1."


class ConcreteProductA2(AbstractProductA):
    def useful_function_a(self) -> str:
        return "The result of the product A2."


class AbstractProductB(ABC):
    """
    Aqui está a interface base de outro produto. Todos os produtos podem interagir entre si, mas a interação adequada só é possível entre produtos da mesma variante concreta.
    """
    @abstractmethod
    def useful_function_b(self) -> None:
        """
        O Produto B é capaz de realizar suas próprias tarefas...
        """
        pass

    @abstractmethod
    def another_useful_function_b(self, collaborator: AbstractProductA) -> None:
        """
        ...mas ele também pode colaborar com o Produto A.
A Fábrica Abstrata garante que todos os produtos que ela cria sejam da mesma variante e, portanto, compatíveis entre si.

        """
        pass


"""
Produtos Concretos são criados pelas Fábricas Concretas correspondentes.
"""


class ConcreteProductB1(AbstractProductB):
    def useful_function_b(self) -> str:
        return "The result of the product B1."

    """
    A variante, Produto B1, só é capaz de funcionar corretamente com a variante Produto A1. No entanto, ela aceita qualquer instância de AbstractProductA como argumento.

    """

    def another_useful_function_b(self, collaborator: AbstractProductA) -> str:
        result = collaborator.useful_function_a()
        return f"The result of the B1 collaborating with the ({result})"


class ConcreteProductB2(AbstractProductB):
    def useful_function_b(self) -> str:
        return "The result of the product B2."

    def another_useful_function_b(self, collaborator: AbstractProductA):
        """
        A variante, Produto B2, só é capaz de funcionar corretamente com a variante Produto A2. No entanto, ela aceita qualquer instância de AbstractProductA como argumento.

        """
        result = collaborator.useful_function_a()
        return f"The result of the B2 collaborating with the ({result})"


def client_code(factory: AbstractFactory) -> None:
    """
   O código do cliente trabalha com fábricas e produtos apenas através de tipos abstratos: AbstractFactory e AbstractProduct. Isso permite que você passe qualquer subclasse de fábrica ou produto para o código do cliente sem quebrá-lo.

    """
    product_a = factory.create_product_a()
    product_b = factory.create_product_b()

    print(f"{product_b.useful_function_b()}")
    print(f"{product_b.another_useful_function_b(product_a)}", end="")


if __name__ == "__main__":
    """
    O código do cliente pode trabalhar com qualquer classe de fábrica concreta.

    """
    print("Client: Testing client code with the first factory type:")
    client_code(ConcreteFactory1())

    print("\n")

    print("Client: Testing the same client code with the second factory type:")
    client_code(ConcreteFactory2())
```
