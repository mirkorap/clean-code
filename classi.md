# Classi

L'attenzione che dedichiamo nella pulizia delle nostre funzioni e dei nostri blocchi di codice deve essere altrettanto dedicata anche nell'organizzazione delle nostre classi.

### Organizzazione delle classi

Le classi dovrebbero seguire la struttura seguente:

```text
class NomeClasse {
    // Elenco costanti pubbliche
    public const MY_PUBLIC_CONST = 'value';
    
    // Elenco costanti protette
    protected const MY_PROTECTED_CONST = 'value';
    
    // Elenco costanti private
    private const MY_PRIVATE_CONST = 'value';
    
    // Elenco proprietà pubbliche
    public $myPublicProperty;
    
    // Elenco proprietà protette
    protected $myProtectedProperty;
    
    // Elenco proprietà private
    private $myPrivateProperty;
    
    // Costruttore
    public function __construct() {
        // ...
    }
    
    // Elenco dei metodi seguendo la "regola dei passi"
}
```

### Le classi dovrebbero essere piccole!

Cosa si intende per classe "piccola"? Con le funzioni abbiamo dato una definizione semplicemente contanto le righe di codice, con le classi invece dobbiamo usare un'altra metrica, ovvero la **responsabilità**. Una classe che presenta 70 metodi è senza dubbio una classe eccessivamente grande, ma la classe seguente secondo voi è piccola o grande?

```text
class SuperDashboard {
    public function getLastFocusedComponent(): Component {
        // ...
    }
    
    public function setLastFocusedComponent(Component $component): void {
        // ...
    }
    
    public function getMajorVersionNumber(): int {
        // ...
    }
    
    public function getMinorVersionNumber(): int {
        // ...
    }
    
    public function getBuildNumber(): int {
        // ...
    }
}
```

Nonostante abbia solo 5 metodi questa classe è troppo grande, perché ha troppe responsabilità. Il nome di una classe dovrebbe già descrivere le sue responsabilità. Se non riusciamo a dare un nome conciso per una classe, significa che essa è troppo grande. Per esempio i nomi che includono parole come `Processor` o `Manager` spesso nascondono un eccesso di responsabilità. Dovremmo essere in grado di scrivere una breve descrizione della classe usando al massimo 25 parole, senza mai usare "se", "e", "o" e "ma". Nel caso della classe `SuperDashboard` diremmo:

> Le classe SuperDashboard fornisce l'accesso al componente che per ultimo aveva il focus **e** ci consente anche di risalire al numero di versione e di build.

La prima "e" già ci fa capire che questa classe ha troppe responsabilità.

### Il principio SRP \(Single Responsibility Principle\)

Tale principio stabilisce che una classe dovrebbe avere un solo motivo per dover cambiare. La classe `SuperDashboard` ha due motivi per dover cambiare:

1. Quando aggiorniamo la versione del software
2. Quando dobbiamo gestire diversamente il componente focussato

Possiamo quindi estrarre da `SuperDashboard` un'altra classe che si occupa di gestire la sola versione del software.

```text
class Version {
    public function getMajorVersionNumber(): int {
        // ...
    }
    
    public function getMinorVersionNumber(): int {
        // ...
    }
    
    public function getBuildNumber(): int {
        // ...
    }
}
```

Il principio SRP è tra i più semplici dei principi [S.O.L.I.D](https://it.wikipedia.org/wiki/SOLID), ma anche il più violato. Questo perché ci concentriamo più sul "far funzionare" il software rispetto a "rendere pulito" il software. Entrambi sono degli aspetti importanti in egual misura. Un altro motivo che ci spinge a non seguire questo principio è perché temiamo che la presenza di tante piccole classi mono-scopo complichi la comprensione del software. Tuttavia usare un sistema con tante piccole classi non ha più meccanismi rispetto ad un sistema con poche grandi classi. Al contrario, un sistema con poche grandi classi ci costringerà a vedere parti di codice che fondamentalmente nemmeno ci interessano in quel momento.

### Coesione

Le classi dovrebbero avere un piccolo numero di variabili di istanza. Ognuno dei metodi di una classe dovrebbe manipolare una o più di queste variabili. In generale più variabili vengono manipolate da un metodo e maggiore è la **coesione** fra tale metodo e la sua classe. Dobbiamo puntare ad avere una elevata coesione tra la classe e i suoi metodi. La strategia che punta ad avere funzioni piccole con pochi parametri, porta a una proliferazione di variabili di istanza che vengono utilizzate solo da un sottoinsieme di metodi. Questo va a discapito della coesione. Quando si verifica ciò, significa quasi sempre che è necessario dividere la classe in due o più classi.

### Curando la coesione si generano tante piccole classi

Il fatto di suddividere le funzioni più grandi in funzioni più piccole genera una proliferazione di classi. Immaginate di avere una funzione grande con dichiarate all'interno tante variabili locali, questa funzione va indubbiamente spezzata in funzioni più piccole le quali necessitano di condividere queste variabili dichiarate localmente. Cosa facciamo? Passiamo queste variabili come argomento? Assolutamente no! Possiamo allora dichiariararle come variabili di istanza. Ben presto questo porterà ad un accumulo di variabili di istanza utilizzate solo da un sottoinsieme di metodi. E' arrivato quindi il momento di suddividere la classe in due o più classi, in modo tale da aumentare la coesione tra i metodi, la classe e le sue variabili di istanza. Vedete quindi come dalla rifattorizzazione di un metodo, siamo passati alla rifattorizzazione dell'intera classe.

### Organizzare gli interventi di modifica

I software sono soggetti a continui cambiamenti e il rischio che un cambiamento possa introdurre dei bug è sempre alto. Immaginate di avere una classe `Sql` che si occupi di generare stringhe SQL.

```text
class Sql {
    public function __construct(string $table, array $columns) { ... }
    public function create(): string { ... }
    public function insert(array $fields): string { ... }
    public function select(): string { ... }
    public function findByKey(string $key, string $value): string { ... }
}
```

Immaginate che in futuro volessimo aggiungere anche il metodo per gestire l'istruzione `update`. Dovremmo "aprire" la classe e modificarla. Questo però va contro un altro principio [S.O.L.I.D](https://it.wikipedia.org/wiki/SOLID), ovvero l'Open-Closed Principle \(OCP\). Il quale ci dice che le classi dovrebbero essere aperte alle estensioni, ma chiuse alle modifiche. Per aderire a questo principio dobbiamo rifattorizzare la classe in questa maniera

```text
abstract class Sql {
    public function __construct(string $table, array $columns) { ... }
    abstract public function generate(): string;
}

class CreateSql extends Sql {
    public function generate(): string { ... }
}

class InsertSql extends Sql {
    public function __construct(string $table, array $columns, array $fields) { ... }
    public function generate(): string { ... }
}

class SelectSql extends Sql {
    public function generate(): string { ... }
}

class FindByKeySql extends Sql {
    public function __construct(string $table, array $columns, string $key, string $value) { ... }
    public function generate(): string { ... }
}
```

In questa maniera il codice non solo diventa molto più semplice da comprendere e da testare, ma aderisce perfettamente al principio OCP. Se domani avessimo la necessità di gestire l'istruzione `update` basterà creare la classe `UpdateSql`. 

Aggiungere delle nuove funzionalità in un codice così strutturato diventa più semplice e ci toglie la paura di introdurre bug nelle altre parti del sistema.

### Isolamento delle modifiche

In OOP sappiamo che le classi concrete contengono i dettagli implementativi, mentre le classi astratte rappresentano solo i concetti. Una classe client dovrebbe dipendere dalle astrazioni e **non** dai dettagli concreti. Questo perché se tali dettagli dovessero cambiare, la classe client subirebbe dei "rischi" portati da questi cambiamenti. Dobbiamo cercare di isolare e disaccoppiare il nostro codice, in questo modo i cambiamenti apportati all'interno di una classe, non influiscono sul comportamento delle altre classi. Le nostre classi obbediranno così ad un altro principio [S.O.L.I.D](https://it.wikipedia.org/wiki/SOLID), ovvero il Dependency Inversion Principle \(DIP\). Il quale ci dice che le classi dovrebbero dipendere da astrazioni e non da dettagli concreti.

```text
interface StockExchange {
    public function getCurrentPrice(string $symbol): Money;
}

class MilanStockExchange {
    public function getCurrentPrice(string $symbol): Money { ... }
}

class Portfolio {
    private $exchange;
    
    public function __construct(StockExchange $exchange) {
        $this->exchange = $exchange;
    }
    
    // ...
}
```

