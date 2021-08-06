# Clases Genéricas

Para ilustrar esta parte, partimos del siguiente código fuente: 

```typescript
type ColorType = 'red' | 'yellow' | 'blue' | 'orange' | 'green';

abstract class TwoDimensionalFigure {
  constructor(private readonly name: string, private color: ColorType) {
  }

  getName() {
    return this.name;
  }
  getColor() {
    return this.color;
  }
  setColor(color: ColorType) {
    this.color = color;
  }

  abstract getArea(): number;
  abstract print(): void;
}

class Rectangle extends TwoDimensionalFigure {
  private readonly sides = 4;

  constructor(name: string, color: ColorType,
    private base: number = 1, private height: number = 1) {
    super(name, color);
  }

  getSides() {
    return this.sides;
  }

  getArea() {
    return this.base * this.height;
  }

  print() {
    console.log(`I am a ${this.getName()}, I have ${this.getSides()} sides, ` +
      `and my area is ${this.getArea()}`);
  }
}

class TwoDimensionalFigureCollection {
  constructor(private figures: TwoDimensionalFigure[]) {
  }

  addFigure(newFigure: TwoDimensionalFigure) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }

  print() {
    this.figures.forEach((figure) => {
      figure.print();
    });
  }
}

const myFigureCollection = new TwoDimensionalFigureCollection([
  new Rectangle('RedRectangle', 'red', 10, 5),
  new Rectangle('YellowRectangle', 'yellow'),
]);

myFigureCollection.print();
```

En este ejemplo definimos la clase abstracta ```TwoDimensionalFigure``` que contiene algunos atributos privados con sus getters y setters, así como los métodos abstractos ```getArea``` y ```print```. De esta clase extiende la clase ```Rectangle```, que define el atributo ```sides```, y tiene acceso a los métodos públicos de la clase a la que extiende y también debe implementar los métodos ```getArea``` y ```print``` obligatoriamente.

Por último se define la clase ```TwoDimensionalFigureCollection``` que permite realizar op. básicas sobre un array de instancias de la clase ```TwoDimensionalFigure```.

Con este primer diseño, podemos añadir a nuestra colecciçon cualquier objeto siempre y cuando sea una instancia de la clase que extiende a la clase abstracta ```TwoDimensionalFigure```.

```typescript
type ColorType = 'red' | 'yellow' | 'blue' | 'orange' | 'green';

abstract class TwoDimensionalFigure {
  constructor(private readonly name: string, private color: ColorType) {
  }

  getName() {
    return this.name;
  }
  getColor() {
    return this.color;
  }
  setColor(color: ColorType) {
    this.color = color;
  }

  abstract getArea(): number;
  abstract print(): void;
}

class Rectangle extends TwoDimensionalFigure {
  private readonly sides = 4;

  constructor(name: string, color: ColorType,
    private base: number = 1, private height: number = 1) {
    super(name, color);
  }

  getSides() {
    return this.sides;
  }

  getArea() {
    return this.base * this.height;
  }

  print() {
    console.log(`I am a ${this.getName()}, I have ${this.getSides()} sides, ` +
      `and my area is ${this.getArea()}`);
  }
}

class Triangle extends TwoDimensionalFigure {
  private readonly sides = 3;

  constructor(name: string, color: ColorType,
    private base: number = 1, private height: number = 1) {
    super(name, color);
  }

  getSides() {
    return this.sides;
  }

  getArea() {
    return this.base * this.height / 2;
  }

  print() {
    console.log(`I am a ${this.getName()}, I have ${this.getSides()} sides, ` +
      `and my area is ${this.getArea()}`);
  }
}

class TwoDimensionalFigureCollection {
  constructor(private figures: TwoDimensionalFigure[]) {
  }

  addFigure(newFigure: TwoDimensionalFigure) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }

  print() {
    this.figures.forEach((figure) => {
      figure.print();
    });
  }
}

const myFigureCollection = new TwoDimensionalFigureCollection([
  new Rectangle('RedRectangle', 'red', 10, 5),
  new Rectangle('YellowRectangle', 'yellow'),
]);

myFigureCollection.addFigure(new Triangle('GreenTriangle', 'green', 25, 3));
myFigureCollection.print();
```

En este segundo ejemplo, hemos definido la clase ```Triangle```, que extiende de la misma clase abstracta.
Supongamos que queremos tener una ```ThreeDimensionalFigureCollection```, cuya funcionalidad es la misma que la de ```TwoDimensionalFigureCollection```. Un primer enfoque sería copiar la clase al completo, pero que el atributo privado ```figures``` en vez de almacenar figuras de dos dimensiones, almacene de tres.

El problema con esto, es que el código no sería mantenible, ni viable, ni elegante, porque cualquier cambio o método que se añada y modifique la funcionalidad de estas clases nos obligará a copiar el código en cada una de las clases.

Otra alternativa sería que el atributo ```figures``` tenga como tipo una unión de tipos, lo cual nos ahorra tener múltiples copias de una misma clase pero haría que la implementación de la clase fuera cada vez más compleja a medida que se aumenten los tipos a almacenar en la union de tipos. Además, podría darse el caso de que quiséramos almacenar una coleccion de un solo tipo exclusivamente y no de una unión de tipos.

Para evitar esto último podríamos hacer uso de una clase genérica. Una clase genérica es una clase, que tiene, al menos, un parámetro de tipo genérico. El concepto es similar al de los templates de C++. De manera que así podemos conseguir definir una clase que lleve a cabo diferentes operaciones sobre un tipo de datos concreto sin conocer, a priori, dicho tipo de datos: 

```typescript
type ColorType = 'red' | 'yellow' | 'blue' | 'orange' | 'green';

abstract class ThreeDimensionalFigure {
  constructor(private readonly name: string, private color: ColorType) {
  }

  getName() {
    return this.name;
  }
  getColor() {
    return this.color;
  }
  setColor(color: ColorType) {
    this.color = color;
  }

  abstract getVolume(): number;
  abstract print(): void;
}

class Cube extends ThreeDimensionalFigure {
  private readonly faces = 6;

  constructor(name: string, color: ColorType, private base: number = 1,
    private height: number = 1, private depth: number = 1) {
    super(name, color);
  }

  getFaces() {
    return this.faces;
  }

  getVolume() {
    return this.base * this.height * this.depth;
  }

  print() {
    console.log(`I am a ${this.getName()}, I have ${this.getFaces()} faces, ` +
      `and my volume is ${this.getVolume()}`);
  }
}

abstract class TwoDimensionalFigure {
  constructor(private readonly name: string, private color: ColorType) {
  }

  getName() {
    return this.name;
  }
  getColor() {
    return this.color;
  }
  setColor(color: ColorType) {
    this.color = color;
  }

  abstract getArea(): number;
  abstract print(): void;
}

class Rectangle extends TwoDimensionalFigure {
  private readonly sides = 4;

  constructor(name: string, color: ColorType,
    private base: number = 1, private height: number = 1) {
    super(name, color);
  }

  getSides() {
    return this.sides;
  }

  getArea() {
    return this.base * this.height;
  }

  print() {
    console.log(`I am a ${this.getName()}, I have ${this.getSides()} sides, ` +
      `and my area is ${this.getArea()}`);
  }
}

class FigureCollection<T> {
  constructor(private figures: T[]) {
  }

  addFigure(newFigure: T) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }

  /* print() {
    this.figures.forEach((figure) => {
      figure.print();
    });
  } */
}

const myTwoDimensionalFigureCollection = new FigureCollection<TwoDimensionalFigure>([
  new Rectangle('RedRectangle', 'red', 10, 5),
  new Rectangle('GreenRectangle', 'green', 5, 30),
]);

const myThreeDimensionalFigureCollection = new FigureCollection<ThreeDimensionalFigure>([
  new Cube('RedCube', 'red', 10, 5, 4),
  new Cube('GreenCube', 'green', 5, 30, 7),
]);

// myFigureCollection.print();

for (let index = 0; index < myTwoDimensionalFigureCollection.getNumberOfFigures(); index++) {
  myTwoDimensionalFigureCollection.getFigure(index).print();
}

for (let index = 0; index < myThreeDimensionalFigureCollection.getNumberOfFigures(); index++) {
  myThreeDimensionalFigureCollection.getFigure(index).print();
}
```

En el ejemplo anterior, podemos ver como la clase ```FigureCollection``` se ha definido con un parámetro de tipo genérico denominado ```T```. Suele llamarse así por convención, pero es simplemente un nombre, se puede poner el que queramos.

El parámetro de tipo genérico ```T``` puede utilizarse en lugar de cualquier tipo específico dentro de la clase genérica como, por ejemplo, en el constructor o en el método ```addFigure```: 

```typescript
constructor(private figures: T[]) {
}

addFigure(newFigure: T) {
  this.figures.push(newFigure);
}
```

El compilador de Typescript es capaz de inferir que el tipo del valor devuelto por el método ```getFigure``` es ```T```.

Para instanciar un objeto de una clase genérica, se debe especificar lo que se conoce como un argumento de tipo genérico, a la hora de invocar al constructor de dicha clase genérica: 

```typescript
const myTwoDimensionalFigureCollection = new FigureCollection<TwoDimensionalFigure>([
  new Rectangle('RedRectangle', 'red', 10, 5),
  new Rectangle('GreenRectangle', 'green', 5, 30),
]);

const myThreeDimensionalFigureCollection = new FigureCollection<ThreeDimensionalFigure>([
  new Cube('RedCube', 'red', 10, 5, 4),
  new Cube('GreenCube', 'green', 5, 30, 7),
]);
```

Si nos fijamos el objeto que creamos es un objeto de la clase genérica ```FigureCollection``` indicando la clase ```TwoDimensionalFigure``` como argumento del parámetro del parámetro de tipo genérico.

# Restricción de los argumentos de tipo genéricos

Por defecto, el compilador de Typescript infiere que el tipo de datos de un parámetro de tipo genérico es ```any```. De este modo, no podremos acceder directamente a ninguna de las propiedades o métodos de algo que es de tipo ```any```.

Un ejemplo de lo anterior ocurre con la definición del método ```print``` de ```FigureCollection```, el cual intenta acceder al método ```print``` de ```figure```, que es de tipo ```T```. Si se descomenta la definición de dicho método, el compilador de Typescript informará de lo siguiente: 

```bash
error TS2339: Property 'print' does not exist on type 'T'.
```

Para solucionar este problema, se pueden restringir los tipos que pueden usarse como argumentos para un parámetro de tipo genérico: 

```typescript
class FigureCollection<T extends (TwoDimensionalFigure | ThreeDimensionalFigure)> {
  constructor(private figures: T[]) {
  }

  addFigure(newFigure: T) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }

  print() {
    this.figures.forEach((figure) => {
      figure.print();
    });
  }
}

const myTwoDimensionalFigureCollection = new FigureCollection<TwoDimensionalFigure>([
  new Rectangle('RedRectangle', 'red', 10, 5),
  new Rectangle('GreenRectangle', 'green', 5, 30),
]);

const myThreeDimensionalFigureCollection = new FigureCollection<ThreeDimensionalFigure>([
  new Cube('RedCube', 'red', 10, 5, 4),
  new Cube('GreenCube', 'green', 5, 30, 7),
]);

myTwoDimensionalFigureCollection.print();
myThreeDimensionalFigureCollection.print();
```

En el ejemplo anterior, gracias a que el método ```print``` existe, tanto en ```TwoDimensionalFigure```, como en ```ThreeDimensionalFigure```, podemos hacer uso de dicho método directamente dentro del cuerpo del método ```print``` de ```FigureCollection```, sin que el compilador de Typescript informe de un error.

Además, ahora, al instanciar un objeto de la clase ```FigureCollection```, solo se pueden usar los tipos ```TwoDimensionalFigure```, ```ThreeDimensionalFigure``` o ```TwoDimensionalFigure | ThreeDimensionalFigure``` como el argumento de tipo genérico: 

```typescript
class FigureCollection<T extends (TwoDimensionalFigure | ThreeDimensionalFigure)> {
  constructor(private figures: T[]) {
  }

  addFigure(newFigure: T) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }

  print() {
    this.figures.forEach((figure) => {
      figure.print();
    });
  }
}

const myTwoDimensionalFigureCollection = new FigureCollection<TwoDimensionalFigure>([
  new Rectangle('RedRectangle', 'red', 10, 5),
  new Rectangle('GreenRectangle', 'green', 5, 30),
]);

const myThreeDimensionalFigureCollection = new FigureCollection<ThreeDimensionalFigure>([
  new Cube('RedCube', 'red', 10, 5, 4),
  new Cube('GreenCube', 'green', 5, 30, 7),
]);

const myWhateverFigureCollection = new FigureCollection<string>([
  "This is a first string",
  "This is a second string",
]);

myTwoDimensionalFigureCollection.print();
myThreeDimensionalFigureCollection.print();
```

Al intentar compilar el ejemplo anterior, el compilador de Typescript informa de lo siguiente: 
```bash
error TS2344: Type 'string' does not satisfy the constraint 'ThreeDimensionalFigure | TwoDimensionalFigure'.
```
 
La palabra reservada ```extends``` permite restringir los tipos que se pueden asignar al parámetro de tipo genérico y, a su vez, el parámetro de tipo genérico permite restringir los tipos con los que se puede utilizar una instancia concreta de la clase genérica. Se crean, por lo tanto, dos niveles de restricción.

# Restricción de los argumentos de tipo genéricos mediante una forma de tipo

El uso de uniones de tipo para restringir los tipos que pueden usarse como argumentos de un parámetro de tipo genérico es una aproximación muy útil, pero la unión debe extenderse con cada nuevo tipo a tener en cuenta. Una solución alternativa es utilizar una forma para restringir el parámetro de tipo genérico:

```typescript
class FigureCollection<T extends { print: () => void }> {
  constructor(private figures: T[]) {
  }

  addFigure(newFigure: T) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }

  print() {
    this.figures.forEach((figure) => {
      figure.print();
    });
  }
}

const myTwoDimensionalFigureCollection = new FigureCollection<TwoDimensionalFigure>([
  new Rectangle('RedRectangle', 'red', 10, 5),
  new Rectangle('GreenRectangle', 'green', 5, 30),
]);

const myThreeDimensionalFigureCollection = new FigureCollection<ThreeDimensionalFigure>([
  new Cube('RedCube', 'red', 10, 5, 4),
  new Cube('GreenCube', 'green', 5, 30, 7),
]);

myTwoDimensionalFigureCollection.print();
myThreeDimensionalFigureCollection.print();
```

La forma que se ha especificado después de la palabra clave ```extends``` a la hora de definir la clase genérica ```FigureCollection``` le indica al compilador de Typescript que dicha clase puede instanciarse haciendo uso de cualquier tupo cuya forma contenga un método denominado ```print``` que no reciba argumentos y cuyo valor de retorno no nos importa (```void```).

# Uso de múltiples parámetros de tipo genéricos

Una clase genérica puede tener varios parámetros de tipo genéricos: 

```typescript
class FigureCollection<T extends TwoDimensionalFigure, U extends ThreeDimensionalFigure> {
  constructor(private figures: (T | U)[]) {
  }

  addFigure(newFigure: T | U) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }

  print() {
    this.figures.forEach((figure) => {
      figure.print();
    });
  }
}

const myFigureCollection =
  new FigureCollection<TwoDimensionalFigure, ThreeDimensionalFigure>([
    new Rectangle('RedRectangle', 'red', 10, 5),
    new Rectangle('GreenRectangle', 'green', 5, 30),
    new Cube('RedCube', 'red', 10, 5, 4),
    new Cube('GreenCube', 'green', 5, 30, 7),
  ]);

myFigureCollection.addFigure(new Rectangle('YellowRectangle', 'yellow', 35, 6));
myFigureCollection.addFigure(new Cube('YellowCube', 'yellow', 35, 6, 7));

for (let index = 0; index < myFigureCollection.getNumberOfFigures(); index++) {
  const myFigure = myFigureCollection.getFigure(index);
  if (myFigure instanceof TwoDimensionalFigure) {
    console.log(myFigure.getArea());
  } else if (myFigure instanceof ThreeDimensionalFigure) {
    console.log(myFigure.getVolume());
  }
}
```

Se puede observar como, ahora, además del parámetro de tipo genérico ```T``` restringido al tipo ```TwoDimensionalFigure```, también se ha definido el parámetro de tipo ```U``` restringido al tipo ```ThreeDimensionalFigure```.

El atributo ```figures``` se ha anotado explícitamente con el tipo ```(T | U)[]```, al igual que el parámetro ```newFigure``` del método ```addFigure```, el cual se ha anotado explícitamente con el tipo ```T | U```: 

```typescript
constructor(private figures: (T | U)[]) {
}

addFigure(newFigure: T | U) {
  this.figures.push(newFigure);
}
```

Además, el compilador de Typescript es capaz de inferir que el tipo de valor retornado por el método ```getFigure``` es ```T | U``` también.

A la hora de instanciar un objeto de la clase ```FigureCollection```, ahora, se deben indicar dos argumentos de tipo genéricos, que en nuestro ejemplo coinciden con ```TwoDimensionalFigure``` y ```ThreeDimensionalFigure```. Es por ello que podemos añadir objetos de la clase ```Rectangle``` (extiende a ```TwoDimensionalFigure```) y ```Cube``` (extiende a ```ThreeDimensionalFigure```) a la colección, mediante el constructor de ```FigureCollection``` o a través del método ```addFigure``` de dicha clase.

Especial cuidado hay que tener a la hora de invocar al método ```getFigure```, que devuelve un valor correspondiente a la unión de tipos especificada y por lo tanto para acceder por ejemplo al método ```getArea``` de ```TwoDimensionalFigure``` necesitaremos deshacer la unión de tipos mediante un guardián de tipos.

Por último, cabe mencionar que cuando un parámetro de tipo genérico se utiliza en un único método de la clase genérica, dicho parámetro de tipo puede moverse de la declaración de la clase a la declaración del método. Lo anterior permite especificar un tipo diferente con cada invocación del método:

```typescript
class FigureCollection {
  constructor(private figures: any[]) {
  }

  addFigure<T extends {print:() => void}>(newFigure: T) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }

  print() {
    this.figures.forEach((figure) => {
      figure.print();
    });
  }
}

const myFigureCollection = new FigureCollection([]);
myFigureCollection.addFigure<TwoDimensionalFigure>(new Rectangle('RedRectangle', 'red', 10, 10));
myFigureCollection.addFigure<ThreeDimensionalFigure>(new Cube('RedCube', 'red', 10, 10, 10));

for (let index = 0; index < myFigureCollection.getNumberOfFigures(); index++) {
  myFigureCollection.getFigure(index).print();
}
```

En el ejemplo anterior, a la hora de invocar al método ```addFigure```, se debe especificar el argumento de tipo concreto. En este caso, la forma del tipo especificado debe contar con un método ```print``` que no reciba argumentos y cuyo valor de retorno no nos importa (```void```).

# Herencia de clases genéricas

Se puede extender una clase genérica. Las subclases pueden lidiar con los parámetros de tipo genéricos de diferentes maneras.

## Añadir nuevas funcionalidades a las subclases 

Para ello, se deben utilizar los mismos parámetros de tipos genéricos de la superclase en la subclase para, simplemente, añadir nuevas funcionalidades en la subclase: 

```typescript
class FigureCollection<T extends TwoDimensionalFigure | ThreeDimensionalFigure> {
  constructor(protected figures: T[]) {
  }

  addFigure(newFigure: T) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }

  print() {
    this.figures.forEach((figure) => {
      figure.print();
    });
  }
}

class SearchableFigureCollection<T extends TwoDimensionalFigure | ThreeDimensionalFigure> extends FigureCollection<T> {
  constructor(figures: T[]) {
    super(figures);
  }

  searchByColor(color: ColorType): T[] | undefined {
    return this.figures.filter((figure) => figure.getColor() === color);
  }
}

const mySearchableFigureCollection = new SearchableFigureCollection([
  new Rectangle('RedRectangle', 'red', 10, 10),
  new Rectangle('BlueRectangle', 'blue', 10, 10),
  new Cube('GreenCube', 'green', 10, 10, 10),
  new Cube('BlueCube', 'blue', 10, 10, 10),
]);

const searchResults = mySearchableFigureCollection.searchByColor('blue');

if (searchResults) {
  searchResults.forEach((result) => {
    result.print();
  });
}
```

Se puede observar como se ha añadido la clase ```SearchableFigureCollection``` que extiende a ```FigureCollection```. En este caso, el parámetro de tipo genérico y sus restricciones son exactamente iguales en el caso de la superclase y subclase genéricas. La funcionalidad añadida en la subclase es la capacidad de buscar objetos de la colección filtrando por el valor de su propiedad color, a la cual se accede a través del método getColor. La funcionalidad añadida viene implementada por el método ```searchByColor``` de la clase ```SearchableFigureCollection```. Cabe mencionar en este punto que, además, se ha modificado el nivel de acceso del atributo figures de ```FigureCollection``` a ```protected``` para poder acceder a dicho atributo directamente desde la subclase ```SearchableFigureCollection```.

Por último, obsérvese que a la hora de instanciar la clase ```SearchableFigureCollection``` no se están utilizando argumentos de tipo genéricos de manera explícita. El compilador de TypeScript permite inferir los argumentos de tipo genéricos de manera implícita a través del propio constructor de la clase genérica. Si se observa el contenido del fichero ```dist/index.d.ts```, la variable ```mySearchableFigureCollection``` tiene el siguiente tipo inferido por el compilador:

```typescript
declare const mySearchableFigureCollection: SearchableFigureCollection<Cube | Rectangle>
```

Para poder instanciar objetos de la clase ```SearchableFigureCollection``` los argumentos de tipo genéricos deben tener una forma compatible con la especificada en la restricción mediante la palabra clave extends. Dicha restricción establece que los argumentos de tipo pueden ser tipos cuya forma sea compatible con la forma de ```TwoDimensionalFigure``` o la forma de vThreeDimensionalFigure``` o la forma de la unión ```TwoDimensionalFigure | ThreeDimensionalFigure```. La forma de la unión de tipos ```Cube | Rectangle``` es totalmente compatible con la forma de la unión ```TwoDimensionalFigure | ThreeDimensionalFigure``` y, es por ello, que en este caso, la inferencia del compilador ha sido correcta, lo cual da lugar a que el programa funcione correctamente.

En cualquier caso, siempre podemos especificar los argumentos de tipo genéricos de manera explícita:

```typescript
const mySearchableFigureCollection = new SearchableFigureCollection<TwoDimensionalFigure | ThreeDimensionalFigure>([
  new Rectangle('RedRectangle', 'red', 10, 10),
  new Rectangle('BlueRectangle', 'blue', 10, 10),
  new Cube('GreenCube', 'green', 10, 10, 10),
  new Cube('BlueCube', 'blue', 10, 10, 10),
]);
```

## Fijar los parámetros de tipos genéricos

Algunas subclases necesitan definir cierta funcionalidad que solo se encuentra disponible utilizando un subconjunto de los tipos que soporta la superclase. En estos casos, una subclase puede utilizar un tipo fijo como argumento del parámetro de tipo genérico definido en la superclase, de modo que la subclase deja de ser una clase genérica:

```typescript
class FigureCollection<T extends TwoDimensionalFigure | ThreeDimensionalFigure> {
  constructor(protected figures: T[]) {
  }

  addFigure(newFigure: T) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }

  print() {
    this.figures.forEach((figure) => {
      figure.print();
    });
  }
}

class SearchableFigureCollection extends FigureCollection<ThreeDimensionalFigure> {
  constructor(figures: ThreeDimensionalFigure[]) {
    super(figures);
  }

  searchByVolume(volume: number) {
    return this.figures.filter((figure) => figure.getVolume() >= volume);
  }
}

const mySearchableFigureCollection = new SearchableFigureCollection([
  new Cube('GreenCube', 'green', 10, 10, 10),
  new Cube('BlueCube', 'blue', 10, 10, 10),
  new Cube('RedCube', 'red', 10, 10, 5),
]);

const searchResults = mySearchableFigureCollection.searchByVolume(1000);

if (searchResults) {
  searchResults.forEach((result) => {
    result.print();
  });
}
```

En este ejemplo, la clase ```SearchableFigureCollection``` ha dejado de ser una clase genérica. Esto se debe a que extiende a ```FigureCollection<ThreeDimensionalFigure>```, fijando el el parámetro de tipo genérico, de modo que ```SearchableFigureCollection``` solo puede tratar con objetos de la clase ```ThreeDimensionalFigure```.

También puede observarse como a la hora de instanciar un objeto de la clase ```SearchableFigureCollection``` ya no se especifica ningún argumento de tipo genérico de manera explícita, a la vez que el compilador de TypeScript infiere que la variable ```mySearchableFigureCollection``` es de tipo ```SearchableFigureCollection```, simplemente.

Por último, se puede observar como se puede invocar a getVolume de ```ThreeDimensionalFigure``` desde dentro del método ```searchByVolume``` de ```SearchableFigureCollection``` sin que el compilador informe de errores.

## Restringir los parámetros de tipos genéricos

Una solución a caballo entre la primera y segunda mostradas con anterioridad es la de restringir los parámetros de tipo genéricos de la superclase al extender la subclase. De este modo, se sigue obteniendo una clase que es genérica después de la extensión pero con algunas restricciones en lo que respecta a los argumentos de tipo genéricos que pueden utilizarse:

```typescript
class FigureCollection<T> {
  constructor(protected figures: T[]) {
  }

  addFigure(newFigure: T) {
    this.figures.push(newFigure);
  }

  getNumberOfFigures() {
    return this.figures.length;
  }

  getFigure(index: number) {
    return this.figures[index];
  }
}

class SearchableFigureCollection<T extends TwoDimensionalFigure | ThreeDimensionalFigure > extends FigureCollection<T> {
  constructor(figures: T[]) {
    super(figures);
  }

  searchByName(name: string) {
    return this.figures.filter((figure) => figure.getName().match(name));
  }
}

const mySearchableFigureCollection = new SearchableFigureCollection<TwoDimensionalFigure | ThreeDimensionalFigure>([
  new Rectangle('GreenRectangle', 'green', 10, 10),
  new Rectangle('BlueRectangle', 'blue', 10, 10),
  new Rectangle('RedRectangle', 'red', 10, 10),
  new Cube('GreenCube', 'green', 10, 10, 10),
  new Cube('BlueCube', 'blue', 10, 10, 10),
  new Cube('RedCube', 'red', 10, 10, 5),
]);

const searchResults = mySearchableFigureCollection.searchByName('Rectangle');

if (searchResults) {
  searchResults.forEach((result) => {
    result.print();
  });
}
```

# Interfaces genéricas

TypeScript permite la definición de interfaces con parámetros de tipo genéricos, por lo que se puede definir la forma que debe tener un objeto sin especificar tipos de datos concretos:

```typescript
interface CollectionInterface<T> {
  addItem(newItem: T): void;
  getItem(index: number): T;
  getNumberOfItems(): number;
}
```

En este ejemplo, se ha declarado una interfaz genérica denominada CollectionInterface con un parámetro de tipo genérico T. Se puede observar como dicho parámetro de tipo se utiliza en la definición de los métodos de la interfaz.

Una interfaz genérica, al igual que ya se ilustró con las interfaces no genéricas, se puede extender. Las opciones para tratar con los parámetros de tipo genéricos en este caso son las mismas que las descritas para tratar con los parámetros de tipo genéricos a la hora de extender una clase genérica:

```typescript
interface CollectionInterface<T> {
  addItem(newItem: T): void;
  getItem(index: number): T;
  getNumberOfItems(): number;
}

interface SearchableCollectionInterface<T extends {name: string}>
  extends CollectionInterface<T> {
  search(name: string): T[] | undefined;
}

interface TwoDimensionalFigureCollectionInterface
  extends CollectionInterface<TwoDimensionalFigure> {
  getSides(): number[];
  getAreas(): number[];
}

interface FigureCollectionInterface<T extends TwoDimensionalFigure |
  ThreeDimensionalFigure> extends CollectionInterface<T> {
  getColors(): ColorType[];
}
```

Cuando una clase implementa una interfaz genérica, debe implementar todas sus métodos y propiedades, pero existen diferentes opciones a la hora de tratar con los parámetros de tipo genericos, las cuales son similares a las descritas en secciones anteriores, cuando se han extendido clases o interfaces genéricas.

La más sencilla consiste en implementar las propiedades y métodos de la interfaz sin modificar el parámetro de tipo genérico. De este modo, se obtiene una clase genérica que implementa la interfaz directamente:

```typescript
interface CollectionInterface<T> {
  addItem(newItem: T): void;
  getItem(index: number): T;
  getNumberOfItems(): number;
}

class Collection<T> implements CollectionInterface<T> {
  constructor(private items: T[]) {
  }

  addItem(newItem: T) {
    this.items.push(newItem);
  }

  getItem(index: number) {
    return this.items[index];
  }

  getNumberOfItems() {
    return this.items.length;
  }
}

const myCollection = new Collection<number>([3, 5, 7]);

for (let index = 0; index < myCollection.getNumberOfItems(); index++) {
  console.log(myCollection.getItem(index));
}
```

La segunda opción consiste en restringir o fijar el parámetro de tipo genérico de la interfaz en la clase que lo implementa. En el primer caso, se obtiene una clase genérica, mientras que en el segundo no, tal y como se ilustra en el ejemplo que sigue:

```typescript
interface CollectionInterface<T> {
  addItem(newItem: T): void;
  getItem(index: number): T;
  getNumberOfItems(): number;
}

class TwoDimensionalFigureCollection implements
  CollectionInterface<TwoDimensionalFigure> {
  constructor(private items: TwoDimensionalFigure[]) {
  }

  addItem(newItem: TwoDimensionalFigure) {
    this.items.push(newItem);
  }

  getItem(index: number) {
    return this.items[index];
  }

  getNumberOfItems() {
    return this.items.length;
  }
}

const myCollection = new TwoDimensionalFigureCollection([
  new Rectangle('RedRectangle', 'red', 10, 5),
  new Rectangle('BlueRectangle', 'blue', 20, 10),
]);

for (let index = 0; index < myCollection.getNumberOfItems(); index++) {
  myCollection.getItem(index).print();
}
```

Finalmente, la tercera opción consiste en definir una clase abstracta que proporcione una implementación parcial de una interfaz. Dicha implementación deberá completarse por las subclases de la clase abstracta:

```typescript
interface CollectionInterface<T> {
  addItem(newItem: T): void;
  getItems(searchTerm: string): T[];
  getNumberOfItems(): number;
}

abstract class FigureCollection<T> implements CollectionInterface<T> {
  constructor(protected items: T[]) {
  }

  addItem(newItem: T) {
    this.items.push(newItem);
  }

  abstract getItems(searchTerm: string): T[];

  getNumberOfItems() {
    return this.items.length;
  }
}

class TwoDimensionalFigureCollection
  extends FigureCollection<TwoDimensionalFigure> {
  constructor(items: TwoDimensionalFigure[]) {
    super(items);
  }

  getItems(searchTerm: string) {
    return this.items.filter((item) => item.getColor() === searchTerm);
  }
}

const myCollection = new TwoDimensionalFigureCollection([
  new Rectangle('RedRectangle', 'red', 10, 5),
  new Rectangle('BlueRectangle', 'blue', 20, 10),
  new Rectangle('BlueRectangle', 'blue', 30, 15),
]);

myCollection.getItems('blue').forEach((item) => {
  item.print();
});
```