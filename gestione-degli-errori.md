# Gestione degli errori

La gestione degli errori è certamente importante quando sviluppiamo, ma spesso essa sembra quasi "dominare" il nostro codice, tanto da offuscarlo e renderlo illegibile.

### Usate le eccezioni al posto dei codici d'errore

L'utilizzo delle eccezioni al posto dei codici d'errore migliora di gran lunga la leggibilità del nostro codice.

Utilizzo dei codici d'errore:

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

Utilizzo delle eccezioni:

```text
try {
    $this->userRepository->delete($user);
    $this->mailer->send($this->email);
} catch(Exception $e) {
    print_r($e->getMessage());
}
```

### Definite le classi per le eccezioni in base alle esigenze

Quando definiamo una classe per un'eccezione, dobbiamo pensare al modo in cui essa può essere cachata e utilizzata. Spesso un'unica classe per eccezioni è sufficiente per distinguere una determinata area/situazione nel codice.

Utilizzo superfluo delle classi per le eccezioni:

```text
if (empty($product->getName())) {
    throw new InvalidProductNameException();
}

if ($product->getPrice() < 0) {
    throw new InvalidProductPriceException();
}

if (empty($product->getUrlKey())) {
    throw new InvalidProductUrlKeyException();
}
```

Utilizzo corretto delle classi per le eccezioni:

```text
if ($product->isValid() === false) {
    throw new InvalidProductException($product);
}
```

### Definite il flusso "normale"

Solitamente, quando si verifica un'eccezione, la gestiamo loggando l'errore, mostrando eventualmente un messaggio all'utente e dopodichè proseguiamo con la normale esecuzione del codice. Tuttavia ci sono casi in cui vogliamo gestire l'eccezione in maniera "speciale":

```text
try {
    $expenses = $expenseReportDAO->getMeals($employee->getId());
    $total += $expenses->getTotal();
} catch (MealExpensesNotFound $e) {
    $total += $this->getMealPerDiem();
}

class ExpenseReportDAO {
    public function getMeals(int $employeeId): MealExpenses {
        // recupera i pasti consumati tramite l'id del dipendente
        if (empty($meals)) {
            throw new MealExpensesNotFound();
        }
    }
}
```

In questo caso, se il pasto viene consumato, diviene parte del totale, altrimenti il dipendente riceve una somma per tale giorno. Questo codice diventa più semplice da leggere, se gestiamo il caso speciale presente nel blocco `catch` attraverso una classe a parte \(SPECIAL CASE PATTERN\).

```text
$expenses = $expenseReportDAO->getMeals($employee->getId());
$total += $expenses->getTotal();

class ExpenseReportDAO {
    public function getMeals(int $employeeId): MealExpenses {
        // recupera i pasti consumati tramite l'id del dipendente
        if (empty($meals)) {
            return new PerDiemMealExpenses($employee);
        }
    }
}

class PerDiemMealExpenses implements MealExpenses {
    public function getTotal(): float {
        // restituisce il totale per giorno
    }
}
```

### Non restituite null

Non restituite `null` se qualcosa va storto, piuttosto lanciate un'eccezione o restituite un oggetto Special Case come mostrato nell'esempio precedente. L'utilizzo di `null` è sconsigliato perché aumenta il carico di lavoro del chiamante che deve verificare se il valore restituito è `null` o meno, portando di conseguenza ad avere condizioni `if` annidate.

Prima del refactoring:

```text
$employees = $employeeRepository->getEmployees();
$totalPay = 0;

if ($employees !== null) {
    foreach ($employees as $employee) {
        $totalPay += $employee->getPay();
    }
}
```

Dopo il refactoring:

```text
$employees = $employeeRepository->getEmployees();
$totalPay = 0;

foreach ($employees as $employee) {
    $totalPay += $employee->getPay();
}

class EmployeeRepository {
    public function getEmployees() {
        // recupera la lista dei dipendenti
        if (empty($employees)) {
            return [];
        }
    }
}
```

### Non passate null

E' sconsigliato passare `null` come argomento \(salvo casi particolari\), perché questo obbliga al metodo chiamato di verificare il caso in cui l'argomento è `null` e il caso in cui non lo è.

