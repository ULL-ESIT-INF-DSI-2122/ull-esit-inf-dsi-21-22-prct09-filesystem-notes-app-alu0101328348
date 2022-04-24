# Práctica 9 - Aplicación de procesamiento de notas de texto
### [Git Pages](https://ull-esit-inf-dsi-2122.github.io/ull-esit-inf-dsi-21-22-prct09-filesystem-notes-app-alu0101328348/).

## Índice
- [Introducción](#id1)
- [Clases](#id2)
  - [Clase Note](#id3)
  - [Clase User](#id4)
- [Programa principal](#id5)
- [Funcionamiento](#id6)
- [Referencias](#id7)


## Introducción <a name="id1"></a>
En esta práctica se implementará una aplicación de procesamiento de notas de texto, en el cual se podrá añadir, modificar, eliminar, listar y leer notas del usuario. Las notas se almacenarán en ficheros JSON. Para las funcionalidades de la aplicación de procesamiento de notas de texto para trabajar con el sistema de ficheros, se usará la API síncrona proporcionada por [Node.js](https://nodejs.org/dist/latest-v18.x/docs/api/fs.html#synchronous-api).

Para interactuar con la aplicación a través de las líneas de comandos, utlizaremos los módulos [yars](https://www.npmjs.com/package/yargs). Así mismo, para mostrar los mensaje informativos de un color, trabajaremos con el paquete [chalk](https://www.npmjs.com/package/chalk).



## Clases <a name="id2"></a>
### Clase Note <a name="id3"></a>
Se define la clase Note, definida en el fichero [note.ts](https://github.com/ULL-ESIT-INF-DSI-2122/ull-esit-inf-dsi-21-22-prct09-filesystem-notes-app-alu0101328348/blob/14da4f012d91938c374a8710342439ddb0c42306/src/note.ts), para almacenar la información del título de la nota, el contenido y el color.

A continuación se muestra el código desarrollado del constructor de la clase.
```typescript
constructor(private name: string, private color: string, private body: string) {}
```

En la clase **Note** se ha implementado los siguientes métodos:

* _getName_: Retorna el título de la nota.
  ```typescript
  public getName(): string {
      return this.name;
  }
  ```
* _getColor_: Retorna el color.
  ```typescript
  public getColor(): string {
    return this.color;
  }
  ```
* _getBody_: Devuelve el contenido.
  ```typescript
  public getBody(): string {
    return this.body;
  }
  ```

### Clase User <a name="id4"></a>
En la clase User, definida en el fichero [user.ts](https://github.com/ULL-ESIT-INF-DSI-2122/ull-esit-inf-dsi-21-22-prct09-filesystem-notes-app-alu0101328348/blob/14da4f012d91938c374a8710342439ddb0c42306/src/user.ts), se gestionará la API síncrona de Note.js y contendrá el nombre del usuario.

A continuación se muestra el código desarrollado del constructor de la clase.
```typescript
constructor(private name: string) {}
```

En la clase **User** se ha implementado los métodos:

* _addNote_: Permite añadir una nota ``.json`` al directorio con el nombre correspodiente del usuario.
  ```typescript
    public addNote(newNote: Note) {
      let path: string = './src/notes/' + this.name;
      let file: string = '/' + newNote.getName() + '.json';
      // comprobamos que no existe el directorio del usuario
      if (!fs.existsSync(path)) {
        // creamos un directorio
        fs.mkdirSync(path, {recursive: true});
      }
      const data = {"title": newNote.getName(), "body": newNote.getBody(), "color": newNote.getColor()};
      // comprobamos que el fichero no existe
      if (!fs.existsSync(path + file)) {
        console.log(chalk.green('El fichero se ha creado'));
        fs.writeFileSync(path + file, JSON.stringify(data));
      } else {
        console.log(chalk.red('Error: el fichero ya existe'));
      }
    }
  ```
  Antes de añadir la nota se comprueba que el directorio del usuario no existe, por lo que se creará una nueva carpeta. Para ello, utilizaremos la función ``mkdirSync``.
  Después, se comprueba que el título de la nota no exista en el directorio del usuario y se escribe la información con ``writeFileSync``. En dicha función, convertimos el objeto _data_ en una cadena de texto JSON con [stringify()](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). En caso de que el fichero exista en la carpeta del usuario, se informará un error.

* _modifyNote_: Permite modificar el contenido o color de una nota.
  ```typescript
    public modifyNote(noteToModify: Note) {
      let path: string = './src/notes/' + this.name;
      let file: string = '/' + noteToModify.getName() + '.json';
      if (fs.existsSync(path)) {
        if (fs.existsSync(path + file)) {
          const data = {"title": noteToModify.getName(), "body": noteToModify.getBody(), "color": noteToModify.getColor()};
          fs.writeFileSync(path + file, JSON.stringify(data));
          console.log(chalk.green("El fichero se ha modificado"));
        } else {
          console.log(chalk.red('Error: el fichero no existe'));
        }
      } else {
        console.log(chalk.red("Error: el directorio no existe"));
      }
    }
  ```
  Antes de modificar la nota, comprobamos que el directorio del usuario existe, luego comprobamos que el fichero existe. Luego, utilizamos la función `writeFileSync` para escribir el contenido de la nueva nota al fichero .json que se desea modificar.


* _deleteNote_: Permite eliminar una nota pasando por parámetro su nombre.
  ```typescript
    public deleteNote(titleNote: string) {
      let path: string = './src/notes/' + this.name;
      let file: string = '/' + titleNote + '.json';
      if (fs.existsSync(path)) {
        if (fs.existsSync(path + file)) {
          fs.rmSync(path + file);
          console.log(chalk.green("Nota eliminada!"));
        } else {
          console.log(chalk.red('Error: el fichero no existe'));
        }
      } else {
        console.log(chalk.red("Error: el directorio no existe"));
      }
    }
  ```
  Comprobamos que la carpeta del usuario existe y también el fichero. Luego, para eliminar el fichero, utilizamos `rmSync`.


* _readNote_: Permite leer una nota del directorio del usuario y muestra por consola el título y contenido de la nota con el color correspondiente.
  ```typescript
    public readNote(titleNote: string) {
      let path: string = './src/notes/' + this.name;
      let file: string = '/' + titleNote + '.json';
      if (fs.existsSync(path)) {
        if (fs.existsSync(path + file)) {
          const data = JSON.parse(fs.readFileSync(path + file).toString());
          this.printPartOfNoteByColor(data.title, data.color);
          this.printPartOfNoteByColor(data.body, data.color);
          console.log(chalk.green("Nota leida!"));
        } else {
          console.log(chalk.red('Error: el fichero no existe'));
        }
      } else {
        console.log(chalk.red("Error: El directorio no existe"));
      }
    }
  ```
  Comprobamos que la carpeta del usuario existe y también el fichero. Luego, utilizamos la función [parse()](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) para analizar la cadena de texto leida en JSON. Finalmente, utilizamos la función _printPartOfNoteByColor()_.

* _listNotes_: Permite listar los títulos de las notas del directorio del usuario.
  ```typescript
    public listNotes() {
      const path: string = './src/notes/' + this.name;
      if (fs.existsSync(path)) {
        console.log(chalk.white("Tus notas:"));
        let ficheros = fs.readdirSync(path);
        ficheros.forEach((file) => {
          let readFile = fs.readFileSync(path + '/' + file);
          let jsonFichero = JSON.parse(readFile.toString());
          this.printPartOfNoteByColor(jsonFichero.title, jsonFichero.color);
        });
      } else {
        console.log(chalk.red("No existe del directorio"));
      }
    }
  ```
  Comprobamos que el directorio del usuario existe. Luego, con la función `readdirSync` leemos el contenido de dicha carpeta. Iteramos en la información y leemos los ficheros con `readFileSync`. Después, utilizamos _parse_ y, finalmente, imprimos con _printPartOfNoteByColor_ los títulos de las notas por consola con el color correspondiente.

* _printPartOfNoteByColor_: Permite imprimir el contenido string al color de la nota.
  ```typescript
    public printPartOfNoteByColor(titleNote: string, colorNote: string) {
      switch (colorNote) {
        case 'blue':
          console.log(chalk.blue.italic(titleNote));
          break;
        case 'yellow':
          console.log(chalk.yellow.italic(titleNote));
          break;
        case 'red':
          console.log(chalk.red.italic(titleNote));
          break;
        case 'green':
          console.log(chalk.green.italic(titleNote));
          break;
      }
    }
  ```

* _checkColor_: Permite comprobar que el color de la nota corresponde a los de la lista de colores.
  ```typescript
    public checkColor(color: string): boolean {
      let colorList: string[] = ['red', 'green', 'blue', 'yellow'];
      for (let i = 0; i < colorList.length; i++) {
        if (color == colorList[i]) {
          return true;
        }
      }
      return false;
    }
  ```


## Programa principal <a name="id5"></a>
El programa principal se define en el fichero [appNote.ts](https://github.com/ULL-ESIT-INF-DSI-2122/ull-esit-inf-dsi-21-22-prct09-filesystem-notes-app-alu0101328348/blob/14da4f012d91938c374a8710342439ddb0c42306/src/appNote.ts) donde se invocará las funciones de Note.ts y se utilizará el módulo yargs.
El siguiente ejemplo muestra la configuración de un comando:
```typescript
yargs.command({
  command: 'add',
  describe: 'Add a new note',
  builder: {
    user: {
      describe: 'User name',
      demandOption: true,
      type: 'string',
    },
    title: {
      describe: 'Note title',
      demandOption: true,
      type: 'string',
    },
    body: {
      describe: 'Content of the note',
      demandOption: true,
      type: 'string',
    },
    color: {
      describe: 'Color used to print the note',
      demandOption: true,
      type: 'string',
    },
  },
  handler(argv) {
    if (typeof argv.title === 'string' && typeof argv.user === 'string' && typeof argv.body === 'string' && typeof argv.color === 'string') {
      const nota = new Note(argv.title, argv.color, argv.body);
      const user = new User(argv.user);
      if (user.checkColor(argv.color)) {
        user.addNote(nota);
      } else {
        console.log(chalk.red("Color inválido"));
      }
    } else {
      console.log(chalk.red("Error: Argumentos inválidos"));
    }
  },
});
```
Como se puede observar, se especifica:
* *_command_*: Nombre del comando.
* *_describe_*: Descripción del comando.
* *_build_*: Argumentos del comando. En él se especifica una breve descripción, se indica que son argumentos obligatorios y el tipo de argumento.
* *_handler_*: El manejador del comando que recibe como parámetro un objeto argv. Dentro del manejador creamos los objetos nota y usuario, luego comprobamos que el color introducido es válido e invocamos a _addNote_.

Además, para procesar los argumentos pasados de la línea de comandos a la aplicación, incluiremos `yargs.parse()`.
## Funcionamiento <a name="id6"></a>
A continuación, se muestra un gif del funcionamiento de la aplicación.

![funcionamiento2](https://user-images.githubusercontent.com/72209810/164998703-0281dff1-10a4-4673-8651-b1cd774bba86.gif)


## Referencias <a name="id7"></a>
- [API Node.js](https://nodejs.org/dist/latest-v18.x/docs/api/fs.html#synchronous-api)
- [Yars](https://www.npmjs.com/package/yargs)
- [Chalk](https://www.npmjs.com/package/chalk)
- [Enunciado](https://ull-esit-inf-dsi-2122.github.io/prct09-filesystem-notes-app/)
