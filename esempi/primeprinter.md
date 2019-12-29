# PrimePrinter

In questo esempio vedremo l'applicazione del principio SRP \(Single Responsibility Principle\) per la creazione di un sistema che si occupa di generare e stampare, sotto forma di righe-colonne, i primi 1000 numeri primi.

### **PrimeGenerator**

```text
<?php

/**
 * Questa classe genera dei numeri primi fino a un massimo specificato dall'utente.
 * L'algoritmo usato è il Setaccio di Eratostene.
 * Dato un array di interi a partire da 2:
 * - trova il primo intero non eliminato ed elimina tutti i suoi multipli
 * - ripeti finché non vi sono più multipli nell'array
 */
class PrimeGenerator
{
    private static $crossedOut = [];
    private static $result = [];

    public static function generate(int $maxValue): array
    {
        if ($maxValue < 2) {
            return [];
        }

        self::uncrossIntegersUpTo($maxValue);
        self::crossOutMultiples();
        self::putUncrossedIntegersIntoResult();

        return self::$result;
    }

    private static function uncrossIntegersUpTo(int $maxValue): void
    {
        for ($i = 0; $i < $maxValue + 1; $i++) {
            self::$crossedOut[$i] = false;
        }
    }

    private static function crossOutMultiples(): void
    {
        $limit = self::determineIterationLimit();
        for ($i = 2; $i <= $limit; $i++) {
            if (self::notCrossed($i)) {
                self::crossOutMultiplesOf($i);
            }
        }
    }

    /**
     * Ogni multiplo nell'array ha un fattore primo che è minore o uguale alla radice
     * delle dimensioni dell'array, così che possiamo evitare di eliminare i multipli
     * dei numeri maggiori di tale radice
     * @return int
     */
    private static function determineIterationLimit(): int
    {
        return (int)sqrt(count(self::$crossedOut));
    }

    private static function notCrossed(int $i): bool
    {
        return self::$crossedOut[$i] === false;
    }

    private static function crossOutMultiplesOf(int $i): void
    {
        for ($multiple = 2 * $i; $multiple < count(self::$crossedOut); $multiple += $i) {
            self::$crossedOut[$multiple] = true;
        }
    }

    private static function putUncrossedIntegersIntoResult()
    {
        for ($j = 0, $i = 2; $i < count(self::$crossedOut); $i++) {
            if (self::notCrossed($i)) {
                self::$result[$j++] = $i;
            }
        }
    }
}
```

### **RowColumnPagePrinter**

```text
<?php

class RowColumnPagePrinter
{
    private $rowsPerPage;
    private $columnsPerPage;
    private $numbersPerPage;
    private $pageHeader;
    private $data;

    public function __construct(
        int $rowsPerPage,
        int $columnsPerPage,
        string $pageHeader,
        array $data
    ) {
        $this->rowsPerPage = $rowsPerPage;
        $this->columnsPerPage = $columnsPerPage;
        $this->numbersPerPage = $rowsPerPage * $columnsPerPage;
        $this->pageHeader = $pageHeader;
        $this->data = $data;
    }

    public function print(): void
    {
        $pageNumber = 1;
        for ($firstIndexOnPage = 0; $firstIndexOnPage < count($this->data); $firstIndexOnPage += $this->numbersPerPage) {
            $lastIndexOnPage = min(($firstIndexOnPage + $this->numbersPerPage) - 1, count($this->data) - 1);
            $this->printPageHeader($pageNumber);
            $this->printPage($firstIndexOnPage, $lastIndexOnPage);
            echo nl2br(PHP_EOL);
            $pageNumber++;
        }
    }

    private function printPageHeader(int $pageNumber): void
    {
        echo nl2br($this->pageHeader . ' --- Page ' . $pageNumber . PHP_EOL);
        echo nl2br(PHP_EOL);
    }

    private function printPage(int $firstIndexOnPage, int $lastIndexOnPage): void
    {
        $firstIndexOfLastRowOnPage = ($firstIndexOnPage + $this->rowsPerPage) - 1;
        for ($firstIndexInRow = $firstIndexOnPage; $firstIndexInRow <= $firstIndexOfLastRowOnPage; $firstIndexInRow++) {
            $this->printRow($firstIndexInRow, $lastIndexOnPage);
            echo nl2br(PHP_EOL);
        }
    }

    private function printRow(int $firstIndexInRow, int $lastIndexOnPage): void
    {
        for ($column = 0; $column < $this->columnsPerPage; $column++) {
            $index = $firstIndexInRow + ($column * $this->rowsPerPage);
            if ($index <= $lastIndexOnPage) {
                echo sprintf('%10d', $this->data[$index]);
            }
        }
    }
}
```

### **PrimePrinter**

```text
<?php
require_once('./PrimeGenerator.php');
require_once('./RowColumnPagePrinter.php');

class PrimePrinter
{
    private const ROWS_PER_PAGE = 10;
    private const COLUMNS_PER_PAGE = 4;

    private $rowColumnPagePrinter;

    public function __construct(int $numberOfPrimes)
    {
        $primes = PrimeGenerator::generate($numberOfPrimes);
        $this->rowColumnPagePrinter = new RowColumnPagePrinter(
            self::ROWS_PER_PAGE,
            self::COLUMNS_PER_PAGE,
            'The First ' . $numberOfPrimes . ' Prime Numbers',
            $primes
        );
    }

    public function __invoke(): void
    {
        $this->rowColumnPagePrinter->print();
    }
}
```

### **Index**

```text
<?php
require_once('./PrimePrinter.php');

$numberOfPrimes = 1000;
$primePrinter = new PrimePrinter($numberOfPrimes);
$primePrinter();
```



