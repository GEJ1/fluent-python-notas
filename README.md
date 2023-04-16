# Fluent-Python-Notas
#### Notas tomadas en español por [Gustavo Juantorena](https://www.linkedin.com/in/gustavo-juantorena/) durante la lectura del libro "Fluent Python, 2nd edition (O'Reilly 2022)" de Luciano Ramalho
![image](https://avatars.githubusercontent.com/u/9216311?s=200&v=4)
#### Le recomiendo a cualquiera persona que quiera mejorar su conocimiento de Python que adquiera el libro. Pueden comprarlo entrando a [este link](amzn.to/3J48u2J)

#### Los ejemplos de código pueden ser parcial o totalmente basados en [este repositorio que acompaña al libro](https://github.com/fluentpython/example-code-2e)

## Parte 1: Data Structures 

### Capítulo 1: The Python Data Model

* Python es consistente
* El "[Python Data model](https://docs.python.org/es/3/reference/datamodel.html)" es una abstracción que nos permite pensar al lenguaje como un *framework*, lo que según el autor hace que luego de aprender estas sea relativamente fácil "intuir" ("informed correct guesses") como se hacen muchas cosas.
* Para llegar a escribir lo que la comunidad llama código "Pythónico", podemos hacer uso de los *métodos dunder* (abreviación de *double under*, también conocidos como métodos mágicos aunque el autor prefiere no llamarlos de esta manera porque cree que de mágicos no tienen nada) los cuales poseen la forma **\_\_nombre\_\_** 
* Estos métodos casi nunca son llamados por nuestros objetos, sino que se llaman internamente el intérprete de Python
* Nosotros usamos len(algo), pero en realidad se está ejecutando algo.\_\_len\_\_\(\)
* Agregándolos a nuestros objetos obtenemos muchas funcionalidad. [Ver ejemplo del mazo de cartas](https://github.com/fluentpython/example-code-2e/blob/master/01-data-model/data-model.ipynb):

```Python
import collections

Card = collections.namedtuple('Card', ['rank', 'suit'])

class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()

    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits for rank in self.ranks]

    def __len__(self):
        return len(self._cards)

    def __getitem__(self, position):
        return self._cards[position]
```

* Con solo agregar los métodos dunder **\_\_len\_\_** y **\_\_getitem\_\_**, nuestro objeto "gratuitamente" es iterable (ej: Usar un for), se puede indexar (ej: Uso de []) y podemos usar el operador **in** para evaluar si contiene un elemento (para este último en algunos casos será necesitario implementar **\_\_contains\_\_**). Básicamente logramos que se comporte como una secuencia.   

* Continuando con los *métodos dunder* habla de emular tipos numéricos y como implementar operadores matemáticos (ej: suma, resta, valor absoluto, etc).
* Da el ejemplo de crear un objeto que sea un [vector en 2D](https://github.com/fluentpython/example-code-2e/blob/28d6d033156831a77b700064997c05a40a83805f/01-data-model/vector2d.py):

```Python
import math

class Vector:

    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

    def __repr__(self):
        return f'Vector({self.x!r}, {self.y!r})'

    def __abs__(self):
        return math.hypot(self.x, self.y)

    def __bool__(self):
        return bool(abs(self))

    def __add__(self, other):
        x = self.x + other.x
        y = self.y + other.y
        return Vector(x, y)

    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)
```
* Acá vemos por ejemplo que con **\_\_add\_\_** podemos usar el operador **+** con nuestros objetos, o que definimos **\_\_bool\_\_** para que nuestro objeto evalúe a **False** si su módulo es 0.
    * También sobre **\_\_bool\_\_**: Si nuestro objeto no tiene implementado **\_\_bool\_\_** o **\_\_len\_\_** nuestra instancia se va a considerar *truthy* (o sea que evalùa a **True**), en caso de que esté **\_\_bool\_\_** tiene prioridad y si solo está **\_\_len\_\_** en caso de devolver 0 se considera *falsy* (evalúa a **False**)   
* También habla de **\_\_repr\_\_** y discute un poco sus diferencias con **\_\_str\_\_** (No parece ser un tema cerrado, ver [esta discusión en Stack Overflow](https://stackoverflow.com/questions/1436703/what-is-the-difference-between-str-and-repr)) 

---
