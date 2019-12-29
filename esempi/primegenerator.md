# PrimeGenerator

In questo esempio andremo a scrivere una classe che genera dei numeri primi fino a un massimo specificato dall'utente.

### Prima del refactoring

```text
<?php

/**
 * Questa classe genera dei numeri primi fino a un massimo specificato dall'utente.
 * L'algoritmo usato è il Setaccio di Eratostene.
 * Eratostene di Cirene fu il primo uomo a calcolare la circonferenza della Terra.
 * Noto anche per aver lavorato sui calendari con anni bisestili e aver gestito la libreria di Alessandria.
 * L'algoritmo è semplice. Dato un array di interi che partono da 2. Elimina tutti i multipli di 2.
 * Trova il prossimo intero disponibile ed elimina tutti i suoi multipli.
 * Ripeti fino ad aver superato la radice quadrata del valore massimo.
 */
class GeneratePrimes
{

    /**
     * @param int $maxValue è il limite della generazione
     *
     * @return int[]
     */
    public static function generate(int $maxValue): array
    {
        // L'unico caso valido
        if ($maxValue >= 2) {
            // Dichiarazioni
            $s = $maxValue + 1; // Dimensione dell'array
            $f = [];

            // Inizializza l'array a true
            for ($i = 0; $i < $s; $i++) {
                $f[$i] = true;
            }

            // Elimina i non-primi
            $f[0] = $f[1] = false;

            // Setaccio
            for ($i = 2; $i < sqrt($s) + 1; $i++) {
                // Se i è primo, elimina i suoi multipli
                if ($f[$i]) {
                    for ($j = 2 * $i; $j < $s; $j += $i) {
                        $f[$j] = false; // Un multiplo non può essere primo
                    }
                }
            }

            // Quanti primi ci sono?
            $count = 0;
            for ($i = 0; $i < $s; $i++) {
                if ($f[$i]) {
                    $count++;
                }
            }

            // Sposto i primi nel risultato
            $primes = [];
            for ($i = 0, $j = 0; $i < $s; $i++) {
                // Se è primo
                if ($f[$i]) {
                    $primes[$j++] = $i;
                }
            }

            return $primes;
        } else { // $maxValue < 2
            return [];
        }
    }
}
```

### Dopo il refactoring

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

