# Fluent-Python-Notas
#### 📚 Notas tomadas en español por [Gustavo Juantorena](https://www.linkedin.com/in/gustavo-juantorena/) durante la lectura del libro "Fluent Python, 2nd edition (O'Reilly 2022)" de Luciano Ramalho.
![image](https://avatars.githubusercontent.com/u/9216311?s=200&v=4)
#### 🦎 Le recomiendo a cualquiera persona que quiera mejorar su conocimiento de Python que adquiera el libro. Pueden comprarlo entrando a [este link](https://www.amazon.com/-/es/gp/aw/d/1492056359/ref=dp_ob_neva_mobile).

#### ⭐ Si te resulta de utilidad este contenido podés darle una estrella al repositorio arriba a la derecha.
#### Si tenés un comentario, ya sea porque algo no se entienda o porque encontraste un error, podés [abrir un issue](https://docs.github.com/es/issues/tracking-your-work-with-issues/creating-an-issue) o un [pull request](https://www.freecodecamp.org/espanol/news/como-hacer-tu-primer-pull-request-en-github/).

#### Los ejemplos de código pueden ser parcial o totalmente basados en [este repositorio que acompaña al libro](https://github.com/fluentpython/example-code-2e).

## Índice
[Parte 1: Data Structures](#parte-1-data-structures)
* [Capítulo 1: The Python Data Model](#capítulo-1-the-python-data-model)
* [Capítulo 2: An array of sequences](#capítulo-2-an-array-of-sequences)
* [Capítulo 3: Dictionaries and sets](#capítulo-3-dictionaries-and-sets)

---

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
    * Funciona con cualquier iterable (incluso *iterators* que no soportan notación de índices ([])).
    * Único requiisito: el iterable tiene que devolver (sigo usando esta palabra como traducción de *yield*) un ítem por variable (a menos que uses el asterisco (\*) para capturar el exceso de elementos).
    * Usos:
        * **Asignación paralela**:
        ```Python
        coordenadas = (34.6037, 58.3816)
        latitutd, longitud = coordenadas
        latitud
        # >> 34.6037
        ```
        * **Usar \* como prefijo de un argumento**
        ```Python
        divmod(20, 8)
        # >> (2, 4)
        t = (20, 8)
        divmod(*t)
        # >> (2, 4)
        ``` 
        * **Quedarse con el exceso de items** 
        ```Python
        vocales = list('aeiou')
        vocales
        # >> ['a', 'e', 'i', 'o', 'u']
        a, e, *resto = vocales
        a
        # >> 'a'
        e
        # >> 'e'
        resto
        # >> ['i', 'o', 'u']
        ```
        * Llamados a función y sequence literals
        ```Python
        # Ejemplo de llamada a función
        def fun(a, b, c, d, *rest):
            return a, b, c, d, rest
        
        fun(*[1, 2], 3, *range(4, 7))
        # >> (1, 2, 3, 4, (5, 6))
        
        # Ejemplo con sequence literals
        {*range(4), 4, *(5, 6, 7)}
        # >> {0, 1, 2, 3, 4, 5, 6, 7}
        ```
        * Unpacking anidado
        ```Python
        metro_areas = [
            ('Tokyo', 'JP', 36.933, (35.689722, 139.691667)),
            ('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
            ('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
            ('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
            ('São Paulo', 'BR', 19.649, (-23.547778, -46.635833)),
        ]

        def main():
            print(f'{"":15} | {"latitude":>9} | {"longitude":>9}')
            for name,_,_, (lat,lon) in metro_areas:
                if lon <= 0:
                    print(f'{name:15} | {lat:9.4f} | {lon:9.4f}')
                        
        main()
       # >>                 |  latitude | longitude
       # >> Mexico City     |   19.4333 |  -99.1333
       # >> New York-Newark |   40.8086 |  -74.0204
       # >> São Paulo       |  -23.5478 |  -46.6358
    ```
* **Pattern matching** con secuencias
    * Es una sección nueva en esta edición del libro (y en Python existen a partir de la versión 3.10)
    * Discute por qué es distinto de un switch/case y sus ventajas sobre usar if/elif
        * Podemos hacer *destructuring* que vendría a ser un unpacking avanzado.
        * Ampliando el ejemplo anterior (no vuelvo a crear la lista):
        Ejemplo 2-10
        ```Python
        def main():
            print(f'{"":15} | {"latitude":>9} | {"longitude":>9}')
            for record in metro_areas:
                match record:
                    case [name, _, _, (lat, lon)] if lon <= 0:  # Podria ser todo () o todo []
                        print(f'{name:15} | {lat:9.4f} | {lon:9.4f}')
        main()
        # >>                 |  latitude | longitude
        # >> Mexico City     |   19.4333 |  -99.1333
        # >> New York-Newark |   40.8086 |  -74.0204
        # >> São Paulo       |  -23.5478 |  -46.6358
        ```
     * Hay sintaxis propia dentro del match/case:
        * El símbolo \_ matchea cualquier item individual en esa posición
        * Se puede agregar información de tipos (ej: str(name), float(lat)). Esto se evalua en tiempo de ejecución.
        * Se puede leer más en los [PEP 634,635 Y 636](https://docs.python.org/3/whatsnew/3.10.html)

* **Slicing**
    * ¿Por qué Slices and Ranges excluyen en último item? Tiene que ver con que los índices arranquen en 0. 
        * 1. Fácil ver el largo final cuando solo se declara la posición del *stop*: range(3) y my_list[:3] tienen el mismo tamaño (3).
        * 2. Computar el largo solo requiere hacer *stop* - *start*
        * 3. Fácil separar listas en dos partes sin superposición:
        ```Python 
        l = [10, 20, 30, 40, 50, 60]
        l[:2]  # split at 2
        # >> [10, 20]
        l[2:]
        # >> [30, 40, 50, 60]
        ```
    * **Slice Objects**
        * Podemos crearlos con `s[start:stop:step]`.
        * Se puede asignar a un Slice.
        * Un uso interesante que remarca el autor es asignar slices a variables para un paso posterior.
        Ejemplo 2-13:
        ```Python
        invoice = """
        0.....6.................................40........52...55........
        1909 Pimoroni PiBrella                      $17.50    3    $52.50
        1489 6mm Tactile Switch x20                  $4.95    2    $9.90
        1510 Panavise Jr. - PV-201                  $28.00    1    $28.00
        1601 PiTFT Mini Kit 320x240                 $34.95    1    $34.95
        """

        SKU = slice(0, 6)
        DESCRIPTION = slice(6, 40)
        UNIT_PRICE = slice(40, 52)
        QUANTITY = slice(52, 55)
        ITEM_TOTAL = slice(55, None)

        line_items = invoice.split('\n')[2:]

        for item in line_items:
            print(item[UNIT_PRICE], item[DESCRIPTION])
            
        # >>    $17.50   imoroni PiBrella                  
        # >>    $4.95    mm Tactile Switch x20             
        # >>    $28.00   anavise Jr. - PV-201              
        # >>    $34.95   iTFT Mini Kit 320x240 
        ```
    * Slicing multidimensional
        * El operador **[]** puede tomar múltiples índices o Slices separados por coma (es lo que usa Numpy por ejemplo, [leer más](https://numpy.org/doc/stable/user/quickstart.html#indexing-slicing-and-iterating))
        * Para evaluar `a[i, j]` Python llama a `a.__get_item__((i, j))` , es decir que recibe una tupla.
    * Existe algo llamado Ellipsis (se usa con `...`) que es reconocido por el intérprete de Python como un token. No se usa mucho en Python base (todas las secuencias son unidimesionales excepto `memoryview`), pero tiene utilidad para librerías externas como Numpy.

* **Creando listas de listas**
    * Se puede hacer con for anidados o con [] * n 
    * Hay que tener cuidado con la segunda forma porque podes estar apuntando a un alias y no a la lista que querias.

* **Augmented assigments**
    * `+=` se implementa con `__iadd__` (in-place addition)
    *  Si no esta implementado Python usa `+`
    *  Para secuencias mutables `+=` modifica al objeto `inplace`, pero para inmutables claramente no se puede hacer.
    
* **`list.sort` Vs. `sorted` de la librería estándar**  
    * Principal diferencia: list.sort modifica el objeto, mientras que sorted crea una nueva lista y la retorna
    * De acá se deprende algo interesante que es general a la API de Python:
        * Si la función modifica `inplace` (o sea produce un cambio en el objeto), entonces devuelve `None` 
    * Las dos tienen los argumentos `reverse` (ordena descendente) y `key` que está buenísimo porque te permite elegir una función que reciba un solo parámetro con la cual decidís como ordenar (ej: Si le pasas `len` evalùa el largo de cada elemento de la lista y ordena en base a eso).
    * Una cosa más sobre `key`, si tenemos una lista con "números" pero que algunos están como `int` y otros como `str` podemos pasarle una de stas dos funciones a `key` para que los interprete a todos como del mismo tipo (ej: `key=int`). 
    * Ojo con el orden que lo hace en base a ASCII (entonces por ej mayúsculas van antes que minúsuculas).


* **Cuando las listas no son la solución**
    * Acá habla de como tendemos a usar listas para todos (me sentí identificado) a pesar de no ser la mejor opción
        * Si la lista solo contiene números mejor usar `array.array` que es tan liviano como un array de C.
            * Acá se pone técnico y explica lo que es un `memoryview`, un "Un tipo de secuencia de memoria compartida que te permite manipular slices de arrays sin copiar bytes".
     * Dice que si vas a hacer muchas operaciones numéricas mejor usar Numpy que es el sostén de todo el stack científico en Python (coincido!)
     *  Finalmente habla de Deques y otros Queues que son estructura de datos optimizadas para para un comportamiento FIFO (*first in first out*), o sea sacar y poner de los extremos, aunque no andan tan bien si tenes que modificar cosas en el medio de la estructura. Se puede importar de `collections`

---

### Capítulo 3: Dictionaries and sets

* **Los diccionarios son una parte fundamental de Python**, no solo porque los usamos cuando programamos sino porque son fundamentales en el funcionamiento interno del lenguaje
    * `__builtins__.__dict__` guarda todos los tipos, objetos y funciones que vienen con la librería estándar.
* Los diccionarios funcionan como [*hash tables*](https://es.wikipedia.org/wiki/Tabla_hash), lo cual los hace muy performantes. En la pàgina de Fluent Python hay [más info sobre la implementación](https://www.fluentpython.com/extra/internals-of-sets-and-dicts/)
* Habla sobr el agregado de los operadores `|` y `|=` a partir de Python 3.9
Ej:
```Python
dict1 = {"a":1, "b":2}
dict2 = {"c":3}
dict1 | dict2 # Hace un 'merge' de los dos dicts creando un nuevo objeto, pero deja a dict1 y a dict2 igual
## >> {'a': 1, 'b': 2, 'c': 3}

dict1 |= dict2 # Hace un 'merge' de los dos dicts pero modifica al primero (dict1) asignándole la uniòn
dict1
## >> {'a': 1, 'b': 2, 'c': 3}
```
* Siguiendo con cosas "nuevas" en Python, se puede hacer Pattern matching con diccionarios.
* `collection.abc` contienen las *abstract base classes (ABCs)* `Mapping` y `MutableMapping` para diccionarios y estructuras similares
    * El autor recomienda usar estas para verificar si es una instancia en lugar de usar `dict`, porque es más amplio. Ej: `isinstance(mi_diccionario, abc.Mapping)`
    
* Entra mucho en detalle sobre qué pasa el insertar o actualizar un valor mutable
    * Dice que lo comùn si querés acceder a un valor `k` que no sabés si está en un diccionario `d`, en lugar de d[k] utilizarìas d.get(k, 'valor_default')
    * Propone dos opciones que considera mejores:
        * 1. Usar `collections.defaultdict`: Cuando lo inicializás le asignás un valor default por si `__getitem__` falla.
        * 2. Crear un objeto que herede de dict (en realidad dice que lo mejor es heredar de `collections.UserDict`) que implemente el método `__missing__` el cual actùa si falla `__getitem__`
        
* Hay variaciones de dict en las que no se detiene mucho como `collections.OrderedDict` (que tiene algunas diferencias aunque desde Python 3.6 los diccionarios comunes mantienen orden), `collections.ChainMap` (sirve para mantener una lista de *mappings* y buscar como si fuera uno) y finalmente `collections.Counter` (útil para contar objetos `hasheables`)
```Python
from collections import Counter
lista1 = ['x','y','z','x','x','x','y', 'z']
Counter(lista1)
# >> Counter({'x': 4, 'y': 2, 'z': 2})
```

* Nombra al módulo `shelve` que sirve para hacer que un objeto persista (guardarlo) en un formato parecido a un diccionario donde los valores son objetos `pickle`.

* Vuelve al tema de por qué es mejor heredar de `collections.UserDict` para crear nuestros mappings mDutables:
    * Si usas `dict` vas a tener que sobreescribir métodos que podrías simplemente heredar de `UserDict` sin problemas.
    
* **Mappings inmutables**  (solo se había hablado de los mutables)
    * Todos los objetos de tipo `mapping` que trae la librerìa estándar son mutables, pero hay situaciones donde podrìamos necesitar de aquellos inmutables.
    * El mòdulo `types` tiene una clase `MappingProxy` que, dado un objeto `mapping` devuelve una instancia `mappingproxy` que puede es de solo lectura pero es un proxy diinámico del mapping (quizás queda más claro con el ejemplo)
    Ej:
    ```Python
    from types import MappingProxyType
    d = {'one': 1} 
    d_proxy = MappingProxyType(d) 
    d_proxy 
    # >> mappingproxy({'one': 1})
    
    d_proxy['one'] # Podemos ver los items en d_proxy
    # >> 1
    
    d_proxy['two'] = 'x' # No podemos hacer asignaciones en d_proxy
    # >> TypeError: 'mappingproxy' object does not support item assignment
    
    d['two'] = 2 # Si modificamos el diccionario original, el cambio se ve reflejado en proxy
    d_proxy 
    # >> mappingproxy({'one': 1, 'two': 2})
    
    d_proxy['two'] 
    # >> 2
    ```
    
* **Dictionary views**
    * Cuando usamos los métodos `.keys()`, `.values()` y `.items()` estamos accediendo a vistas (*views*) que son instancias de las clases `dict.keys()`, `dict.values()` y `dict.items()` respectivamente.
    * Son proyecciones de solo lectura usadas en la implementación de la clase `dict`
    * No las podemos crear o modificar, están para ser usadas por Python internamente.
    
* **Sets**
    * El autor dice que se tienden a usar poco, pero que son muy útiles (coincido)
    * Un uso interesante es el de eliminar duplicados (`list(set(lista_con_duplicados)` devuelve una lista sin duplicados), pero da un truco interesante que es usar un diccionario para además preservar el orden (`listdict.fromkeys(lista_con_duplicados))`)
    * Los elementos dentro de los `sets` tienen que ser *hasheables* (por lo cual no puede haber `sets` dentro de `sets`), pero esto no vale para su versión inmutable los `frozenset`. 
    * Para inicializar un set vacìo estamos obligados a usar `set()` porque `{}` genera un diccionario vacìo.
    * Hay sets comprehensions tal como existen los dict comprehensions.
    * Por su implementaciòn, buscar en elemento en un set es muy eficiente.
    * Operaciones con sets: Usando teorìa de conjuntos se puede operar con los sets para buscar intersecciones, conjunciones, etc. La API es muy rica en este sentido y muchas veces podemos obtener resultados màs eficientes en cantidad de código y tiempo de ejecución respecto a implementaciones con loops.
    
 
    
        



    
