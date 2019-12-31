# Funzioni

Le funzioni rappresentano le azioni che è possibile compiere all'interno del nostro software. Per questo motivo è importante che siano chiare e semplici da leggere. Come fare? Scopriamolo insieme!

### Che sia Piccola

La prima regola delle funzioni è che devono essere piccole. In generale una funzione non dovrebbe superare le 20 righe di codice.

### Blocchi e indentazione

I blocchi delle istruzioni `if`, `else`, `for` e così via, devono essere di una sola riga. Probabilmente tale riga dovrà contenere una chiamata a funzione. In questa maniera il nostro codice non solo resta compatto, ma sarà anche più chiaro, perché la funzione richiamata avrà un nome descrittivo. Ciò consente anche di non avere grandi strutture annidate. Il livello di indentazione di una funzione non dovrebbe essere superiore a uno o due.

### Che faccia una cosa sola

Cosa significa "fare una cosa sola"? Prendiamo per esempio questa funzione:

```text
public function renderPageWithSetupsAndTeardowns() {
    if ($this->isTestPage($this->pageData)) {
        $this->includeSetupAndTeardown($this->pageData, $this->isSuite);
    }

    return $this->pageData;
}
```

In ordine questa funzione fa tre cose:

    * Determina se è una pagina di test
    * In caso affermativo, include attacchi e chiusure
    * Esegue il rendering della pagina HTML

Apparentemente questa funzione fa tre cose, ma notare come questi passi si trovano ad un solo livello di astrazione da quello che è il nome della funzione. In generale, una funzione fa una cosa sola quando i passi della funzione si trovano ad un solo livello di astrazione da quello che è il nome scelto per la funzione. Un altro modo per capire se una funzione sta facendo più di una cosa, è scoprire se riuscite ad estrarre un'altra funzione con un nome che non è semplicemente una riaffermazione della sua implementazione.

### Sezioni all'interno delle funzioni

Le funzioni che fanno più di una cosa spesso possono essere suddivise in tre sezioni: _dichiarazioni_, _inizializzazioni_ e _setaccio_. Se la funzione a cui stai lavorando presenta queste tre sezioni, vuol dire che essa non fa una cosa sola.

### La regola dei passi

Le funzioni devono essere lette come se fossero dei paragrafi, ognuno dei quali descrive il livello di astrazione corrente e fa riferimento ai successivi paragrafi \(ovvero alle funzioni\) posti ad un livello di astrazione successivo.

### Istruzioni switch

Le istruzioni `switch` sono problematiche, in quanto le funzioni che le contengono non solo fanno più di una cosa, ma spesso violano anche i principi SOLID.

```text
public function calculatePay(Employee $employee): float {
    switch($employee->getType()) {
        case Employee::COMMISSIONED:
            return $this->calculateCommissionedPay($employee);
        case Employee::HOURLY:
            return $this->calculateHourlyPay($employee);
        case Employee::SALARIED:
            return $this->calculateSalariedPay($employee);
        default:
            throw new InvalidEmployeeType($employee->getType());
    }
}
```

Questa funzione viola sia il principio di singola responsabilità \(SRP\), che il principio Open Closed \(OCP\) in quanto ogniqualvolta che si aggiungerà un nuovo tipo, si dovrà modificare la funzione. La soluzione in questi casi è quella di usare il polimorfismo + una classe factory che si occupa di istanziare oggetti a seconda del tipo. Le istruzioni `switch` sono infatti tollerate solo se vengono usate per creare oggetti polimorfici.

```text
interface Employee {
    public function isPayday(): bool;
    public function calculatePay(): float;
    public function deliverPay(Money $pay): void;
}

interface EmployeeFactory {
    public function make(EmployeeRecord $record): Employee;
}

class EmployeeFactoryImpl implements EmployeeFactory {

    public function make(EmployeeRecord $record): Employee {
        switch($record->getType()) {
            case EmployeeRecord::COMMISSIONED:
                return new CommissionedEmployee();
            case EmployeeRecord::HOURLY:
                return new HourlyEmployee();
            case EmployeeRecord::SALARIED:
                return new SalariedEmployee();
            default:
                throw new InvalidEmployee($record->getType());
        }
    }
}
```

### Usate nomi descrittivi

Il nome della funzione deve chiarire subito ciò che la funzione fa. Più piccola sarà la funzione, più facile sarà scegliere un nome descrittivo. Inoltre è sempre meglio dare un nome lungo e descrittivo, piuttosto che un nome corto ed enigmatico preceduto da un lungo commento.

### Argomenti di funzione

Una funzione non dovrebbe avere più di tre argomenti. Gli argomenti in una funzione, non solo la rendono difficile da leggere, ma generano anche difficoltà nello scrivere tutti i casi di test.

### Funzioni ad un solo argomento \(monadica\)

Le funzioni con un solo argomento \(monadica\) vengono usate o per fare una domanda relativa a tale argomento: `isFileExists("test.txt")` o per operare su tale argomento: `fileOpen("test.txt")`. Un caso eccezionale sono gli eventi, dove a fronte di un argomento di input non c'è alcun valore restituito.

### Evitare i flag come argomenti

I flag come argomenti sono da evitare, in quanto è sintomo che la funzione non fa una cosa sola. Fai X se il flag è `true` altrimenti fai Y.

### Funzioni a due argomenti \(diadica\)

Le funzioni a due argomenti \(diadica\) sono più difficili da leggere, per questo motivo vanno preferibilmente utilizzate solo quando c'è coerenza tra i due argomenti: `new Point(12, 23)`. In tutti gli altri casi si consiglia di trasformare le funzioni diadiche in funzioni monadiche.

Funzione diadica:

```text
class Example {

    public function writeField(StreamInterface $outputStream, string $name) {
        // ...
    }
}
```

Funzione monadica:

```text
class Example {

    private $outputStream;

    public function __construct(StreamInterface $outputStream) {
        $this->outputStream = $outputStream;
    }

    public function writeField(string $name) {
        // ...
    }
}
```

### Funzioni a tre argomenti \(triadica\)

Le funzioni a tre argomenti \(triadica\) sono da evitare, in quanto rendono molto più difficile sia leggere il codice che testarlo. Utilizzarle solo in casi eccezionali.

### Oggetti usati come argomenti

Se una funzione richiede più di due / tre argomenti, è probabile che questi argomenti devono essere inseriti all'interno di una classe separata.

Funzione a tre argomenti:

```text
public function makeCircle(double $x, double $y, double $radius)
```

Funzione a due argomenti:

```text
public function makeCircle(Point $p, double $radius)
```

### Verbi e parole chiave

La scelta del nome della funzione è fondamentale per chiarire sia lo scopo della funzione e sia lo scopo e l'ordine degli argomenti. Per tale ragione occorre trovare una buona accoppiata verbo/nome che tenga conto anche degli argomenti presenti nella funzione.

Ad esempio questa funzione:

```text
public function assertEquals($expected, $actual)
```

Potrebbe essere rinominata in:

```text
public function assertExpectedEqualsActual($expected, $actual)
```

### Niente effetti collaterali

Le funzioni non devono avere effetti collaterali. Se una funzione dice di fare X e poi fa anche Y, questo, non solo viola la regola di "fare una cosa sola", ma potrebbe anche portare a comportamenti indesiderati.

### Argomenti di output

Evitare gli argomenti di output, in quanto sono brutti e difficili da leggere. Se la funzione deve modificare lo stato, lo deve fare per l'oggetto a cui appartiene.

Funzione da evitare:

```text
public function appendFooter(string &$pageHtml)
```

Funzione corretta:

```text
class Page {
    private $html;

    public function appendFooter() {
        // ...
    }
}

$page = new Page();
$page->appendFooter();
```

### Separate i comandi dalle richieste

Le funzioni devono _fare_ qualcosa oppure _rispondere_ a qualcosa, non entrambe le cose. La funzione deve o modificare lo stato di un oggetto oppure restituire un informazione su tale oggetto. Se fa entrambe le cose, vuol dire che la funzione ha più di una responsabilità.

```text
public function set(string $attributeName, string $value): bool {
    // ...
}

if ($this->set('username', 'mirko123')) {
    // ...
}
```

Questa funzione imposta il valore all'attributo e restituisce `true` se ha successo \(e quindi l'attributo esiste\), altrimenti ritorna `false`. La funzione è poco chiara, per sistemarla occorre separare il comando dalla richiesta:

```text
public function isAttributeExists(string $attributeName): bool {
    // ...
}

public function setAttribute(string $attributeName, string $value): void {
    // ...
}

if ($this->isAttributeExists('username')) {
    $this->setAttribute('username', 'mirko123');
}
```

### Scegliete le eccezioni invece dei codici d'errore

I codici d'errore sono da evitare perché inducono a creare istruzioni `if` annidate. Al loro posto è meglio utilizzare le eccezioni.

```text
if ($this->userRepository->delete($user) === UserRecord::STATUS_OK) {
    if ($this->mailer->send($email) === MailerRecord::STATUS_OK) {
        print_r('Email inviata con successo.');
    } else {
        print_r('Non è stato possibile inviare l`email.');
    }
} else {
    print_r('Non è stato possibile eliminare l`utente.');
}
```

Diventa:

```text
public function deleteUser(User $user) {
    try {
        $this->userRepository->delete($user);
        $this->mailer->send($this->email);
    } catch(Exception $e) {
        print_r($e->getMessage());
    }
}
```

### Estraete i block try / catch

I blocchi try / catch sono brutti da vedere e spesso creano confusione. Pertanto è meglio estrarli e inserirli all'interno di funzioni a sé stanti.

L'esempio sopra diventa:

```text
public function deleteUser(User $user) {
    try {
        $this->deleteUserAndSendEmail($user);
    } catch(Exception $e) {
        print_r($e->getMessage());
    }
}

private function deleteUserAndSendEmail(User $user) {
    $this->userRepository->delete($user);
    $this->mailer->send($this->email);
}
```

### Il principio DRY

Seguite il principio DRY \(_Don't Repeat Yourself_\) per evitare duplicazioni nel codice.

