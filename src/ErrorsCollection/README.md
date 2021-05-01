Collection of php errors
---
Details: http://php.net/manual/en/errorfunc.constants.php

Interesting:

 - http://php.net/manual/ru/migration70.incompatible.php
 - http://php.net/manual/ru/migration71.incompatible.php

On russian
---
■ Внешние ошибки - ошибки, в которых код ведет себя непредвиденным образом вследствие того, что часть программы не работает так, как ожидалось. Например, произошла ошибка при установке соединения с базой данных, когда для работы кода требуется успешная установка соединения. Такая ошибка является внешней.

■ Логические ошибки - ошибки, в которых конструкция кода в корне неверна либо вследствие ошибочной логики (программа просто не будет работать), либо из-за простейшей ошибки, такой как опечатка.

Эти две категории ошибок значительно отличаются друг от друга.

■ Внешние ошибки будут возникать всегда, независимо от того, насколько безошибочен разработанный код. Сами по себе они - не ошибки, поскольку являются внешними по отношению к программе.

■ Внешние ошибки, не учтенные в логике кода, могут оказаться дефектами программы. Например, слепое предположение о том, что подключение к базе данных всегда выполняется успешно, является ошибочным, поскольку приложение в таком случае, безусловно, не ответит корректно.

■ Логические ошибки гораздо труднее обнаруживать, чем внешние ошибки, поскольку их расположение по определению не известно. Однако чтобы выявить их, можно реализовать проверку непротиворечивости данных.

Иерархия всех исключений в виде дерева:
```
┌Throwable
├──Error
│  ├──ArithmeticError
│  ├──AssertionError
│  ├──DivisionByZeroError
│  ├──ParseError
│  ├──TypeError
│  └──ArgumentCountError
└──Exception
   ├──ErrorException
   ├──LogicException
   │  ├──BadFunctionCallException
   │  │  └──BadMethodCallException
   │  ├──DomainException
   │  ├──InvalidArgumentException
   │  ├──LengthException
   │  └──OutOfRangeException
   └──RuntimeException
      ├──OutOfBoundsException
      ├──OverflowException
      ├──RangeException
      ├──UnderflowException
      └──UnexpectedValueException
```