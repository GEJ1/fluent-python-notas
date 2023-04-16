# Fluent-Python-Notas
Notas de lectura en español en base al libro "Fluent Python, 2nd edition (O'Reilly 2022)"

#### Los ejemplos de código pueden ser parcial o totalmente basados en [este repositorio que acompaña al libro](https://github.com/fluentpython/example-code-2e)

## Parte 1: Data Structures 

### Capítulo 1: The Python Data Model

* Python es consistente
* El "[Python Data model](https://docs.python.org/es/3/reference/datamodel.html)" es una abstracción que nos permite pensar al lenguaje como un *framework*
* Para llegar a escribir código "Pythónico" podemos hacer uso de los *métodos dunder* (Abreviación de  *double under*, también conocidos como métodos mágicos aunque el autor prefiere no llamarlos de esta manera) que tienen la forma **\_\_nombre\_\_** 
* Estos métodos casi nunca son llamados por nuestros objetos, sino que se llaman internamente el intérprete de Python
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

* Con solo agregar los métodos dunder **\_\_len\_\_** y **\_\_getitem\_\_**, nuestro objeto "gratuitamente" es iterable, se puede indexar y podemos usar el operador **in** para evaluar si contiene un elemento (para este último en algunos casos será necesitario implementar **\_\_contains\_\_**). Básicamente logramos que se comporte como una secuencia.   
