# Simple Design

Esistono quattro semplici regole che ci consentono di scrivere del codice pulito. Queste regole, descritte per la prima volta da Kent Beck in **Simple Design**, ci dicono che un progetto è semplice se:

* Passa tutti i test
* Non contiene duplicazioni
* Esprime lo scopo del programmatore
* Minimizza il numero di classi e metodi

Le regole sono elencate in ordine di priorità.

### Regola 1 - Passa tutti i test

Un sistema che ha il 90% del codice coperto da test è un sistema collaudabile. Il fatto di rendere collaudabile un sistema favorisce una progettazione in cui le nostre classi sono piccole e svolgono un unico compito \(SRP\). Uno stretto accoppiamento complica la creazione di test. Pertanto più test scriviamo e più usiamo principi come il DIP e strumenti come la Dependency Injection, le interfacce e l'astrazione per ridurre al minimo l'accoppiamento.

### Regola 2 - Niente duplicazione

Una volta che abbiamo i nostri test, possiamo rifattorizzare il codice senza alcun timore eliminando le duplicazioni presenti. La duplicazione è il nemico numero uno in un progetto, perché introduce rischi e complica inutilmente il codice. Per eliminarla dobbiamo estrarre ad un livello minuto ciascun metodo, cosicché possiamo riconoscere un eventuale violazione del principio SRP, il quale ci porterà ad estrarre un ulteriore classe. Il pattern [**Template Method**](https://it.wikipedia.org/wiki/Template_method) è molto utilizzato per eliminare la duplicazione. 

Prendiamo per esempio questa classe che si occupa di calcolare il numero di giorni di vacanza in base alla nazionalità del dipendente:

```text
class VacationPolicy {
    public function accrueUSDivisionVacation(): void {
        // codice per calcolare le vacanze sulla base del numero di ore lavorate
        
        // codice per garantire il minimo di legge USA in giorni di vacanza
        
        // codice per considerare il periodo di vacanza nella paga
    }
    
    public function accrueEUDivisionVacation(): void {
        // codice per calcolare le vacanze sulla base del numero di ore lavorate
        
        // codice per garantire il minimo di legge EU in giorni di vacanza
        
        // codice per considerare il periodo di vacanza nella paga
    }
}
```

Il codice di queste due funzioni è quasi uguale, l'unica differenza sta nel codice per calcolare il minimo di legge. Possiamo eliminare questa duplicazione utilizzando il pattern **Template Method**:

```text
abstract class VacationPolicy {
    public function accrueVacation(): void {
        $this->calculateBaseVacationHours();
        $this->alterForLegalMinimums();
        $this->applyToPayroll();
    }
    
    private function calculateBaseVacationHours(): void { ... }
    
    abstract protected function alterForLegalMinimums(): void;
    
    private function applyToPayroll(): void { ... }
}

class USVacationPolicy {
    protected function alterForLegalMinimums(): void { ... }
}

class EUVacationPolicy {
    protected function alterForLegalMinimums(): void { ... }
}
```

### Regola 3 - Espressività

La maggior parte del costo di un progetto software è la sua manutenzione a lungo termine. Aumentando la complessità del sistema, uno sviluppatore avrà bisogno di sempre più tempo per comprenderlo. Pertanto, il codice deve esprimere chiaramente lo scopo del suo autore.

Possiamo curare l'espressività nei seguenti modi:

* Scegliendo buoni nomi
* Mantenendo piccole le funzioni e le classi
* Usando sistemi di denominazione standard. Per esempio se usiamo i pattern **Command** o **Visitor**, possiamo aggiungere questi nomi al nome della classe
* Scrivere unit test. Un obiettivo importante dei test è quello di fungere da documentazione basata sull'esempio

### Regola 4 - Minimizzare classi e metodi

Nel tentativo di compattare le nostre classi e metodi, potremmo correre il rischio di creare classi e metodi troppo piccoli. Pertanto bisogna cercare di essere pragmatici e contenere il numero di classi e metodi \(ad esempio creare un'interfaccia per ogni classe non ha senso\).

