# Args

In questo esempio vedremo la costruzione della classe `Args`, la quale, data in input una stringa di formattazione e degli argomenti, e poi possibile interrogarla chiedendole il valore degli argomenti.

### **ArgumentMarshaler**

```text
<?php

interface ArgumentMarshaler
{
    /**
     * @param ArgumentMarshaler $am
     * @return mixed
     */
    public static function getValue(ArgumentMarshaler $am);

    /**
     * @param mixed $currentArgument
     * @return void
     */
    public function set($currentArgument): void;
}
```

### **BooleanArgumentMarshaler**

```text
<?php
include_once('ArgumentMarshaler.php');

class BooleanArgumentMarshaler implements ArgumentMarshaler
{
    /** @var bool */
    private $value = false;

    /**
     * @param ArgumentMarshaler $am
     * @return bool
     */
    public static function getValue(ArgumentMarshaler $am)
    {
        if ($am instanceof BooleanArgumentMarshaler) {
            return $am->value;
        }

        return false;
    }

    /**
     * {@inheritDoc}
     */
    public function set($currentArgument): void
    {
        $this->value = true;
    }
}
```

### **FloatArgumentMarshaler**

```text
<?php
include_once('ArgumentMarshaler.php');

class FloatArgumentMarshaler implements ArgumentMarshaler
{
    public const SYMBOL = '##';

    /** @var float */
    private $value = 0.0;

    /**
     * @param ArgumentMarshaler $am
     * @return float
     */
    public static function getValue(ArgumentMarshaler $am)
    {
        if ($am instanceof FloatArgumentMarshaler) {
            return $am->value;
        }

        return 0.0;
    }

    /**
     * {@inheritDoc}
     */
    public function set($currentArgument): void
    {
        $this->value = (float)$currentArgument;
    }
}
```

### **IntegerArgumentMarshaler**

```text
<?php
include_once('ArgumentMarshaler.php');

class IntegerArgumentMarshaler implements ArgumentMarshaler
{
    public const SYMBOL = '#';

    /** @var int */
    private $value = 0;

    /**
     * @param ArgumentMarshaler $am
     * @return int
     */
    public static function getValue(ArgumentMarshaler $am)
    {
        if ($am instanceof IntegerArgumentMarshaler) {
            return $am->value;
        }

        return 0;
    }

    /**
     * {@inheritDoc}
     */
    public function set($currentArgument): void
    {
        $this->value = (int)$currentArgument;
    }
}
```

### **StringArgumentMarshaler**

```text
<?php
include_once('ArgumentMarshaler.php');

class StringArgumentMarshaler implements ArgumentMarshaler
{
    public const SYMBOL = '*';

    /** @var string */
    private $value = '';

    /**
     * @param ArgumentMarshaler $am
     * @return string
     */
    public static function getValue(ArgumentMarshaler $am)
    {
        if ($am instanceof StringArgumentMarshaler) {
            return $am->value;
        }

        return '';
    }

    /**
     * {@inheritDoc}
     */
    public function set($currentArgument): void
    {
        $this->value = (string)$currentArgument;
    }
}
```

### **ArgumentMarshalerCollection**

```text
<?php
include_once('ArgsException.php');
include_once('ArgumentMarshaler.php');

class ArgumentMarshalerCollection
{
    /** @var array */
    private $array = [];

    /**
     * @param string $elementId
     * @return ArgumentMarshaler
     * @throws ArgsException
     */
    public function get(string $elementId): ArgumentMarshaler
    {
        if (!isset($this->array[$elementId])) {
            throw new ArgsException(ArgsException::UNEXPECTED_ARGUMENT, $elementId);
        }

        return $this->array[$elementId];
    }

    /**
     * @param string $elementId
     * @param ArgumentMarshaler $am
     * @return void
     */
    public function put(string $elementId, ArgumentMarshaler $am): void
    {
        $this->array[$elementId] = $am;
    }
}
```

### **ArgsException**

```text
<?php

class ArgsException extends Exception
{
    public const INVALID_ARGUMENT_FORMAT = 1;
    public const UNEXPECTED_ARGUMENT = 2;
    public const INVALID_ARGUMENT_NAME = 3;

    /** @var string|null */
    private $argumentId;

    /** @var string|null */
    private $parameter;

    /**
     * ArgsException constructor.
     * @param int $code
     * @param string|null $argumentId
     * @param string|null $parameter
     */
    public function __construct(int $code, string $argumentId = null, string $parameter = null)
    {
        $this->code = $code;
        $this->argumentId = $argumentId;
        $this->parameter = $parameter;
        parent::__construct($this->formatErrorMessage(), $code);
    }

    /**
     * @return string|null
     */
    public function getArgumentId(): ?string
    {
        return $this->argumentId;
    }

    /**
     * @return string|null
     */
    public function getParameter(): ?string
    {
        return $this->parameter;
    }

    /**
     * @return string
     */
    private function formatErrorMessage(): string
    {
        switch ($this->code) {
            case self::INVALID_ARGUMENT_FORMAT:
                return sprintf('`%s` is not a valid argument format.', $this->parameter);
            case self::UNEXPECTED_ARGUMENT:
                return sprintf('Argument -%s unexpected.', $this->argumentId);
            case self::INVALID_ARGUMENT_NAME:
                return sprintf('`%s` is not a valid argument name.', $this->argumentId);
            default:
                return 'TILT: Should not get here.';
        }
    }
}
```

### **Args**

```text
<?php
include_once('ArgsException.php');
include_once('ArgumentMarshaler/ArgumentMarshalerCollection.php');
include_once('ArgumentMarshaler/BooleanArgumentMarshaler.php');
include_once('ArgumentMarshaler/FloatArgumentMarshaler.php');
include_once('ArgumentMarshaler/IntegerArgumentMarshaler.php');
include_once('ArgumentMarshaler/StringArgumentMarshaler.php');

class Args
{
    private const SCHEMA_SEPARATOR = ',';

    /** @var ArgumentMarshalerCollection */
    private $marshalers;

    /** @var string[] */
    private $args = [];

    /** @var string[] */
    private $argsFound = [];

    /**
     * Args constructor.
     * @param string $schema
     * @param string[] $args
     * @throws ArgsException
     */
    public function __construct(string $schema, array $args)
    {
        $this->marshalers = new ArgumentMarshalerCollection();
        $this->args = $args;
        $this->parseSchema($schema);
        $this->parseArgumentStrings();
    }

    /**
     * @param string $schema
     * @return void
     * @throws ArgsException
     */
    private function parseSchema(string $schema): void
    {
        $elements = explode(self::SCHEMA_SEPARATOR, $schema);
        foreach ($elements as $element) {
            if (strlen($element) > 0) {
                $this->parseSchemaElement(trim($element));
            }
        }
    }

    /**
     * @param string $element
     * @return void
     * @throws ArgsException
     */
    private function parseSchemaElement(string $element): void
    {
        $elementId = $element[0];
        $elementTail = substr($element, 1);
        $this->validateSchemaElementId($elementId);
        if (strlen($elementTail) === 0) {
            $this->marshalers->put($elementId, new BooleanArgumentMarshaler());
        } elseif ($elementTail === StringArgumentMarshaler::SYMBOL) {
            $this->marshalers->put($elementId, new StringArgumentMarshaler());
        } elseif ($elementTail === IntegerArgumentMarshaler::SYMBOL) {
            $this->marshalers->put($elementId, new IntegerArgumentMarshaler());
        } elseif ($elementTail === FloatArgumentMarshaler::SYMBOL) {
            $this->marshalers->put($elementId, new FloatArgumentMarshaler());
        } else {
            throw new ArgsException(ArgsException::INVALID_ARGUMENT_FORMAT, $elementId, $elementTail);
        }
    }

    /**
     * @param string $elementId
     * @return void
     * @throws ArgsException
     */
    private function validateSchemaElementId(string $elementId): void
    {
        if (!ctype_alpha($elementId)) {
            throw new ArgsException(ArgsException::INVALID_ARGUMENT_NAME, $elementId);
        }
    }

    /**
     * @return void
     * @throws ArgsException
     */
    private function parseArgumentStrings(): void
    {
        foreach ($this->args as $arg) {
            if (substr($arg, 0, 1) === '-') {
                $this->parseArgumentCharacters(substr($arg, 1));
            }
        }
    }

    /**
     * @param string $argChars
     * @return void
     * @throws ArgsException
     */
    private function parseArgumentCharacters(string $argChars): void
    {
        for ($i = 0; $i < strlen($argChars); $i++) {
            $this->parseArgumentCharacter($argChars[$i]);
        }
    }

    /**
     * @param string $argChar
     * @return void
     * @throws ArgsException
     */
    private function parseArgumentCharacter(string $argChar): void
    {
        $am = $this->marshalers->get($argChar);
        $am->set(next($this->args));
        $this->argsFound[] = $argChar;
    }

    /**
     * @param string $arg
     * @return bool
     */
    public function has(string $arg): bool
    {
        return in_array($arg, $this->argsFound);
    }

    /**
     * @param string $arg
     * @return bool
     * @throws ArgsException
     */
    public function getBoolean(string $arg): bool
    {
        return BooleanArgumentMarshaler::getValue($this->marshalers->get($arg));
    }

    /**
     * @param string $arg
     * @return string
     * @throws ArgsException
     */
    public function getString(string $arg): string
    {
        return StringArgumentMarshaler::getValue($this->marshalers->get($arg));
    }

    /**
     * @param string $arg
     * @return int
     * @throws ArgsException
     */
    public function getInteger(string $arg): int
    {
        return IntegerArgumentMarshaler::getValue($this->marshalers->get($arg));
    }

    /**
     * @param string $arg
     * @return float
     * @throws ArgsException
     */
    public function getFloat(string $arg): float
    {
        return FloatArgumentMarshaler::getValue($this->marshalers->get($arg));
    }
}
```

### **Index**

```text
<?php
include_once('Args.php');

try {
    $args = new Args('l,p#,d*,x##', ['-lpdx', true, 42, 'param', 16.5]);

    var_dump($args->getBoolean('l'));
    var_dump($args->getInteger('p'));
    var_dump($args->getString('d'));
    var_dump($args->getFloat('x'));
} catch (ArgsException $e) {
    var_dump($e->getMessage());
}
```



