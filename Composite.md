# Composite
Também conhecido como: Árvore de objetos, Object tree
## Propósito
O Composite é um padrão de projeto estrutural que permite que você componha objetos em estruturas de árvores e então trabalhe com essas estruturas como se elas fossem objetos individuais.
## Problema
Usar o padrão Composite faz sentido apenas quando o modelo central de sua aplicação pode ser representada como uma árvore.

Por exemplo, imagine que você tem dois tipos de objetos: Produtos e Caixas. Uma Caixa pode conter diversos Produtos bem como um número de Caixas menores. Essas Caixas menores também podem ter alguns Produtos ou até mesmo Caixas menores que elas, e assim em diante.

Digamos que você decida criar um sistema de pedidos que usa essas classes. Os pedidos podem conter produtos simples sem qualquer compartimento, bem como caixas recheadas com produtos... e outras caixas. Como você faria para determinar o preço total desse pedido?

Você pode tentar uma solução direta: desempacotar todas as caixas, verificar cada produto e então calcular o total. Isso pode ser viável no mundo real; mas em um programa, não é tão simples como executar uma iteração. Você tem que conhecer as classes dos Produtos e Caixas que você está examinando, o nível de aninhamento das caixas e outros detalhes cabeludos de antemão. Tudo isso torna uma solução direta muito confusa ou até impossível.

## Solução
O padrão Composite sugere que você trabalhe com Produtos e Caixas através de uma interface comum que declara um método para a contagem do preço total.

Como esse método funcionaria? Para um produto, ele simplesmente retornaria o preço dele. Para uma caixa, ele teria que ver cada item que ela contém, perguntar seu preço e então retornar o total para essa caixa. Se um desses itens for uma caixa menor, aquela caixa também deve verificar seu conteúdo e assim em diante, até que o preço de todos os componentes internos sejam calculados. Uma caixa pode até adicionar um custo extra para o preço final, como um preço de embalagem.

O maior benefício dessa abordagem é que você não precisa se preocupar sobre as classes concretas dos objetos que compõem essa árvore. Você não precisa saber se um objeto é um produto simples ou uma caixa sofisticada. Você pode tratar todos eles com a mesma interface. Quando você chama um método os próprios objetos passam o pedido pela árvore.

##  Analogia com o mundo real

Exércitos da maioria dos países estão estruturados como hierarquias. Um exército consiste de diversas divisões; uma divisão é um conjunto de brigadas, e uma brigada consiste de pelotões, que podem ser divididos em esquadrões. Finalmente, um esquadrão é um pequeno grupo de soldados reais. Ordens são dadas do topo da hierarquia e são passadas abaixo para cada nível até cada soldado saber o que precisa ser feito.

##  Estrutura

1. A interface Componente descreve operações que são comuns tanto para elementos simples como para elementos complexos da árvore.
2. A Folha é um elemento básico de uma árvore que não tem sub-elementos.
3. O Contêiner (ou composite) é o elemento que tem sub-elementos: folhas ou outros contêineres. Um contêiner não sabe a classe concreta de seus filhos. Ele trabalha com todos os sub-elementos apenas através da interface componente.
4. O Cliente trabalha com todos os elementos através da interface componente. Como resultado, o cliente pode trabalhar da mesma forma tanto com elementos simples como elementos complexos da árvore.

## Pseudocódigo

```
// A interface componente declara operações comuns para ambos os
// objetos simples e complexos de uma composição.
interface Graphic is
    method move(x, y)
    method draw()

// A classe folha representa objetos finais de uma composição.
// Um objeto folha não pode ter quaisquer sub-objetos.
// Geralmente, são os objetos folha que fazem o verdadeiro
// trabalho, enquanto que os objetos composite somente delegam
// para seus sub componentes.
class Dot implements Graphic is
    field x, y

    constructor Dot(x, y) { ... }

    method move(x, y) is
        this.x += x, this.y += y

    method draw() is
        // Desenhar um ponto em X e Y.

// Todas as classes componente estendem outros componentes.
class Circle extends Dot is
    field radius

    constructor Circle(x, y, radius) { ... }

    method draw() is
        // Desenhar um círculo em X e Y com raio R.

// A classe composite representa componentes complexos que podem
// ter filhos. Objetos composite geralmente delegam o verdadeiro
// trabalho para seus filhos e então "somam" o resultado.
class CompoundGraphic implements Graphic is
    field children: array of Graphic

    // Um objeto composite pode adicionar ou remover outros
    // componentes (tanto simples como complexos) para ou de sua
    // lista de filhos.
    method add(child: Graphic) is
        // Adiciona um filho para o vetor de filhos.

    method remove(child: Graphic) is
        // Remove um filho do vetor de filhos.

    method move(x, y) is
        foreach (child in children) do
            child.move(x, y)

    // Um composite executa sua lógica primária em uma forma
    // particular. Ele percorre recursivamente através de todos
    // seus filhos, coletando e somando seus resultados. Já que
    // os filhos do composite passam essas chamadas para seus
    // próprios filhos e assim em diante, toda a árvore de
    // objetos é percorrida como resultado.
    method draw() is
        // 1. Para cada componente filho:
        //     - Desenhe o componente.
        //     - Atualize o retângulo limitante.
        // 2. Desenhe um retângulo tracejado usando as
        // limitantes.

// O código cliente trabalha com todos os componentes através de
// suas interfaces base. Dessa forma o código cliente pode
// suportar componentes folha simples e composites complexos.
class ImageEditor is
    field all: CompoundGraphic

    method load() is
        all = new CompoundGraphic()
        all.add(new Dot(1, 2))
        all.add(new Circle(5, 3, 10))
        // ...

    // Combina componentes selecionados em um componente
    // composite complexo.
    method groupSelected(components: array of Graphic) is
        group = new CompoundGraphic()
        foreach (component in components) do
            group.add(component)
            all.remove(component)
        all.add(group)
        // Todos os componentes serão desenhados.
        all.draw()
```
## Prós 
-  Você pode trabalhar com estruturas de árvore complexas mais convenientemente: utilize o polimorfismo e a recursão a seu favor.
-  Princípio aberto/fechado. Você pode introduzir novos tipos de elemento na aplicação sem quebrar o código existente, o que agora funciona com a árvore de objetos.

## Contras
 Pode ser difícil providenciar uma interface comum para classes cuja funcionalidade difere muito. Em certos cenários, você precisaria generalizar muito a interface componente, fazendo dela uma interface de difícil compreensão.

# Composite em Python
## Exemplo conceitual

```
from __future__ import annotations
from abc import ABC, abstractmethod
from typing import List


class Component(ABC):
    """
    A classe base Component declara operações comuns tanto para objetos simples quanto para objetos complexos de uma composição.
    """

    @property
    def parent(self) -> Component:
        return self._parent

    @parent.setter
    def parent(self, parent: Component):
        """
        Opcionalmente, o **Component** base pode declarar uma interface para definir e acessar o pai de um componente em uma estrutura de árvore. Ele também pode fornecer uma implementação padrão para esses métodos.
        """

        self._parent = parent

    """
    Em alguns casos, pode ser benéfico definir as operações de gerenciamento de filhos diretamente na classe base **Component**. Dessa forma, você não precisará expor nenhuma classe de componente concreto ao código do cliente, mesmo durante a montagem da árvore de objetos. A desvantagem é que esses métodos ficarão vazios para os componentes do nível **Leaf**.
    """

    def add(self, component: Component) -> None:
        pass

    def remove(self, component: Component) -> None:
        pass

    def is_composite(self) -> bool:
        """
        Você pode fornecer um método que permita ao código do cliente determinar se um componente pode ter filhos.
        """

        return False

    @abstractmethod
    def operation(self) -> str:
        """
        O Component base pode implementar algum comportamento padrão ou deixá-lo para as classes concretas (declarando o método que contém o comportamento como "abstrato").
        """

        pass


class Leaf(Component):
    """
   A classe **Leaf** representa os objetos finais de uma composição. Uma **Leaf** não pode ter nenhum filho.  

   Geralmente, são os objetos **Leaf** que realizam o trabalho real, enquanto os objetos **Composite** apenas delegam para seus subcomponentes.
    """

    def operation(self) -> str:
        return "Leaf"


class Composite(Component):
    """
    A classe Composite representa os componentes complexos que podem ter filhos. Geralmente, os objetos Composite delegam o trabalho real para seus filhos e, em seguida, "somam" os resultados.
    """

    def __init__(self) -> None:
        self._children: List[Component] = []

    """
    Um objeto Composite pode adicionar ou remover outros componentes (tanto simples quanto complexos) à ou da sua lista de filhos.
    """

    def add(self, component: Component) -> None:
        self._children.append(component)
        component.parent = self

    def remove(self, component: Component) -> None:
        self._children.remove(component)
        component.parent = None

    def is_composite(self) -> bool:
        return True

    def operation(self) -> str:
        """
        O **Composite** executa sua lógica principal de uma maneira específica. Ele percorre recursivamente todos os seus filhos, coletando e somando seus resultados. Como os filhos do **Composite** repassam essas chamadas para seus próprios filhos e assim por diante, toda a árvore de objetos é percorrida como resultado.
        """

        results = []
        for child in self._children:
            results.append(child.operation())
        return f"Branch({'+'.join(results)})"


def client_code(component: Component) -> None:
    """
    O código do cliente trabalha com todos os componentes por meio da interface base.
    """

    print(f"RESULT: {component.operation()}", end="")


def client_code2(component1: Component, component2: Component) -> None:
    """
    Graças ao fato de que as operações de gerenciamento de filhos são declaradas na classe base Component, o código do cliente pode trabalhar com qualquer componente, simples ou complexo, sem depender de suas classes concretas.
    """

    if component1.is_composite():
        component1.add(component2)

    print(f"RESULT: {component1.operation()}", end="")


if __name__ == "__main__":
    # This way the client code can support the simple leaf components...
    simple = Leaf()
    print("Client: I've got a simple component:")
    client_code(simple)
    print("\n")

    # ...as well as the complex composites.
    tree = Composite()

    branch1 = Composite()
    branch1.add(Leaf())
    branch1.add(Leaf())

    branch2 = Composite()
    branch2.add(Leaf())

    tree.add(branch1)
    tree.add(branch2)

    print("Client: Now I've got a composite tree:")
    client_code(tree)
    print("\n")

    print("Client: I don't need to check the components classes even when managing the tree:")
    client_code2(tree, simple)
 Output.txt: Resultados da execução
Client: I've got a simple component:
RESULT: Leaf

Client: Now I've got a composite tree:
RESULT: Branch(Branch(Leaf+Leaf)+Branch(Leaf))

Client: I don't need to check the components classes even when managing the tree:
RESULT: Branch(Branch(Leaf+Leaf)+Branch(Leaf)+Leaf)
```

## Referências
https://refactoring.guru/pt-br/design-patterns/composite
https://refactoring.guru/pt-br/design-patterns/composite/python/example
