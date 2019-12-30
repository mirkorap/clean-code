# Avvertenze ed euristiche

## Commenti

### Commenti inappropriati

Un commento non dovrebbe contenere informazioni inutili quali: la cronologia delle modifiche, l'autore del file, la data di creazione e di ultima modifica. Tutte queste informazioni, ormai, vengono salvate dai sistemi di controllo versione.

### Commenti obsoleti

I commenti invecchiano rapidamente e ben presto diventano imprecisi e obsoleti. Se trovate un commento obsoleto, è meglio aggiornarlo o eliminarlo.

### Commenti ridondanti

Un commento è ridondante se descrive qualcosa di ovvio e già esplicativo.

```text
$i++; // incrementa $i
```

Un altro esempio sono i commenti che riportano la signature e il valore di ritorno del metodo

```text
/**
 * @param Item $item
 * @return void
 */
public function sellItem(Item $item): void { ... }
```

### Commenti mal scritti

I commenti devono essere scritti con attenzione. Non divagate. Non raccontate ovvietà. Siate concisi.

### Codice commentato

Il codice commentato se ne sta lì e "marcisce". Nessuno lo toccherà perché tutti penseranno che sia importante. Quando vedete del codice commentato, **cancellatelo!** Non temete, il sistema di controllo versione continuerà a ricordarlo.

## Ambiente

### Build che richiedono più di un passo

La build di un progetto dovrebbe essere un'unica operazione semplice. Non si deve essere costretti a lanciare una serie di script per compilare i singoli elementi.

### Test che richiedono più di un passo

Dovreste essere in grado di eseguire tutti gli unit test con un solo comando. La capacità di eseguire tutti i test è così importante che dovrebbe essere rapida, facile e banale.

## Funzioni

### Troppi argomenti

Le funzioni dovrebbero avere un numero limitato di argomenti. Meglio nessun argomento; uno, due o tre argomenti vanno bene. Più di tre è troppo!

### Argomenti di output

Gli argomenti passati per riferimento non sono intuitivi. Se la vostra funzione deve modificare lo stato di qualcosa, dovrà trattarsi dello stato dell'oggetto sul quale viene richiamata.

### Flag usati come argomenti

Gli argomenti booleani dichiarano che la funzione fa più di una cosa. Questo perché ci porta ad avere all'interno della funzione una condizione sia per quando il flag è `true` e sia per quando è `false`. Per tale ragione essi vanno evitati.

```text
public function render(bool $canFormat = false): string {
    if ($canFormat) {
        ...
    } else {
        ... 
    }
}
```

### Funzioni "morte"

I metodi che non vengono mai richiamati dovrebbero essere eliminati. Non temete, il sistema di controllo versione continuerà a ricordarli.

## Generali

### Più linguaggi in un file di codice sorgente

Gli ambienti di programmazione di oggi consentono di inserire più linguaggi in un unico file di codice sorgente. Questo potrebbe essere fonte di confusione. Purtroppo, a volte, è necessario utilizzarne più di uno, ma dovremmo cercare di ridurre al minimo i frammenti di altri linguaggi nel nostro file di codice sorgente.

### Non viene implementato un comportamento naturale

Ogni funzione o classe dovrebbe implementare i comportamenti che un altro programmatore possa aspettarsi. Per esempio, considerate una funzione che traduce il nome di un giorno della settimana in valore numerico:

```text
$day = Day::stringToDay($dayName);
```

Ci aspetteremmo che:

* La stringa "Monday" restituisca 1 \(si tratta del primo giorno della settimana\)
* Venissero tradotte anche le abbreviazioni più comuni \(es.: lun, mar, mer...\)
* Non ci siano distinzioni tra maiuscole e minuscole

### Comportamento errato alle delimitazioni

Ogni condizione, ogni caso limite e ogni dettaglio deve essere controllato. Non contate ciecamente sul vostro intuito. Individuate ogni condizione e scrivete un test apposito.

### Disattivazione delle "sicure"

E' pericoloso disattivare i meccanismi di sicurezza. Disattivare determinati warning del compilatore o commentare determinati test, può produrre estenuanti sessioni di debugging.

### Duplicazioni

Il **"Principio DRY"** \(_Don't Repeat Yourself_\) è uno dei principi più importanti della progettazione del software. Esistono diverse forme con cui si può presentare la duplicazione del codice:

* Quando si hanno dei frammenti di codice identici
* Quando abbiamo dei costrutti _switch-case_ o _if-else_ che compaiono più volte
* Quando abbiamo algoritmi simili, ma non con le stesse righe di codice

Ogni volta che troviamo questo tipo di duplicazioni, dobbiamo eliminarle. Nel **primo caso** possiamo creare una funzione che elimina i frammenti di codice identici. Nel **secondo caso** possiamo evitare i costrutti _switch-case_ o _if-else_ attraverso il polimorfismo. Nel **terzo caso** possiamo adottare pattern progettuali come il [**Template Method**](https://it.wikipedia.org/wiki/Template_method) ****o [**Strategy**](https://it.wikipedia.org/wiki/Strategy_pattern).

### Codice posto al livello di astrazione errato

E' importante creare astrazioni che separano i concetti generali di alto livello dai concetti dettagliati di basso livello. Quando ci comportiamo in questo modo, dobbiamo fare in modo che **tutti i concetti di basso livello si trovano nelle classi derivate e che tutti i concetti di alto livello si trovano nelle classi astratte.** Per esempio, le costanti, le variabili e le funzioni che appartengono solo all'implementazione non dovrebbero trovarsi nella classe base. Questa regola riguarda anche i file di codice sorgente, i componenti e i moduli.

### Classi base che dipendono dalle loro derivate

I concetti fra le classi base e derivate devono essere separati e resi indipendenti. In generale, le classi base non dovrebbero conoscere nulla delle loro derivate.

### Eccesso di informazioni

Dobbiamo cercare di limitare il numero di informazioni che esponiamo verso l'esterno. Non create classi con tanti metodi pubblici/protetti. Non create classi con tante variabili di istanza. Nascondete le costanti che non devono essere utilizzate all'esterno della classe. In questa maniera manteniamo le nostre classi leggere e riduciamo al minimo l'accoppiamento.

### Il "dead code"

Il **dead code** è quel codice che non viene mai eseguito. Lo trovate nel corpo di un'istruzione `if` che controlla una condizione che non può mai verificarsi. Lo trovate nel corpo di un metodo che non viene mai richiamato. Quando trovate questo tipo di codice... eliminatelo!

### Separazione verticale

Le variabili e le funzioni dovrebbero essere definite nei pressi di dove vengono usate. 

**Le variabili locali** dovrebbero essere dichiarate appena sopra il loro primo uso. 

**Le funzioni private** dovrebbero essere definite appena sotto il loro primo uso.

### Incoerenza

Se fate qualcosa in un determinato modo, fate anche tutte le cose simili nello stesso modo. Se in una determinata funzione impiegate una variabile dal nome `response` per contenere una `App\Http\Response`, allora usate lo stesso nome in tutte le altre funzioni che usano oggetti di tipo `App\Http\Response` 

### Congestione

Variabili inutilizzate, funzioni mai richiamate, commenti non informativi e così via, sono tutte cose che congestionano il codice e perciò devono essere eliminate.

### Accoppiamento artificioso

Le cose che non dipendono le une dalle altre non dovrebbero essere accoppiate artificiosamente. Una variabile, una costante o una funzione inserita in una posizione inappropriata, costringe le altre classi a dipendere da una classe che non ha niente a che vedere con loro.

### Una questione di invidia \("feature envy"\)

I metodi di una classe dovrebbero interessarsi alle variabili della classe a cui appartengono e non alle variabili di altre classi. Ciò è considerato un _code smells_ perché porta il metodo della classe a dover conoscere la struttura e i dati dell'oggetto che sta manipolando. **Agli oggetti dovremmo chiedere di fare qualcosa e non chiedergli i loro dati interni.** Almeno che non abbiamo a che fare con delle **strutture** \(vedi capitolo "[Oggetti e strutture](https://mirkorap16.gitbook.io/clean-code/oggetti-e-strutture)"\). Chiaramente questa regola non è assoluta e talvolta è necessario esporre i dati di una classe per poterli recuperare.

### Argomenti a "selettore"

Gli argomenti a "selettore" sono quel tipo di argomenti che vengono usati per selezionare il comportamento che deve avere la funzione. Questo genere di argomenti, che solitamente sono di tipo `boolean`, vanno evitati, perché costringe a ricordarsi che cosa significa il valore per quel dato argomento.

```text
public function getErrors(bool $clear): array {
    $messages = $this->getMessages(Message::TYPE_ERROR);
    
    if ($clear) {
        $this->clearByType(Message::TYPE_ERROR);
    }
    
    return $messages;
}
```

Questa funzione utilizza il selettore `$clear` per svuotare i messaggi presenti nella sessione, solo nel caso in cui viene passato il valore `true`. Questo, però, porta a doversi ricordare che esiste questa funzione che, in base al valore passato, consente di pulire o meno la lista dei messaggi. Se proprio non possiamo fare a meno di avere le due funzioni separate, una per recuperare gli errori e l'altra per eliminarli, allora possiamo creare una funzione di questo tipo:

```text
public function getErrorsThenClear(): array {
    $messages = $this->getErrors();
    $this->clearByType(Message::TYPE_ERROR);
    return $messages;
}
```

### Offuscamento dello scopo

Il codice deve essere il più espressivo possibile. Le espressioni al volo, la notazione ungherese e i numeri magici offuscano lo scopo del nostro codice.

```text
public function m_otCalc(): int {
    return $iThsWkd * $iThsRte + (int)round(0.5 * $iThsRte * max(0, $iThsWkd - 400));
}
```

### Responsabilità mal collocate

Una delle responsabilità più importanti di uno sviluppatore è la scelta di dove collocare il codice. Il codice dovrebbe essere collocato là dove chi legge si aspetta di trovarlo. Se dobbiamo creare la funzione per calcolare la paga settimanale di un lavoratore, la collochiamo all'interno della classe `Employee` o nella classe `EmployeePayCalculator`?

### Modificatore static inappropriato

`Math::max($a, $b)` è un buon metodo statico perché non opera su un'unica istanza. Sarebbe assurdo dover dire `new Math()->max($a, $b)` o `$a->max($b)`. Questo perché tutti i dati usati da `Math::max()` provengono dai suoi due argomenti e non da un oggetto "proprietario". In poche parole in nessun caso vogliamo che `Math::max()` sia polimorfica. Tuttavia a volte scriviamo funzioni statiche che non dovrebbero essere statiche:

```text
HourlyPayCalculator::calculate($employee, $overtimeRate);
```

Per la funzione `calculate` è ragionevole che possiamo volere che sia polimorfica. Infatti potremmo voler implementare più algoritmi per il calcolo della paga oraria. Pertanto, in questo caso la funzione non dovrebbe essere statica. In generale dovreste preferire i metodi non statici a quelli statici.

### Usate variabili descrittive

Uno dei modi più potenti per rendere leggibile il codice consiste nel suddividere i calcoli in valori intermedi contenuti in variabili con nomi significativi.

```text
$data = explode('-', $nameFormatted);
$array[$data[1]] = $data[0];
```

Cosa vuol dire il codice sopra riportato? Che cos'è `$data[0]` e `$data[1]`? Riscriviamo lo stesso codice in questa maniera:

```text
[$firstName, $lastName] = explode('-', $nameFormatted);
$array[$lastName] = $firstName;
```

Ora è molto più chiaro! Sappiamo che `$nameFormatted` è una stringa del tipo "Mario-Rossi" e quindi stiamo costruendo un array in cui la chiave è il cognome e il valore è il nome.

### Il nome di una funzione deve essere comunicativo

Osservate il seguente codice:

```text
$newDate = $date->add(5);
```

Che cosa vi aspettate che faccia la funzione `add`? Vi aspettate che aggiunga cinque giorni alla data? O magari settimane o ore? La istanza di `$date` viene modificata o la funzione restituisce un nuovo oggetto `Date` senza modificare il vecchio? E' impossibile capire dalla chiamata che cosa fa la funzione. Se la funzione aggiungesse cinque giorni alla data e la modificasse, allora si dovrebbe chiamare `addDaysTo` o `increaseByDays`. Se invece la funzione restituisce un nuovo oggetto `Date` posto cinque giorni dopo, senza cambiare l'istanza originale, allora la funzione si dovrebbe chiamare `daysLater`. Se siete costretti a dover guardare l'implementazione per capire che cosa fa una funzione, allora vuol dire che dovreste scegliere un nome migliore.

### Comprendere l'algoritmo

Prima di pensare di aver terminato di lavorare su una funzione, assicuratevi di aver capito come essa funziona. Non basta sapere che la funzione "fa il suo lavoro", bisogna anche comprenderne il funzionamento. Se non si riesce a comprenderne il funzionamento, allora vuol dire che bisogna rendere il codice più pulito e chiaro.

### Rendere fisiche le dipendenze logiche

Se un modulo dipende da un altro, tale dipendenza dovrebbe essere fisica, non solo logica. Per esempio, immaginate di voler creare una funzione che stampa un report delle ore di lavoro dei dipendenti. Abbiamo una classe `HourlyReporter` che raccoglie tutti i dati e poi li passa a `HourlyReportFormatter` che si occupa invece della stampa:

```text
class HourlyReporter {
    private const PAGE_SIZE = 55;
    private $formatter;
    private $page;
    
    public function __construct(HourlyReportFormatter $formatter) {
        $this->formatter = $formatter;
        $this->page = new Collection();
    }
    
    public function generateReport(array $employees): void {
        foreach ($employees as $employee) {
            $this->addLineItemToPage($employee);
            
            if ($this->page->size() === self::PAGE_SIZE) {
                $this->printAndClearItemList();
            }
        }
        
        if ($this->page->size() > 0) {
            $this->printAndClearItemList();
        }
    }
    
    private function printAndClearItemList(): void {
        $this->formatter->format($this->page);
        $this->page->clear();
    }
    
    private function addLineItemToPage(Employee $employee): void {
        $item = new LineItem();
        $item->setName($employee->getName());
        $item->setHours($employee->getTenthsWorked() / 10);
        $item->setTenths($employee->getTenthsWorked() % 10);
        $this->page->add($item);
    }
}
```

La costante `PAGE_SIZE` è una dipendenza logica che non è stata resa fisica. Perché `HourlyReporter` dovrebbe conoscere le dimensioni della pagina? Ciò dovrebbe rientrare nelle responsabilità di `HourlyReportFormatter`. Possiamo rimediare rendendo fisica questa dipendenza creando in `HourlyReportFormatter` un nuovo metodo chiamato `getMaxPageSize`. `HourlyReporter` richiamerà questo metodo invece di utilizzare la costante `PAGE_SIZE` 

### Meglio il polimorfismo di un if/else o di uno switch/case

Molti usano le istruzioni if/else o switch/case perché è la classica soluzione a forza bruta e non perché esse siano la soluzione più adatta alla situazione. Pertanto, ogni volta che vediamo un'istruzione di questo tipo, ricordiamoci di considerare il polimorfismo come una possibile alternativa. Inoltre, cerchiamo di rispettare la regola del **One Switch:**

{% hint style="info" %}
Non ci deve mai essere più di un'istruzione switch per un determinato tipo di selezione. I case in tale istruzione switch devono creare oggetti polimorfici, in modo tale da evitare di ripetere lo stesso switch/case in altri punti del sistema.
{% endhint %}

### Seguite convenzioni standard

Ogni team dovrebbe seguire uno standard di programmazione basato su norme convenzionali del settore. Ogni membro del team deve accettare e seguire lo stesso standard deciso da tutto il team.

### Al posto dei "numeri magici", usate costanti "parlanti"

Non è una buona idea avere dei numeri grezzi all'interno del codice. Ciò non solo rende difficile riconoscerli, ma rende difficile anche trovare tutte le loro occorrenze. Pertanto, al loro posto, dovreste utilizzare delle costanti dal nome significativo.

```text
const MAX_SCHEDULE_PER_DAY = 10;
const SECONDS_PER_DAY = 86400;
const LINES_PER_PAGE = 50;
const PROCESSING_ORDER_STATUS = 'processing';
const DEFAULT_HOST = 'localhost';
```

### Siate precisi

Quando prendete una decisione nel vostro codice, cercate di essere precisi. Non siate pigri nella precisione delle vostre decisioni. Se richiamate una funzione che potrebbe restituire `null`, occupatevi di quel valore. Se richiamate una funzione che potrebbe lanciare un'eccezione, implementate un meccanismo per gestire le eccezioni. Questo tipo di imprecisioni dovrebbero essere eliminate.

### Il principio "structure over convention"

Esplicitate le decisioni progettuali con la struttura piuttosto che con una semplice convenzione. Le convenzioni di denominazione vanno bene, ma sono comunque inferiori rispetto ad una struttura progettuale che imponga la regola.

### Incapsulate i costrutti condizionali

La logica booleana a volte è difficile da comprendere. Pertanto create delle funzioni che spieghino lo scopo del costrutto condizionale.

```text
if ($this->shouldBeDeleted($timer)) { ... }

è meglio rispetto a:

if ($timer->hasExpired() && !$timer->isRecurrent()) { ... }
```

### Evitate i negativi nei costrutti condizionali

I costrutti condizionali negativi sono più difficili da comprendere rispetto a quelli positivi.

```text
if ($this->shouldBeDeleted($timer)) { ... }

è più semplice da capire rispetto a:

if (!$this->shouldNotBeDeleted($timer)) { ... }
```

### Le funzioni dovrebbero fare una cosa sola

Le funzioni che fanno più di una cosa dovrebbero essere convertite in funzioni più piccole, ognuna delle quali fa una cosa soltanto.

```text
public function pay(): void {
    foreach ($this->employees as $e) {
        if ($e->isPayDay()) {
            $pay = $e->calculatePay();
            $e->deliverPay($pay);
        }
    }
}
```

Questa funzione fa tre cose:

* Cicla tutti i dipendenti
* Controlla se il dipendente deve essere pagato
* Paga il dipendente

Possiamo dividere questa funzione nel seguente modo:

```text
public function pay(): void {
    foreach ($this->employees as $e) {
        $this->payIfNecessary($e);
    }
}

private function payIfNecessary(Employee $e): void {
    if ($e->isPayDay()) {
        $this->calculateAndDeliverPay($e);
    }
}

private function calculateAndDeliverPay(Employee $e): void {
    $pay = $e->calculatePay();
    $e->deliverPay($pay);
}
```

### Accoppiamenti temporali nascosti

Gli accoppiamenti temporali possono essere necessari, ma non nascondeteli.

```text
class MoogDiver {
    private $gradient;
    private $splines = [];
    
    public function dive(string $reason): void {
        $this->saturateGradient();
        $this->reticulateSplines();
        $this->diveForMoog($reason);
    }
    
    private function saturateGradient(): void { ... }
    
    private function reticulateSplines(): void { ... }
    
    private function diveForMoog(string $reason): void { ... }
}
```

L'ordine delle tre funzioni è importante. Dobbiamo prima saturare il gradiente, dopo dobbiamo produrre il reticolo delle spline e infine passare tutto alla funzione `diveForMoog`. Questo accoppiamento temporale non si capisce bene. Potremmo riscrivere le funzioni nel seguente modo:

```text
class MoogDiver {
    private $gradient;
    private $splines = [];
    
    public function dive(string $reason): void {
        $this->saturateGradientAndReticulateSplines();
        $this->diveForMoog($reason);
    }
    
    private function saturateGradientAndReticulateSplines(): void {
        // codice per saturare il gradiente
        $this->reticulateSplines();
    }
    
    private function reticulateSplines(): void { ... }
    
    private function diveForMoog(string $reason): void { ... }
}
```

### Non siate arbitrari

Abbiate un motivo per scegliere la struttura del vostro codice e assicuratevi che tale motivo sia comunicato adeguatamente dalla struttura del codice. Se la sua struttura appare arbitraria, gli altri si sentiranno in diritto di cambiarla e di migliorarla.

### Incapsulate le condizioni di delimitazione

Le condizioni di delimitazione sono difficili da comprendere. Incapsulatele in un solo luogo e **non** sparpagliatele in tutto il codice.

```text
if ($level + 1 < count($tags)) {
    $parts = new Parse($body, $tags, $level + 1);
    $body = null;
}
```

Dall'esempio sopra riportato compare per ben due volte `$level + 1`. Questa condizione di delimitazione dovrebbe essere incapsulata nel seguente modo:

```text
$nextLevel = $level + 1;
if ($nextLevel < count($tags)) {
    $parts = new Parse($body, $tags, $nextLevel);
    $body = null;
}
```

### Le funzioni devono discendere un solo livello di astrazione

Le istruzioni presenti all'interno di una funzione dovrebbero tutte trovarsi allo stesso livello di astrazione, il quale dovrebbe essere esattamente un livello sotto l'operazione descritta dal nome della funzione.

```text
public function render(): string {
    $html = new StringBuffer('<hr');
    if ($this->size > 0) {
        $html->append(' size="')->append($size + 1)->append('"');
    }
    $html->append('>');
    return $html->toString();
}
```

Questa funzione presenta due livelli di astrazione. Il primo è il concetto che una linea orizzontale abbia uno spessore \($size\). Il secondo è la sintassi del tag HR. Rifattorizziamo la funzione in modo tale che abbia un solo livello di astrazione:

```text
public function render(): string {
    $hr = new HtmlTag('hr');
    if ($this->size > 0) {
        $hr->addAttribute('size', $this->calculateHeight());
    }
    return $hr->html();
}

private function calculateHeight(): string {
    $height = $this->size + 1;
    return sprintf('%d', $height);
}
```

### Mantenete ai livelli più elevati i dati configurabili

Se avete una costante, come un default o un valore di configurazione, la quale è nota e prevista a un livello di astrazione superiore, non seppellitela in una classe che si trova ad un basso livello di astrazione. Spostatela in alto, così che essa possa essere "diffusa" ai livelli sottostanti dell'applicazione.

### Evitate la navigazione transitiva

Evitate di far conoscere ai vostri moduli l'intera mappa di navigazione del sistema. Se il modulo A utilizza B e il modulo B utilizza C, non vogliamo che il modulo A conosca e utilizza C. Non vogliamo avere catene di chiamate come la seguente: `$a->getB()->getC()->doSomething();`. Questa è chiamata [_Legge di Demetra_](https://mirkorap16.gitbook.io/clean-code/oggetti-e-strutture#la-legge-di-demetra). Piuttosto vogliamo che i nostri moduli ci offrano direttamente ciò di cui abbiamo bisogno. Dovremmo semplicemente poter dire: `$a->doSomething();` 

## Nomi

### Scegliete nomi descrittivi

Nel software, i nomi contribuiscono per il 90% alla leggibilità del codice. Pertanto sceglieteli sempre con grande cura. Attraverso l'utilizzo di buoni nomi, coloro che leggeranno il codice capiranno subito qual è il contenuto di una variabile, che cosa fa la funzione o ancora qual è lo scopo della classe.

### Scegliete nomi collocati al corretto livello di astrazione

Scegliete dei nomi che riflettano il livello di astrazione della classe o della funzione nella quale state lavorando. Considerate l'interfaccia seguente:

```text
interface Modem {
    public function dial(string $phoneNumber): bool;
    public function disconnect(): bool;
    public function send(string $c): bool;
    public function recv(): string;
    public function getConnectedPhoneNumber(): string;
}
```

L'interfaccia sopra presenta dei nomi collocati ad un livello di astrazione errato, perché da per scontato che i modem si connetteranno componendo il numero, ma potrebbero esserci modem che invece si connettono tramite cavo. Pertanto rinominiamo le funzioni nel seguente modo:

```text
interface Modem {
    public function connect(string $connectionLocator): bool;
    public function disconnect(): bool;
    public function send(string $c): bool;
    public function recv(): string;
    public function getConnectedLocator(): string;
}
```

### Usate la nomenclatura standard

I nomi sono più facili da comprendere se si basano su una convenzione. Per esempio, se utilizzate il pattern DECORATOR, potete aggiungere la parola `Decorator` come suffisso nel nome delle classi di "decorazione". Spesso i team sviluppano un proprio standard di denominazione per un determinato progetto. Questo faciliterà nella comprensione coloro che leggeranno il codice.

### Nomi non ambigui

Scegliete dei nomi che non introducano ambiguità nel comportamento di una funzione o di una variabile. Piuttosto utilizzate dei nomi un po' più lunghi se questo vi aiuta ad enfatizzare il comportamento della funzione.

### Usate nomi lunghi per grandi campi di visibilità \(scope\)

La lunghezza di un nome dovrebbe essere correlata all'estensione del livello di visibilità \(scope\). Potete usare nomi corti per le variabili con scope molto limitato, ma se la variabile risulta ampiamente visibile, dovreste usare nomi lunghi.

Nell'esempio seguente ho utilizzato un nome molto corto per indicare un dipendente `$e`, questo perché la variabile ha uno scope molto limitato.

```text
public function pay() {
    foreach ($this->employees as $e) {
        $this->payIfNecessary($e);
    }
}
```

### Evitate le codifiche

I nomi non dovrebbero contenere una codifica del tipo: `$_`, `$o_user`, `f_` ecc. Proteggete i vostri nomi dalla notazione ungherese.

### I nomi dovrebbero descrivere anche gli effetti collaterali

I nomi dovrebbero descrivere tutto quello che una variabile, una funzione o una classe è o fa. Non usate un semplice verbo per descrivere una funzione che svolge più di un'azione.

```text
public function getCollection(): Collection {
    if ($this->collection === null) {
        $this->collection = new Collection();
    }
    return $this->collection;
}
```

La funzione sopra riportata fa più di un'azione: crea una collection, se non è stata ancora creata, e la restituisce. Pertanto dovrebbe essere rinominata in `createCollectionIfNotExists` 

## Test

### Test insufficienti

I test, soprattutto quelli unitari, dovrebbero coprire tutta la codebase! I test sono insufficienti ogni volta che ignorano anche solo un'unica condizione.

### Usate uno strumento di copertura

I report degli strumenti di copertura aiutano a scovare le parti di codice che non sono state sottoposte a test. Usateli per avere una copertura totale del vostro codice.

### Non saltate i test che considerate banali

Sono facili da scrivere e il loro valore documentario è maggiore dell'impegno necessario per realizzarli.

### Verificate le condizioni di delimitazione

Sottoponete a test anche le condizioni di delimitazione. Spesso è lì che si nascondono dei comportamenti indesiderati.

### Applicate test esaustivi in prossimità dei bug

Quando trovate un bug in una funzione, è saggio svolgere un test esaustivo su tale funzione.

### I test aiutano a diagnosticare i problemi

Talvolta potete diagnosticare un problema semplicemente osservando i casi di test che falliscono. Questo è un motivo in più per far sì che i casi di test siano il più possibile completi.

### I test dovrebbero essere veloci

Un test lento è un test che non verrà eseguito volentieri. Pertanto fate tutto il possibile per curare la velocità dei vostri test.

