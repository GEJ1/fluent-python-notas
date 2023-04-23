# Fluent-Python-Notas
#### 📚 Notas tomadas en español por [Gustavo Juantorena](https://www.linkedin.com/in/gustavo-juantorena/) durante la lectura del libro "Fluent Python, 2nd edition (O'Reilly 2022)" de Luciano Ramalho.
![image](https://avatars.githubusercontent.com/u/9216311?s=200&v=4)
#### 🦎 Le recomiendo a cualquiera persona que quiera mejorar su conocimiento de Python que adquiera el libro. Pueden comprarlo entrando a [este link](https://www.amazon.com/-/es/gp/aw/d/1492056359/ref=dp_ob_neva_mobile).

#### ⭐ Si te resulta de utilidad este contenido podés darle una estrella al repositorio arriba a la derecha.
#### Si tenés un comentario, ya sea porque algo no se entienda o porque encontraste un error, podés [abrir un issue](https://docs.github.com/es/issues/tracking-your-work-with-issues/creating-an-issue) o un [pull request](https://www.freecodecamp.org/espanol/news/como-hacer-tu-primer-pull-request-en-github/).

#### Los ejemplos de código pueden ser parcial o totalmente basados en [este repositorio que acompaña al libro](https://github.com/fluentpython/example-code-2e).

## Parte 1: Data Structures 

### Capítulo 1: The Python Data Model

* Python es consistente.
* El "[Python Data model](https://docs.python.org/es/3/reference/datamodel.html)" es una abstracción que nos permite pensar al lenguaje como un *framework*, lo que según el autor hace que luego de aprender estas sea relativamente fácil "intuir" ("informed correct guesses") como se hacen muchas cosas.
* Para llegar a escribir lo que la comunidad llama código "Pythónico", podemos hacer uso de los *métodos dunder* (abreviación de *double under*, también conocidos como métodos mágicos aunque el autor prefiere no llamarlos de esta manera porque cree que de mágicos no tienen nada) los cuales poseen la forma **\_\_nombre\_\_** 
* Estos métodos casi nunca son llamados por nuestros objetos, sino que los llama internamente el intérprete de Python
* Nosotros usamos **len(algo)**, pero en realidad se está ejecutando **algo.\_\_len\_\_\(\)**
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

* Con solo agregar los métodos dunder **\_\_len\_\_** y **\_\_getitem\_\_**, nuestro objeto "gratuitamente" es iterable (ej: Uso de **for**), se puede indexar (ej: Uso de **[]**) y podemos usar el operador **in** para evaluar si contiene un elemento (para este último en algunos casos será necesitario implementar **\_\_contains\_\_**). Básicamente logramos que se comporte como una secuencia.   

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

### Capítulo 2: An array of sequences

* Muchas estructura de datos de tipo secuencias comparten un conjunto comùn de operaciones (ej: iteración, slicing, sorting y concatenación).
* Habla de las **secuencias implementadas en C que vienen en la librería estándar** (*built-in*) y las divide según dos criterios:
* Criterio 1 (por lo que pueden contener):
    * *Container sequences*: 
        * Pueden contener elementos de distintos tipos, incluyendo otros contaners asociados.
        * Guarda referencias a los objetos que contiene. 
        * Ej: `list`, `tuple` y `collections.deque`.
    * *Flat sequences*: 
        * Solo contienen un tipo de dato.
        * Guardan el dato (NO una referencia).
        * Más compactas pero limitadas tipos de datos primitivos. 
        * Ej: `str`, `bytes` y `array.array` 

* Criterio 2 (mutabilidad ([buen recurso simple para entenderlo](https://ellibrodepython.com/mutabilidad-python#mutabilidad))):
    * *Secuencias mutables*: 
        * Permiten ser modificadas una vez creadas. 
        * Heredan todos los métodos de las inmutables y se les agregan otros.
        * Ej: `list`, `bytearray`, `array.array`, `collections.deque`
    * *Secuencias inmutables*: 
        * NO permiten ser modificadas una vez creadas (ojo que esto es un poco tramposo, lo va a retomar)
        * Ej: `tuple`, `str`, `bytes`


* A contnuación cambia de tema para hablar de listas por comprensión y generadores
* **Listas por comprensión** o *list comprehensions* son una manera de popular listas utilizando un `for` en una sola línea
    * Ej:
   ```Python
   lista_diez = [i for i in range(10)]
   
   # Es equivalente a
   lista_diez = []
   for i in range(10):
       lista_diez.append(i)
   ```
   * Advierte que sin bien se recomienda su uso y se considera Pythónico, es posible abusar del mismo por lo cual hay que tener sentido comùn. 
   * Un aspecto interesante también es que pueden remplazar a las funciones `map` y `filter`:
    * Ejemplo 2-3 del libro:
      ```Python 
      
        symbols = '$¢£¥€¤'
        beyond_ascii = [ord(s) for s in symbols if ord(s) > 127]
        
        # Lo mismo pero usando map y filter
        beyond_ascii = list(filter(lambda c: c > 127, map(ord, symbols)))
      ```  
      Coincido con el autor sobre que es más fácil usar las listas por comprensión
      
   * También se pueden hacer for anidados como en el ejemplo del mazo de cartas del cap 1 (recomienda hace el salto de linea en cada for para mejorar la legibilidad, esto no afecta al intérprete)
   ```Python
   [Card(rank, suit) for suit in self.suits 
                     for rank in self.ranks]
   ```
* **Generadores**
    * Te "devuelven" (*yields*) items de uno en uno
    * Esto hace que usen menos memoria que una listcomp (evitan que tengas que crearte una lista entera de una)
    * Se crean con paréntesis () en lugar de corchetes []
    * Los explica en detalle en el cap 17

* Luego entra en detalle sobre las **tuplas**
    * **Tuplas como registros**
        * Si las usamos con este objetivo no solo es importante que sean inmutables, sino que respeten el orden
        * Ejemplo 2-7:
        ```Python
        lax_coordinates = (33.9425, -118.408056)
        city, year, pop, chg, area = ('Tokyo', 2003, 32_450, 0.66, 8014)
        traveler_ids = [('USA', '31195855'), ('BRA', 'CE342567'), ('ESP', 'XDA205856')]

        for passport in sorted(traveler_ids):
            print('%s/%s' % passport)
        ```
     * **Tuplas como listas inmutables**
        * Ventajas:
            * Claridad: Si usas tuplas sabés que su largo no va a cambiar
            * Performance: Menos memoria que una lista y Python puede hacer ciertas optimizaciones (en el libro se dan varios ejemplos).
             
        * Cuidado!
            * Si bien las tuplas son secuencias inmutables, pueden contener secuencias mutables dentro y estos **sí pueden cambiar**
                * Ej:
                ```Python
                a = (10, 'alpha', [1, 2])
                b[-1].append(99)
                # >> (10, 'alpha', [1, 2, 99])
                ```
* **Unpacking** en Secuencias e iterables
    * Principal ventaja del unpacking según el autor es evitar el uso innecesario de índices para extraer elementos de las secuencias.
    * Funciona con cualquier iterable (incluso *iterators* que no soportan notación de índices ([])) 
        
    


   


