# ComparisonCompactor

In questo esempio andremo a realizzare e rifattorizzare un comparatore di stringhe. Date due stringhe differenti, come **abc** e **abbc**, mostra la differenza generando una stringa del tipo: **"expected: {ab\[\]c} but was: {ab\[b\]c}"**.

### Substring

```text
<?php

/**
 * @param string $str
 * @param int $start
 * @param int|null $end
 * @return false|string
 */
function substring(string $str, int $start, int $end = null)
{
    if ($end !== null) {
        return substr($str, $start, $end - $start);
    }

    return substr($str, $start);
}
```

### ComparisonCompactor \(prima del refactoring\)

```text
<?php
include_once('Substring.php');

class ComparisonCompactorOriginal
{
    private const ELLIPSIS = '...';
    private const DELTA_START = '[';
    private const DELTA_END = ']';

    /** @var int */
    private $_contextLength;

    /** @var string|null */
    private $_expected;

    /** @var string|null */
    private $_actual;

    /** @var int */
    private $_prefix;

    /** @var int */
    private $_suffix;

    /**
     * ComparisonCompactorOriginal constructor.
     * @param int $contextLength
     * @param string|null $expected
     * @param string|null $actual
     */
    public function __construct(int $contextLength, ?string $expected, ?string $actual)
    {
        $this->_contextLength = $contextLength;
        $this->_expected = $expected;
        $this->_actual = $actual;
    }

    /**
     * @param string|null $message
     * @return string
     */
    public function compact(string $message = null): string
    {
        if ($this->_expected === null || $this->_actual === null || $this->areStringsEqual()) {
            return trim(sprintf('%s expected: {%s} but was: {%s}', $message, $this->_expected, $this->_actual));
        }

        $this->findCommonPrefix();
        $this->findCommonSuffix();
        $expected = $this->compactString($this->_expected);
        $actual = $this->compactString($this->_actual);
        return trim(sprintf('%s expected: {%s} but was: {%s}', $message, $expected, $actual));
    }

    /**
     * @param string $source
     * @return string
     */
    private function compactString(string $source): string
    {
        $result =
            self::DELTA_START .
            substring($source, $this->_prefix, strlen($source) - $this->_suffix + 1) .
            self::DELTA_END;

        if ($this->_prefix > 0) {
            $result = $this->computeCommonPrefix() . $result;
        }

        if ($this->_suffix > 0) {
            $result = $result . $this->computeCommonSuffix();
        }

        return $result;
    }

    /**
     * @return void
     */
    private function findCommonPrefix(): void
    {
        $this->_prefix = 0;
        $end = min(strlen($this->_expected), strlen($this->_actual));
        for (; $this->_prefix < $end; $this->_prefix++) {
            if ($this->_expected[$this->_prefix] !== $this->_actual[$this->_prefix]) {
                break;
            }
        }
    }

    /**
     * @return void
     */
    private function findCommonSuffix(): void
    {
        $expectedSuffix = strlen($this->_expected) - 1;
        $actualSuffix = strlen($this->_actual) - 1;
        for (; $actualSuffix >= $this->_prefix && $expectedSuffix >= $this->_prefix; $actualSuffix--, $expectedSuffix--) {
            if ($this->_expected[$expectedSuffix] !== $this->_actual[$actualSuffix]) {
                break;
            }
        }
        $this->_suffix = strlen($this->_expected) - $expectedSuffix;
    }

    /**
     * @return string
     */
    private function computeCommonPrefix(): string
    {
        return
            ($this->_prefix > $this->_contextLength ? self::ELLIPSIS : '') .
            (substring($this->_expected, max(0, $this->_prefix - $this->_contextLength), $this->_prefix));
    }

    /**
     * @return string
     */
    private function computeCommonSuffix(): string
    {
        $end = min(strlen($this->_expected) - $this->_suffix + 1 + $this->_contextLength, strlen($this->_expected));
        return
            (substring($this->_expected, strlen($this->_expected) - $this->_suffix + 1, $end)) .
            (strlen($this->_expected) - $this->_suffix + 1 < strlen($this->_expected) - $this->_contextLength ? self::ELLIPSIS : '');
    }

    /**
     * @return bool
     */
    private function areStringsEqual(): bool
    {
        return $this->_expected === $this->_actual;
    }
}
```

### ComparisonCompactor \(dopo il refactoring\)

```text
<?php
include_once('Substring.php');

class ComparisonCompactorFinal
{
    private const ELLIPSIS = '...';
    private const DELTA_START = '[';
    private const DELTA_END = ']';

    /** @var int */
    private $contextLength;

    /** @var string|null */
    private $expected;

    /** @var string|null */
    private $actual;

    /** @var int */
    private $prefixLength;

    /** @var int */
    private $suffixLength;

    /**
     * ComparisonCompactorFinal constructor.
     * @param int $contextLength
     * @param string|null $expected
     * @param string|null $actual
     */
    public function __construct(int $contextLength, ?string $expected, ?string $actual)
    {
        $this->contextLength = $contextLength;
        $this->expected = $expected;
        $this->actual = $actual;
    }

    /**
     * @param string|null $message
     * @return string
     */
    public function formatCompactedComparison(string $message = null): string
    {
        $compactExpected = $this->expected;
        $compactActual = $this->actual;

        if ($this->shouldBeCompacted()) {
            $this->findCommonPrefixAndSuffix();
            $compactExpected = $this->compact($this->expected);
            $compactActual = $this->compact($this->actual);
        }

        return trim(sprintf('%s expected: {%s} but was: {%s}', $message, $compactExpected, $compactActual));
    }

    /**
     * @return bool
     */
    private function shouldBeCompacted(): bool
    {
        return !$this->shouldNotBeCompacted();
    }

    /**
     * @return bool
     */
    private function shouldNotBeCompacted(): bool
    {
        return $this->expected === null || $this->actual === null || $this->expected === $this->actual;
    }

    /**
     * @return void
     */
    private function findCommonPrefixAndSuffix(): void
    {
        $this->findCommonPrefix();
        $this->suffixLength = 0;
        for (; !$this->suffixOverlapsPrefix(); $this->suffixLength++) {
            if ($this->charFromEnd($this->expected) !== $this->charFromEnd($this->actual)) {
                break;
            }
        }
    }

    /**
     * @return void
     */
    private function findCommonPrefix(): void
    {
        $this->prefixLength = 0;
        $end = min(strlen($this->expected), strlen($this->actual));
        for (; $this->prefixLength < $end; $this->prefixLength++) {
            if ($this->expected[$this->prefixLength] !== $this->actual[$this->prefixLength]) {
                break;
            }
        }
    }

    /**
     * @return bool
     */
    private function suffixOverlapsPrefix(): bool
    {
        return (
            strlen($this->actual) - $this->suffixLength <= $this->prefixLength ||
            strlen($this->expected) - $this->suffixLength <= $this->prefixLength
        );
    }

    /**
     * @param string $s
     * @return string
     */
    private function charFromEnd(string $s): string
    {
        return $s[strlen($s) - $this->suffixLength - 1];
    }

    /**
     * @param string $s
     * @return string
     */
    private function compact(string $s): string
    {
        return (
            $this->startEllipsis() .
            $this->startContext() .
            self::DELTA_START .
            $this->delta($s) .
            self::DELTA_END .
            $this->endContext() .
            $this->endEllipsis()
        );
    }

    /**
     * @return string
     */
    private function startEllipsis(): string
    {
        return $this->prefixLength > $this->contextLength ? self::ELLIPSIS : '';
    }

    /**
     * @return string
     */
    private function startContext(): string
    {
        $contextStart = max(0, $this->prefixLength - $this->contextLength);
        $contextEnd = $this->prefixLength;
        return substring($this->expected, $contextStart, $contextEnd);
    }

    /**
     * @param string $s
     * @return string
     */
    private function delta(string $s): string
    {
        $deltaStart = $this->prefixLength;
        $deltaEnd = strlen($s) - $this->suffixLength;
        return substring($s, $deltaStart, $deltaEnd);
    }

    /**
     * @return string
     */
    private function endContext(): string
    {
        $contextStart = strlen($this->expected) - $this->suffixLength;
        $contextEnd = min($contextStart + $this->contextLength, strlen($this->expected));
        return substring($this->expected, $contextStart, $contextEnd);
    }

    /**
     * @return string
     */
    private function endEllipsis(): string
    {
        return $this->suffixLength > $this->contextLength ? self::ELLIPSIS : '';
    }
}
```

### Index

```text
<?php
include_once('ComparisonCompactorOriginal.php');
include_once('ComparisonCompactorFinal.php');

$contextLength = 2;
$expected = 'abc';
$actual = 'abbc';

$comparisonOriginal = new ComparisonCompactorOriginal($contextLength, $expected, $actual);
print_r(nl2br('<b>Original: </b>' . PHP_EOL . $comparisonOriginal->compact()));

print_r(nl2br(PHP_EOL . PHP_EOL));

$comparisonFinal = new ComparisonCompactorFinal($contextLength, $expected, $actual);
print_r(nl2br('<b> Final: </b>' . PHP_EOL . $comparisonFinal->formatCompactedComparison()));
```



