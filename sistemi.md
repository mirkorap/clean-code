# Sistemi

In questo capitolo parleremo della pulizia del codice e della separazione degli ambiti a livello di sistema.

### Separate la realizzazione dall'uso di un sistema

I sistemi software dovrebbero separare il processo di avvio, ovvero quando vengono istanziati gli oggetti dell'applicazione e vengono stabilite le dipendenze, dalla logica runtime che si esprime dopo l'avvio.

```text
class MyClass {
    public function getService(): Service {
        if ($this->service === null) {
            $this->service = new Service();
        }
        return $this->service;
    }
}
```

Questa si chiama "lazy initialization", ovvero la costruzione dell'oggetto avviene solo nel momento in cui abbiamo effettivamente bisogno di quell'oggetto. Tuttavia così abbiamo creato una dipendenza diretta con la classe `Service` e da tutto ciò che richiede il suo costruttore \(che per semplicità ho lasciato vuoto\). Questo non solo viola il principio SRP, in quanto se cambia il costruttore della classe `Service` sarà costretta a cambiare anche questa classe, ma stiamo anche precludendo il fatto che la classe `Service` vada bene in ogni contesto. Per questo occorre separare il processo di avvio dalla logica runtime.

### Separazione di main

Una soluzione per separare la costruzione degli oggetti dal loro utilizzo consiste nel trasferire tutti gli aspetti della costruzione all'interno di `main`, che non fa altro che costruire gli oggetti e passarli all'applicazione. Quest'ultima sarà quindi all'oscuro di come gli oggetti vengono costruiti, semplicemente si aspetta che tutto sia stato già costruito.

### Factory

Talvolta è necessario che l'applicazione sia responsabile del momento in cui l'oggetto viene creato. In questi casi possiamo usare il pattern [Abstract Factory](https://en.wikipedia.org/wiki/Abstract_factory_pattern), così da permettere all'applicazione di controllare la creazione dell'oggetto, pur mantenendo i dettagli della costruzione separati rispetto al codice dell'applicazione.

### Dependency Injection

Un potente meccanismo per separare la costruzione degli oggetti dal loro utilizzo è chiamato **Dependency Injection \(DI\)**, che è l'applicazione dell'inversione del controllo \([Inversion of Control - IoC](https://it.wikipedia.org/wiki/Inversione_del_controllo)\) alla gestione delle dipendenze. Nel contesto della gestione delle dipendenze, un oggetto non dovrebbe assumersi la responsabilità di istanziare le proprie dipendenze. Questa responsabilità deve essere trasferita altrove. Nella Dependency Injection la classe fornisce dei metodi setter o degli argomenti del costruttore \(o entrambi\), che vengono utilizzati per iniettare le dipendenze. Il container DI istanzia gli oggetti richiesti e li passa al costruttore o ai metodi setter della classe. Quali oggetti dipendenti vengono usati in realtà viene specificato all'interno di un file di configurazione \(xml / yml\).

### Confusione di ambiti

All'interno di un sistema il codice deve essere modulare, ci deve essere una netta separazione degli ambiti. Per esempio in un sistema in cui è presente la persistenza degli oggetti in memoria, la logica che si occupa della persistenza deve essere incapsulata e separata rispetto alla logica di dominio. Questa separazione degli ambiti, oltre a rendere più semplice la scrittura dei test, favorisce l'evoluzione incrementale del nostro codice.

### Sottoporre a test l'architettura del sistema

Il vantaggio di separare gli ambiti all'interno dell'architettura del sistema porta ad avere un codice disaccoppiato e facilmente testabile. Così facendo possiamo far evolvere la nostra applicazione, adottando le nuove tecnologie disponibili, senza adottare soluzioni in stile [**BDUF**](https://en.wikipedia.org/wiki/Big_Design_Up_Front) **\(Big Design Up Front\)**. BDUF è la pratica che consiste nel progettare tutto in anticipo, prima ancora di iniziare ogni implementazione. Questa pratica può essere pericolosa, poiché non favorisce gli adattamenti a causa della resistenza psicologica a scartare ogni decisione che va fuori dalla pianificazione iniziale. Se invece abbiamo una struttura in cui l'accoppiamento è minimo, possiamo facilmente far evolvere la nostra struttura man mano che il progetto cresce di dimensioni.

### Usate gli standard con cura, solo se dimostrano il proprio valore

Molti team utilizzano gli standard solo perché tali, seppur mal si sposano con le reali esigenze di progetto. Gli standard vanno utilizzati solo se dimostrano realmente il proprio valore e non rappresentano invece un ostacolo.

