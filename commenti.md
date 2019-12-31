# Commenti

I commenti sono uno strumento che, se utilizzato con parsimonia, ci permette di rendere più chiare determinate parti del codice, ma, se utilizzato con eccesso, può sortire l'effetto contrario ed indurre a scrivere codice confusionario e fuorviante. Il motivo principale del perché ciò accade è che il codice, essendo in continua evoluzione, rende difficile tenere dei commenti costantemente aggiornati.

### I commenti non bastano a migliorare il codice

Non scrivete commenti solo per migliorare il codice presente. E' molto meglio avere un codice pulito, chiaro e con pochi commenti, piuttosto che un codice intricato e super commentato.

### Spiegatevi nel codice

E' molto meglio scrivere una funzione in più che spiega chiaramente il nostro intento, piuttosto che utilizzare un commento.

Prima del refactoring:

```text
// Restituisce true se lo studente ha la possibilità di accedere alla borsa di studio
if (
    $student->getAge() > 15 && 
    $student->getAverageGrade() > 8.0 && 
    $student->getSchool()->getName() === 'I.T.I.S Archimede'
) {
    // ...
}
```

Dopo il refactoring:

```text
if ($student->canReceiveScholarship()) {
    // ...
}
```

### Buoni commenti

Ci sono alcuni casi in cui un commento può essere realmente utile. Ecco quali:

### Commenti legali

I commenti contenenti le note legali talvolta sono necessari in determinate realtà aziendali. Commenti di questo tipo non devono però contenere pagine e pagine di note legali, ma semplicemente il tipo di licenza utilizzata e un collegamento esterno sulle condizioni d'uso.

### Descrizione dell'intento

Talvolta è utile inserire un commento che spieghi il motivo di una determinata decisione.

```text
public function sendBulkEmails(array $recipients): void {
    foreach ($recipients as $recipient) {
        $this->sendEmail($recipient);
        // Delay per evitare il sovraccarico del provider
        sleep(self::DELAY_BETWEEN_EMAILS);
    }
}

public function sendEmail(User $recipient): void {
    $this->mailer->setSubject($this->emailTemplate->getSubject())
                 ->setFrom($this->emailTemplate->getFrom())
                 ->setTo($recipient->getEmail())
                 ->setBody($this->emailTemplate->toHtml())
                 ->send();
}
```

### Commenti di chiarifica

Commenti di questo tipo talvolta sono necessari per chiarire strani comportamenti. In generale è meglio trovare un altro modo per chiarire un determinato comportamento, ma se questo non è possibile farlo \(es. codice proveniente da una libreria esterna\), può essere utile lasciare commenti di questo tipo.

```text
// $status === self::PAGE_SUCCESS
if (assertTrue($status, self::PAGE_SUCCESS) === 0) {
    // ...
}
```

### Commenti di avvertenza

A volte è utile lasciare un commento per avvertire gli altri programmatori su determinate conseguenze.

```text
/**
 * @deprecated Don't use this class to stock products if you use 2.3.0+ version
 * @see MultiStoreStockInterface
 */
class StockInterface {

    public function stockProduct(ProductInterface $product) {
        // ...
    }
}
```

### Commenti TODO

Questo tipo di commenti sono utili per indicare dei promemoria di funzionalità da correggere all'interno del codice.

### Commenti interpretati

A volte i commenti vengono utilizzati dal linguaggio o dall'IDE per interpretare il nostro codice. Pensate al sistema di annotazioni utilizzato dall'ORM Doctrine o anche il `@var` utilizzato in PHP per indicare un tipo di dato.

```text
/**
 * @return ProductInterface[]
 */
public function getProductsAsArray(): array {
    // ...
}
```

### Cattivi commenti

I tipi di commenti che vedrete nelle prossime righe sono tutti esempi di commenti utilizzati in maniera errata.

### Commenti enigmatici

I commenti devono chiarire i dubbi del programmatore, non aggiungerne degli altri. Evitate commenti che possono essere interpretati soltanto da chi li scrive.

### Commenti ridondanti

Evitate commenti che sono inutili e meno informativi del codice stesso. I commenti devono aggiungere significato al codice, non confermare ciò che il codice già ci dice.

```text
/**
 * Get first name
 * @return string
 */
public function getFirstName(): string {
    return $this->firstName;
}

/**
 * Set first name
 * @param string $firstName
 * @return void
 */
public function setFirstName(string $firstName): void {
    $this->firstName = $firstName;
}
```

### Commenti fuorvianti

Bisogna stare attenti a scrivere i commenti, spesso possono essere fuorvianti e poco accurati rispetto alla reale implementazione.

```text
/**
 * Chiude la connessione al database se è aperta
 * @param int $timeoutMillis
 * @return bool
 */
public function closeConnection(int $timeoutMillis): bool {
    if ($this->$dbConnection->isOpen()) {
        sleep($timeoutMillis);
        $this->$dbConnection->close();
        return true;
    }

    return false;
}
```

Questo commento, oltre ad essere ridondante, è anche fuorviante, in quanto non fa alcun riferimento al metodo `sleep` presente all'interno del metodo. Chi si ferma alla lettura del commento non potrà mai capire perché ci mette così tanto a chiudere la connessione al database.

### Commenti obbligati

Non bisogna mai commentare una funzione o una variabile senza alcuna ragione o solo perché vi è una "regola" in cui tutto il codice deve essere commentato. Questo tipo di commenti non fanno altro che introdurre confusione all'interno del codice.

```text
/**
 * Get first name
 * @return string
 */
public function getFirstName(): string {
    return $this->firstName;
}

/**
 * Set first name
 * @param string $firstName
 * @return void
 */
public function setFirstName(string $firstName): void {
    $this->firstName = $firstName;
}
```

### Commenti a "log"

Non utilizzate i commenti solo per tenere traccia delle modifiche effettuate ad un file. Utilizzate i sistemi di controllo versione come git, che sono nati proprio con questo scopo.

### Non usate un commento al posto di una funzione o una variabile

Ciò che può essere espresso sotto forma di codice, deve essere scritto attraverso il codice.

Prima del refactoring:

```text
// Se lo studente è maggiorenne entra dentro la condizione
if ($student->getAge() > 18) {
    // ...
}
```

Dopo il refactoring:

```text
if ($student->isAdult()) {
    // ...
}
```

### Contrassegni di posizione

Non usate i commenti per contrassegnare determinate parti di codice.

```text
class Person {
    // **************
    // * Properties *
    // **************
    private $firstName;
    private $lastName;
    private $age;

    // ***********
    // * Methods *
    // ***********
    public function getFirstName(): string {
        return $this->firstName;
    }

    public function setFirstName(string $firstName): void {
        $this->firstName = $firstName;
    }
}
```

### Codice commentato

Il codice commentato non fa altro che aggiungere confusione. Chi vede del codice commentato non avrà mai il coraggio di eliminarlo. Penserà che sia lì per qualche buona ragione, quando in realtà si tratta di codice risalente a qualche anno fa.

### Informazioni fuori posizione

Si tratta di commenti che non descrivono il codice circostante, ma bensì funzionalità di sistema presenti altrove. Questo genere di commenti vanno evitati perché spesso non vengono aggiornati qualora vengono effettuate modifiche al sistema.

```text
/**
 * Set port. Default port: 8082
 * @param int $port
 * @return void
 */
public function setPort(int $port): void {
    $this->port = $port;
}
```

### Commenti troppo lunghi

I commenti devono essere brevi e concisi. Evitate di inserire commenti chilometrici.

### Intestazioni di funzioni

Le funzioni "piccole" non hanno bisogno di commenti. Quest'ultimi, il più delle volte, sono inutili se viene utilizzato un nome "parlante" per la funzione.

