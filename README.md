## Steps to reproduce the problem

For reproduce use example repo: https://github.com/liborm85/phpstan-excludepaths-bug with folders/files structure and neon config files and follow the steps below. Where is 1 error in `src/thirdparty/ThirdpartyClass.php` file and 1 error in `src/broken/broken.php` file.

### 0. PHPStan config file `phpstan0.neon` - Work OK

Nothing is ignored.

#### Correct result with 2 errors:
```
 ------ --------------------------------------------------------------------
  Line   broken\broken.php
 ------ --------------------------------------------------------------------
  3      Function brokenfile not found.
         💡 Learn more at https://phpstan.org/user-guide/discovering-symbols
 ------ --------------------------------------------------------------------

 ------ ----------------------------------------------------------------------
  Line   thirdparty\ThirdpartyClass.php
 ------ ----------------------------------------------------------------------
  6      Access to an undefined property ThirdpartyClass::$undefinedVariable.
 ------ ----------------------------------------------------------------------
```

### 1. PHPStan config file `phpstan1.neon` - Work OK

Folder `src/thirdparty` is excluded from analyse.

#### Correct result with 1 error:
```
 ------ --------------------------------------------------------------------
  Line   broken\broken.php
 ------ --------------------------------------------------------------------
  3      Function brokenfile not found.
         💡 Learn more at https://phpstan.org/user-guide/discovering-symbols
 ------ --------------------------------------------------------------------
```

### 2. PHPStan config file `phpstan2.neon` - Work WRONG

Folder `src/thirdparty` is excluded from analyse.

Folder `src/broken` is excluded from analyseAndScan.

`phpstan2.neon` file:
```
  excludePaths:
    analyse:
      - src/thirdparty
    analyseAndScan:
      - src/broken
```

I expect exclude `src/thirdparty` folder from analyse (only discovering symbols will be performed) and `src/broken` from analyse and scan completely.

#### Actual result:
```
 ------ ----------------------------------------------------------------------
  Line   thirdparty\ThirdpartyClass.php
 ------ ----------------------------------------------------------------------
  6      Access to an undefined property ThirdpartyClass::$undefinedVariable.
 ------ ----------------------------------------------------------------------
```

#### Expected result:

```
[OK] No errors
```

I expect code analyse in `src/thirdparty` folder, but now is skipped.