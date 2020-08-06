
<div id="mybrand" align="center">
    <img src="https://i.imgur.com/s50m6R0.png" height="200">
    <br><br>
    <a><img alt="undefined" src="https://img.shields.io/badge/release-v3.0.1-yellow?style=for-the-badge"></a>
    <a><img alt="undefined" src="https://img.shields.io/badge/license-MIT-brightgreen?style=for-the-badge"></a>
    <a><img alt="undefined" src="https://img.shields.io/badge/-python-9cf?style=for-the-badge&logo=python"></a>
    <br><br><br>
</div>

Cryfind is a tool to help you find crypto signatures in binary.

## Usage

```
Usage: cryfind [-m METHODS] [-y] <filename>

-h --help           Show this screen
-m METHODS          Methods to be used, could be : constant,stackstrings,api,peimport or all [default: constant,api]
-y --summary        Only show summary
```

You can also compile all constants to yara rules using `crygen`.

```
Usage: crygen [-e ENCODING]

-h --help           Show this screen
-e ENCODING         Encoding, could be : fullword,qword,dword or all [default: all]
```

## Install

Python >= 3

```
python setup.py install
```

## Python API

```shell
pydoc crylib
```

```python
Help on package crylib:

NAME
    crylib

PACKAGE CONTENTS
    base
    constants (package)
    findapi
    findconst
    peimport
    stackstrings

FUNCTIONS
    constants_to_rules(constants, sizes=['fullword'])
        Convert constants to yara rules
        
        Parameters
        ----------
        constant: Dict
            Constant you want to convert.
        sizes: List[str]
            Sizes of word, can be 'fullword', 'qword', or 'dword'. Defaults to ['fullword'].
        
        Returns
        -------
        str
        
        Examples
        --------
        >>> constants_to_rules([{'name': 'test', 'value': b'abcd'}])
        rule cry_1 {
            meta:
                id = 0
                name = "test"
                length = 4
            strings:
                $c_0_0_0 = { 61626364 }
                $c_0_0_1 = { 64636261 }
            condition:
                (any of ($c_0_0_*))
        }
    
    find_api(binary, apis)
        Find crypto api names in binary
        
        Parameters
        ----------
        binary: bytes
            Target binary to search for.
        apis: List[Dict]
            API names you want to find.
        
        Returns
        -------
        List[Dict]
        
        Examples
        --------
        >>> results = find_api(b'......A_SHAFinal.....', [{'name': 'advapi32.dll', 'functions': ['A_SHAFinal', 'A_SHAInit']}])
        >>> print(results[0])
        {'name': 'advapi32.dll', 'functions': [{'name': 'A_SHAFinal', 'address': 6}]}
    
    find_const(binary, constants, summary=False)
        Find constants in binary
        
        Parameters
        ----------
        binary: bytes
            Target binary to search for.
        constants: List[Dict]
            Constants you want to find.
        summary: boolean (optional)
            Only get the name of constants. Defaults to False.
        
        Returns
        -------
        List[Result]
        
        Examples
        --------
        >>> results = find_const(b'abcd', [{'name': 'test', 'value': b'abcd'}])
        >>> print(results[0])
        [+] test
            - fullword
                | [0] 61626364 (big): 0x0
    
    pe_import(binary)
        Find api names in PE import tables
        
        Parameters
        ----------
        binary: bytes
            Target binary to search for.
        
        Returns
        -------
        List[Dict]
        
        Examples
        --------
        >>> results = pe_import(open('./test', 'wb').read())
        >>> print(results[0])
        {'dll': 'advapi32.dll', 'function': 'CryptAcquireContextA'}
    
    stackstrings(binary)
        Dump the stack from emulation
        
        Parameters
        ----------
        binary: bytes
            Target binary.
        
        Returns
        -------
        bytes

DATA
    __all__ = ['find_const', 'find_api', 'stackstrings', 'pe_import', 'con...

FILE
    /Users/oalieno/cryfind/crylib/__init__.py
```

## Constants

Cryptographic constants, or you can also call it signatures or patterns.

* I merge the following crypto constants signatures into my database
    * KryptoAnalyzer
        - http://www.dcs.fmph.uniba.sk/zri/6.prednaska/tools/PEiD/plugins/kanal.htm
    * Polichombr
        - https://github.com/ANSSI-FR/polichombr/blob/dev/polichombr/analysis_tools/AnalyzeIt.rb#L26