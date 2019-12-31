# Oggetti e strutture

Il motivo principale per cui manteniamo le variabili private è perché non vogliamo che qualcun altro conosca i dettagli dei nostri oggetti, ma utilizzi solamente i metodi per manipolare tali dati. Allora perché creiamo variabili private se poi aggiungiamo metodi _getter_ e _setter_ rendendo di fatto le variabili private come se fossere pubbliche?

### Astrazione dei dati

Se creiamo oggetti con delle variabili private, dovremmo mantenere queste variabili private e fornire semplicemente dei metodi astratti che consentono di manipolare tali dati. In questo modo nascondiamo l'implementazione e forniamo solo i metodi che si occupano di manipolare l'essenza dei nostri dati.

Veicolo concreto:

```text
interface Vehicle {
    public function getFuelTankCapacityInGallons(): double;
    public function getGallonsOfGasoline(): double;
}
```

Veicolo astratto:

```text
interface Vehicle {
    public function getPercentFuelRemaining(): double;
}
```

In questi due esempi, il secondo è preferibile. Non vogliamo esporre i dettagli dei nostri dati. Piuttosto vogliamo esprimere i nostri dati in termini astratti.

### Asimmetria dei dati/oggetti

L'esempio sopra mostra la differenza tra **oggetti** e **strutture**. Gli **oggetti** nascondono i loro dati dietro astrazioni ed espongono le funzioni che operano su tali dati. Le **strutture** espongono i loro dati e non hanno alcuna funzione significativa.

Esempio di utilizzo delle strutture:

```text
class Square {
    public $side;
}

class Rectangle {
    public $height;
    public $width;
}

class Circle {
    public $center;
    public $radius;
}

class Geometry {
    private const PI = 3.141592653589793;

    public function area(object $shape): double {
        switch(true) {
            case $shape instanceof Square:
                return pow($shape->side, 2);
            case $shape instanceof Rectangle:
                return $shape->height * $shape->width;
            case $shape instanceof Circle:
                return pow($shape->radius, 2) * self::PI;
            default:
                throw new NoSuchShapeException();
        }
    }
}
```

Esempio di utilizzo degli oggetti:

```text
interface Shape {
    public function area(): double;
}

class Square implements Shape {
    private $side;
    
    public function __construct(double $side) {
        $this->side = $side;
    }

    public function area(): double {
        return pow($this->side, 2);
    }
}

class Rectangle implements Shape {
    private $height;
    private $width;
    
    public function __construct(double $height, double $width) {
        $this->height = $height;
        $this->width = $width;
    }

    public function area(): double {
        return $this->height * $this->width;
    }
}

class Circle implements Shape {
    private const PI = 3.141592653589793;
    private $center;
    private $radius;
    
    public function __construct(double $radius) {
        $this->radius = $radius;
    }

    public function area(): double {
        return pow($this->radius, 2) * self::PI;
    }
}
```

### In sintesi

Da questi due esempi è possibile notare la differenza sostanziale tra oggetti e strutture. Se venisse aggiunta una nuova funzione `perimeter()` alla classe `Geometry`, le classi delle forme non subirebbero nessuna modifica e l'aggiunta della nuova funzione sarebbe piuttosto semplice, ma se venisse aggiunta una nuova forma `Triangle`, dovrei andare a modificare le funzioni esistenti all'interno di `Geometry`. Al contrario, nell'esempio degli oggetti, se venisse aggiunta una nuova forma `Triangle` non devo fare alcuna modifica alle funzioni esistenti, ma se venisse aggiunta una nuova funzione `perimeter()` dovrei andare a modificare tutte le forme! Possiamo quindi dedurre che oggetti e strutture sono l'una l'opposto dell'altra. Le **strutture** facilitano l'aggiunta di nuove funzioni senza modificare l'assetto esistente, ma qualora venisse aggiunta una nuova struttura, dovrò modificare tutte le funzioni esistenti. Gli **oggetti**, al contrario, facilitano l'aggiunta di nuove classi senza modificare le funzioni esistenti, ma complicano l'aggiunta di nuove funzioni, perchè tutte le classi devono cambiare.

### La legge di Demetra

La _legge di Demetra_ dice che un modulo non dovrebbe conoscere i dettagli degli oggetti che manipola. Significa che un oggetto non dovrebbe mostrare la propria struttura interna tramite i metodi di accesso, perché ciò vorrebbe dire esporre, e non nascondere, la propria struttura interna. Nello specifico la legge di Demetra dice che un metodo _f_ di una classe _C_ dovrebbe richiamare solo i metodi di:

* C
* un oggetto creato da f
* un oggetto passato come argomento a f
* un oggetto contenuto in una variabile di istanza di C

Un esempio di violazione di questa legge è dato dal seguente codice:

```text
$outputDir = $ctxt->getOptions()->getScratchDir()->getAbsolutePath();
```

### Relitti ferroviari

Questo genere di codice viene chiamato "relitto ferroviario" perché ha l'aspetto di un ammasso di coppie di vagoni e generalmente va evitato. Ritornando a noi, il codice sopra riportato, viola la legge di Demetra? Ciò dipende dal fatto che _ctxt_, _options_, e _scratchDir_ siano oggetti o strutture. Se sono oggetti, allora la loro struttura interna dovrebbe essere nascosta, pertanto viola la legge di Demetra. Se sono strutture, e quindi non hanno funzioni che fanno qualcosa di significativo, è normale che espongono i propri dati interni, pertanto **non** viola la legge di Demetra. L'utilizzo dei metodi di accesso confonde la situazione.

### Ibridi

Questo talvolta porta ad avere degli ibridi: per metà oggetti e per metà strutture. Hanno funzioni che fanno qualcosa di significativo, ma hanno anche _getter_ e _setter_ che espongono le variabili private.

### Nascondere la struttura

E se _ctxt_, _options_ e _scratchDir_ fossero oggetti? Come faccio a ricavarmi il percorso assoluto di scratch? Se _ctxt_ è un oggetto, dovremmo chiedergli di **fare qualcosa** e non chiedergli i suoi dettagli interni. Continuando con il codice vediamo a cosa serve _outputDir_:

```text
$outFilename = $outputDir . '/' . str_replace('.', '/', $className) . '.class';
$file = fopen($outFilename, 'w');
```

Vediamo quindi come _outputDir_ serve per creare un file, ma allora perché non farlo creare direttamente a _ctxt_?

```text
$file = $ctxt->createScratchFile($classFilename);
```

Ciò consente a _ctxt_ di nascondere la propria struttura interna ed evita la violazione della legge di Demetra.

### Data Transfer Object \(DTO\)

Una struttura è una classe con variabili pubbliche \(o private, ma manipolate tramite _getter_ e _setter_\) e senza funzioni, un cosiddetto **Data Transfer Object** o **DTO**. I DTO sono utili soprattutto per comunicare con i database. Servono per trasformare il dato "grezzo" di una tabella in oggetti dell'applicazione.

```text
class Address {
    private $state;
    private $province;
    private $city;
    private $address;
    private $zip;

    public function __construct(
        string $state,
        string $province,
        string $city,
        string $address,
        string $zip
    ) {
        $this->state = $state;
        $this->province = $province;
        $this->city = $city;
        $this->address = $address;
        $this->zip = $zip;
    }

    public function getState(): string {
        return $this->state;
    }

    public function getProvince(): string {
        return $this->province;
    }

    public function getCity(): string {
        return $this->city;
    }

    public function getAddress(): string {
        return $this->address;
    }

    public function getZip(): string {
        return $this->zip;
    }
}
```

### Active Record

Gli **Active Record** sono delle forme particolari di DTO. Sono simili ai DTO, ma sono dotati di metodi di **navigazione** come _save_ e _find_. Sfortunatamente spesso si tende a trattare queste strutture come se fossero oggetti, inserendovi anche metodi di manipolazione. Evitatelo! Piuttosto create degli oggetti appositi che contengono le regole operative e che nascondono i propri dati interni.

