# Nomi significativi

> There are only two hard things in Computer Science: cache invalidation and naming things.

I nomi sono dappertutto nel software \(variabili, funzioni, argomenti, classi, istanze, file, cartelle...\), per questo motivo è molto importante nominare bene le cose. Ecco delle semplici regole per scegliere dei buoni nomi.

## Usate nomi "parlanti"

I nomi devono essere parlanti. Già dal nome deve essere chiaro il perchè esiste, cosa fa e come viene utilizzato ciò che state nominando.

Esempio di cattivo nome:

```text
$d = 12;
$list1 = [];
$a = true;
```

Esempio di buon nome:

```text
$daysSinceCreation = 12;
$flaggedCells = [];
$isFlagged = true;
```

## Evitate la disinformazione

I nomi non devono essere ambigui e disinformativi. Se uso `$accountList` e non si tratta di una lista, ma di un gruppo di accounts, allora è meglio utilizzare `$accountGroup`.

## Adottate distinzioni sensate

Usare due nomi uguali che si distinguono solo per un valore posto alla fine del nome \(Es. `$customer1` e `$customer2`\) non è sensato. Piuttosto cercare di dare un nome che ben distingua le due cose \(Es. `$buyer` e `$seller`\). Anche nel nominare le classi si commette questo errore. Pensate a quante volte vi è capitato di trovare due classi che si chiamavano rispettivamente `Customer` e `CustomerData`. Qual è la differenza tra le due? Ecco perché è importante dare nomi sensati che ben distinguano le cose.

## Usate nomi pronunciabili

Evitare di dare nomi che siano difficili da pronunciare e di conseguenza da scrivere.

## Usate nomi ricercabili

I nomi devono essere anche facilmente ricercabili. Se usiamo nomi mono-lettera, quest'ultimi diventano impossibili da ricercare, poiché il risultato della ricerca darà migliaia di occorrenze. Evitare anche di usare magic numbers, meglio racchiuderli all'interno di costanti.

Valore difficile da ricercare:

```text
if ($status === 1) {
  // ...
}
```

Valore facile da ricercare:

```text
if ($status === Task::STATUS_CLOSED) {
  // ...
}
```

## Evitate le codifiche

I nomi codificati sono difficili da ricordare e capire, soprattutto per chi è stato neo-assunto.

## Suffissi

E' inutile aggiungere ai nomi di variabili, funzioni e interfacce dei suffissi. I moderni IDE, attraverso il Syntax Highlighting o tramite l'utilizzo di apposite icone, ci fornisco già delle informazioni che rendono inutile l'utilizzo di suffissi.

Nome di interfaccia da evitare:

```text
interface ShapeFactoryInterface
```

Nome di interfaccia corretto:

```text
interface ShapeFactory
```

## Nomi di classi

Il nome di una classe non deve essere un verbo. Inoltre evitate parole come `Manager`, `Processor`, `Data` o `Info` che spesso vengono utilizzate solo per distinguere i nomi e non aggiungono altre informazioni su ciò che fa la classe.

## Nomi di metodi

I nomi dei metodi devono contenere un verbo o una frase verbale, come `sendEmail`, `save`, `deleteCustomer`. Anche per i costruttori spesso è utile adottare un nome \([Named Constructor](http://blog.conorsmith.ie/named-constructors-in-php)\) per rendere più chiaro quest'ultimo.

Costruttore poco chiaro:

```text
$fulcrumPoint = new Complex(23.0);
```

Costruttore chiaro:

```text
$fulcrumPoint = Complex::fromRealNumber(23.0);
```

## Una parola, un concetto

Scegliere una parola per un determinato concetto astratto e continuarla ad utilizzarla per tutto il codice. Per esempio, non ha senso utilizzare `fetch`, `retrieve` e `get` per metodi equivalenti di classi differenti. Se ad esempio abbiamo utilizzato `add` per la somma di due valori, non usiamo lo stesso nome per inserire un elemento all'interno di una lista. Piuttosto utilizzare qualche altro nome tipo `insert` o `append`.

## Usate nomi appartenenti al dominio

Cercare di utilizzare nomi appartenenti al dominio dell'applicazione.

## Aggiungere un contesto al nome

Aggiungendo un contesto al nome, quest'ultimo acquisisce maggiore chiarezza. Se prendo il nome `state`, vi risulta chiaro a cosa io mi stia riferendo? Se lo rinominassi in `addrState`, quest'ultimo diventerebbe subito più chiaro. Spesso anche creare una classe aiuta a dare contesto ai nomi \(Es. la classe `Address` per rappresentare i campi di un indirizzo\).

## Non aggiungete contesti superflui

Non bisogna abusare dei contesti nei nomi se non ce n'è di bisogno. Il nome `CustomerAddress` è un buon nome per rappresentare l'istanza di una classe, ma diventa un cattivo nome nel momento in cui lo utilizzo come nome per una classe, in quanto è superfluo aggiungere il contesto `Customer` per rappresentare dei semplici campi di un indirizzo.

