# Delimitazioni

Spesso abbiamo la necessità di integrare nel nostro codice pacchetti e/o librerie esterne. Esistono delle tecniche che ci consentono di integrare in modo pulito questo codice esterno con il nostro.

### Usare codice esterno

Chi fornisce il package o il framework punta a un'ampia applicabilità. Chi utilizza il package, invece, desidera avere delle interfacce che siano calibrate sulle proprie specifiche esigenze. Il codice di un package di terze parti è soggetto a continui cambiamenti che vanno incontro alle esigenze di un'ampia utenza. Questi cambiamenti, talvolta, potrebbero essere problematici per la nostra applicazione e potrebbero persino romperla. Perciò dobbiamo "salvaguardare" il nostro codice construendo delle interfacce di cui abbiamo il pieno controllo.

Interfaccia di cui **non** abbiamo il pieno controllo:

```text
$users = [
    ['name' => 'Mario', 'surname' => 'Rossi', 'age' => 35],
    ['name' => 'Carlo', 'surname' => 'Tropea', 'age' => 42],
    ['name' => 'Luca', 'surname' => 'Tozzi', 'age' => 48],
];
$userCollection = new ExternalPackage\Collection($users);
$names = $userCollection->map(fn($user) => $user['name']);
$userCollection->clear(); // Ops... io non volevo permettere la cancellazione :(
```

Interfaccia di cui abbiamo il pieno controllo:

```text
class UserCollection {
    private $collection;

    public function __construct(array $users) {
        $this->collection = new ExternalPackage\Collection($users);
    }

    public function getNames(): array {
        if ($this->collection->hasKey('name')) {
            return $this->collection->map(fn($user) => $user['name']);
        }

        return [];
    }
}

$users = [
    ['name' => 'Mario', 'surname' => 'Rossi', 'age' => 35],
    ['name' => 'Carlo', 'surname' => 'Tropea', 'age' => 42],
    ['name' => 'Luca', 'surname' => 'Tozzi', 'age' => 48],
];
$userCollection = new UserCollection($users);
$names = $userCollection->getNames();
// Non posso effettuare la cancellazione perché la mia interfaccia non me lo consente :)
```

### Learning Test

Capire come funziona un'API esterna è difficile. Integrare il codice esterno è ancora più difficile. Invece di sperimentare le API esterne direttamente nel codice dell'applicazione, potremmo creare dei test, chiamati appunto **Learning Test**, che ci consentono di capire come funziona il codice esterno. I learning test verificano che i package esterni che usiamo funzionano come ci aspettiamo. Questo ci garantisce anche che, se il package esterno subisce dei cambiamenti con le nuove release, il nostro codice continui a funzionare. Ciò ci aiuta ad aggiornare i nostri package senza alcun timore.

### Conclusioni

Un buon progetto software si deve adattare ai cambiamenti \(sia interni che esterni\). Dovremmo evitare di far conoscere al nostro codice troppi dettagli del software esterno. E' meglio dipendere da qualcosa sotto il nostro controllo che da qualcosa che non possiamo controllare. L'utilizzo dell'[Adapter Pattern](https://it.wikipedia.org/wiki/Adapter_pattern) ci aiuta ad eseguire la conversione dalla nostra interfaccia a quella fornita dal codice esterno.

