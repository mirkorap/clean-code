# Formattazione

La formattazione del codice riveste una grande importanza. Le funzionalità che implementiamo sono soggette a continue modifiche nel corso del tempo. Avere un codice chiaro e leggibile, ci aiuterà ad applicare queste modifiche più facilmente.

### Formattazione verticale

Per formattazione verticale si intendono tutte quelle regole che vengono applicate per rendere chiara la lettura del codice dall'alto verso il basso.

### Quanto può essere lungo un file

In genere un file di codice sorgente non deve superare le 500 righe. Mediamente i file devono attestarsi sulle 200 / 300 righe di codice.

### La metafora della rivista

Il codice sorgente deve essere fruibile come se legessimo una rivista. In alto, il nome della classe, ci aiuta a capire se siamo nel modulo di nostro interesse, subito dopo troviamo le dipendenze e le proprietà della classe, continuando abbiamo i metodi più generici con un **basso** livello di dettaglio e, infine, in fondo troviamo i metodi con un **alto** livello di dettaglio.

### Spaziatura verticale fra i concetti

Lo spazio all'interno di un file è fondamentale per separare i concetti. Ogni riga rappresenta un'espressione e ogni gruppo di righe rappresenta un ragionamento. Questi ragionamenti dovrebbero essere separati dagli altri con delle righe vuote.

### Densità verticale

Le righe di codice strettamente correlate dovrebbero presentarsi vicine l'uno dall'altra.

```text
class ReporterConfig {

    /**
     * Il nome della classe del reporter listener
     */
    private $className;

    /**
     * Le proprietà del reporter listener
     */
    private $properties = [];

    // ...
}
```

Notare come i commenti sopra quelle proprietà, oltre ad essere inutili, spezzano la lettura. Senza quei commenti la classe risulterebbe più facile da leggere.

```text
class ReporterConfig {
    private $className;
    private $properties = [];

    // ...
}
```

### Dichiarazione verticale

Funzioni o variabili che sono strettamente correlate tra di loro dovrebbero trovarsi vicine, così da evitare il _"salto"_ tra una funzione ed un'altra.

### Dichiarazioni di variabili

Le variabili locali dovrebbero essere dichiarate il più possibile vicine al luogo in cui vengono usate.

### Variabili di istanza

Le variabili di istanza dovrebbero essere dichiarate in cima alla classe.

### Funzioni dipendenti

Se una funzione ne richiama un'altra, esse dovrebbero trovarsi vicine e la chiamante dovrebbe essere sopra quella richiamata, se possibile.

### Affinità concettuale

Le funzioni che sono concettualmente affini e che quindi svolgono varianti di una stessa operazione, dovrebbero stare vicine.

```text
class Assert {

    public function assertTrue(string $message, bool $condition): bool {
        if (!$condition) {
            throw new Exception($message);
        }
        return true;
    }

    public function assertFalse(string $message, bool $condition): bool {
        return $this->assertTrue($message, !$condition);
    }
}
```

### Formattazione orizzontale

Per formattazione orizzontale si intendono tutte quelle regole che vengono applicate per rendere chiara la lettura del codice da sinistra a destra.

### Quanto può essere lunga una riga

In genere una riga in un file di codice sorgente non dovrebbe superare gli 80 caratteri.

### Allineamento orizzontale

Evitate l'utilizzo dell'allineamento orizzontale, in quanto distoglie spesso lo sguardo da informazioni importanti quali il tipo \(nel caso delle dichiarazioni\) e l'operatore utilizzato \(nel caso di operazioni di assegnamento\).

```text
class User implements JsonSerializable {
    private $firstName;
    private $lastName;
    private $age;

    public function jsonSerialize(): array {
        return [
            'firstName' => $this->firstName,
            'lastName'  => $this->lastName,
            'age'       => $this->age,
        ];
    }
}
```

Diventa:

```text
class User implements JsonSerializable {
    private $firstName;
    private $lastName;
    private $age;

    public function jsonSerialize(): array {
        return [
            'firstName' => $this->firstName,
            'lastName' => $this->lastName,
            'age' => $this->age,
        ];
    }
}
```

