# Unit test

Affinché siamo sicuri che il nostro codice funzioni, quest'ultimo deve essere sottoposto a test. Tuttavia i test non devono rappresentare una massa di codice usa-e-getta che si occupa solamente di testare il nostro codice. Essi devono essere curati, così come deve essere curato il nostro codice di produzione.

### Le tre leggi dello sviluppo TDD

Lo sviluppo TDD \(Test-Driven Development\) ci chiede di scrivere per primi gli unit test, prima ancora di scrivere il codice effettivo del software. Il principio dello sviluppo TDD si può riassumere in tre leggi:

* Non bisogna scrivere il codice di produzione finché non si è scritto prima uno unit test
* Non bisogna procedere con gli unit test più di quanto sia sufficiente per ottenere un fallimento
* Non bisogna procedere con il codice di produzione più di quanto sia sufficiente per passare gli attuali unit test

Da queste leggi si può capire come unit test e codice di produzione vanno scritti **insieme**. Se lavoriamo in questo modo, i test copriranno tutto il codice di produzione.

### Curate la pulizia dei test

Il codice dei test è importante quanto il codice di produzione. Avere dei test scritti bene e che rispettono gli stessi standard qualitativi del codice di produzione, ci aiuta ad apportare delle modifiche ai test quando ce n'è di bisogno. Il motivo principale è che i test devono cambiare insieme al codice di produzione. Più scadenti sono i test, più sarà difficile modificarli.

### I test garantiscono le "...bilità"

Gli unit test garantiscono la flessibilità, manutenibilità e riutilizzabilità del nostro codice. Se abbiamo dei test scritti bene, non avremo mai paura di modificare il nostro codice! Maggiore è la copertura dei test, minora è la paura. Se i test sono scadenti, ciò ci indurrà a non modificare e migliorare il nostro codice. Peggiori sono i test e peggiore diventà il nostro codice.

### Un doppio standard

Il codice di test deve sottostare ad un insieme di standard progettuali differente rispetto al codice di produzione. Deve essere chiaro, semplice ed espressivo, ma non deve essere altrettanto efficiente come il codice di produzione. Questo perché il codice di test viene eseguito in un ambiente per l'appunto di test. Dove le risorse di memoria RAM e CPU sono differenti rispetto all'ambiente di produzione.

### Una sola richiesta per test

Vi è una scuola di pensiero che dice che ogni funzione di test deve avere una e una sola istruzione `assert`. Questo aiuta a scrivere dei test chiari e concisi. Tuttavia a volte questa regola diventa difficile da applicare, in quanto richiederebbe la strutturazione di classi troppo complesse. Pertanto consiglio di ridurre al minimo il numero di `assert` presenti all'interno di una funzione di test, ma senza vincolarsi troppo seguendo questa regola.

### Un unico concetto per ogni test

Per ogni funzione di test che scriviamo, dobbiamo sottoporre a test un unico concetto. Questo ci evita di realizzare lunghe funzioni che applicano tanti test disparati.

### F.I.R.S.T

Un test è "pulito" se obbedisce a queste cinque regole che formano l'acronimo F.I.R.S.T

* _Fast_: I test dovrebbero essere veloci da eseguire. Se i test sono lenti, eviteremo di eseguirli con la giusta frequenza.
* _Independent_: I test non dovrebbero dipendere l'uno dall'altro. Dovremmo essere in grado di eseguire i test in qualsiasi ordine.
* _Repeatable_: I test dovrebbero essere ripetibili in qualsiasi ambiente.
* _Self-Validating_: I test dovrebbero essere autoconclusivi e avere un output booleano. Il test o ha successo o fallisce.
* _Timely_: I test devono essere aggiornati. Gli unit test dovrebbero essere scritti appena prima del codice di produzione cui fanno riferimento e devono cambiare insieme al codice di produzione.

