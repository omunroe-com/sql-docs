---
title: "NCHAR (SQL Server PDW)"
ms.custom: na
ms.date: 07/27/2016
ms.reviewer: na
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 398481e5-2823-4aae-aff7-0e284626e682
caps.latest.revision: 5
author: BarbKess
---
# NCHAR (SQL Server PDW)
Returns the Unicode character with the specified integer code, as defined by the Unicode standard.  
  
![Topic link icon](../../mpp/sqlpdw/media/Topic_Link.gif "Topic_Link")[Syntax Conventions &#40;SQL Server PDW&#41;](../../mpp/sqlpdw/syntax-conventions-sql-server-pdw.md)  
  
## Syntax  
  
```  
NCHAR (integer_expression )  
```  
  
## Arguments  
*integer_expression*  
When the collation of the database does not contain the supplementary character (SC) flag, this is a positive whole number from 0 through 65535 (0 through 0xFFFF). If a value outside this range is specified, NULL is returned. For more information about supplementary characters, see [Collations &#40;SQL Server PDW&#41;](../../mpp/sqlpdw/collations-sql-server-pdw.md).  
  
When the collation of the database supports the supplementary character (SC) flag, this is a positive whole number from 0 through 1114111 (0 through 0x10FFFF). If a value outside this range is specified, NULL is returned.  
  
## Return Types  
**nchar(1)** when the default database collation does not support supplementary characters.  
  
**nvarchar(2)** when the default database collation supports supplementary characters.  
  
If the parameter *integer_expression* lies in the range 0 - 0xFFFF, only one character is returned. For higher values, NCHAR returns the corresponding surrogate pair. Do not construct a surrogate pair by using `NCHAR(<High surrogate>) + NCHAR(<Low Surrogate>)`. Instead, use a database collation that supports supplementary characters and then specify the Unicode codepoint for the surrogate pair. The following example demonstrates both the old style method of constructing a surrogate pair and the preferred method of specifying the Unicode codepoint.  
  
```  
CREATE DATABASE test COLLATE Finnish_Swedish_100_CS_AS_SC;  
DECLARE @d nvarchar(10) = N'𣅿';   
-– Old style method.  
SELECT NCHAR(0xD84C) + NCHAR(0xDD7F);   
  
-- Preferred method.   
SELECT NCHAR(143743);   
  
-- Alternative preferred method.  
SELECT NCHAR(UNICODE(@d));  
```  
  
## Examples  
  
### A. Using NCHAR and UNICODE  
The following example uses the `UNICODE` and `NCHAR` functions to print the `UNICODE` value and the `NCHAR` (Unicode character) of the second character of the `København` character string, and to print the actual second character, `ø`.  
  
```  
DECLARE @nstring nchar(8);  
SET @nstring = N'København';  
SELECT UNICODE(SUBSTRING(@nstring, 2, 1)),   
   NCHAR(UNICODE(SUBSTRING(@nstring, 2, 1)));  
GO  
```  
  
Here is the result set.  
  
```  
----------- -   
248         ø  
(1 row(s) affected)  
```  
  
### B. Using SUBSTRING, UNICODE, CONVERT, and NCHAR  
The following example uses the `SUBSTRING`, `UNICODE`, `CONVERT`, and `NCHAR` functions to print the character number, the Unicode character, and the UNICODE value of each character in the string `København`.  
  
```  
-- The @position variable holds the position of the character currently  
-- being processed. The @nstring variable is the Unicode character   
-- string to process.  
DECLARE @position int, @nstring nchar(9);  
-- Initialize the current position variable to the first character in   
-- the string.  
SET @position = 1;  
-- Initialize the character string variable to the string to process.  
-- Notice that there is an N before the start of the string. This   
-- indicates that the data following the N is Unicode data.  
SET @nstring = N'København';  
-- Print the character number of the position of the string you are at,   
-- the actual Unicode character you are processing, and the UNICODE   
-- value for this particular character.  
PRINT 'Character #' + ' ' + 'Unicode Character' + ' ' + 'UNICODE Value';  
WHILE @position <= DATALENGTH(@nstring)  
   BEGIN  
   SELECT @position,   
      NCHAR(UNICODE(SUBSTRING(@nstring, @position, 1))),  
      CONVERT(NCHAR(17), SUBSTRING(@nstring, @position, 1)),  
      UNICODE(SUBSTRING(@nstring, @position, 1))  
   SELECT @position = @position + 1  
   END;  
GO  
```  
  
Here is the result set.  
  
```  
Character # Unicode Character UNICODE Value  
  
----------- ---- ----------------- -----------   
1           K    K                 75  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
2           ø    ø                 248  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
3           b    b                 98  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
4           e    e                 101  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
5           n    n                 110  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
6           h    h                 104  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
7           a    a                 97  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
8           v    v                 118  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
9           n    n                 110  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
10          NULL                   NULL  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
11          NULL                   NULL  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
12          NULL                   NULL  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
13          NULL                   NULL  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
14          NULL                   NULL  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
15          NULL                   NULL  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
16          NULL                   NULL  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
17          NULL                   NULL  
  
(1 row(s) affected)  
  
----------- ---- ----------------- -----------   
18          NULL                   NULL  
  
(1 row(s) affected)  
```  
  
## See Also  
[Functions &#40;SQL Server PDW&#41;](../../mpp/sqlpdw/functions-sql-server-pdw.md)  
[Common Metadata Query Examples &#40;SQL Server PDW&#41;](../../mpp/sqlpdw/common-metadata-query-examples-sql-server-pdw.md)  
  