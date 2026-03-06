# MATLAB — CheatSheet
___
- Purpose: minimal reference of core MATLAB language primitives (no examples)

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$
## File Operations
- Copy files: `copyfile('source.txt','destination.txt')` `copyfile('*.m','backup/')`
- Move files: `movefile('source.txt','destination.txt')` `movefile('*.m','newdir/')`
- Delete files: `delete('file.txt')` `delete('*.tmp')`
- Check if file exists: `exist('file.txt','file')` `isfile('data.mat')`
- Create directory: `mkdir('newdir')` `mkdir('parent','child')`
- Remove directory: `rmdir('dirname')` `rmdir('dirname','s')` (s=recursive)
- List directory contents: `dir` `dir('*.m')` `ls`
- Get current directory: `pwd`
- Change directory: `cd('dirname')` `cd ..`
- Read/write text files: `fileread('file.txt')` `filewrite('file.txt',text)`
- File information: `dir('file.txt')` `finfo = dir('file.txt')`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Data types
- Numeric arrays: double (default) `[1 2 3]`, single `single([1 2 3])`
- Integers: int8/16/32/64 `int8(5)`, uint8/16/32/64 `uint16(500)`
- Logical: true/false `logical(1)` `true`
- Characters and text: char vectors `'text'`, string scalars/arrays `"text"` `["a","b"]`
- Complex numbers: real + imaginary `1+2i` `complex(1,2)`
- Tables and timetables `table(x,y)` `timetable(t,data)`
- Cell arrays `{1, 'text', [1 2 3]}`
- Structures (scalars, arrays) `struct('field',value)` `s.field = value`
- Function handles `@sin` `@(x) x^2`
- Categorical arrays `categorical({'small','medium','large'})`
- Datetime, duration, calendarDuration `datetime('now')` `hours(5)` `calendarDuration(1,2,3)`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Operators — arithmetic
- Element-wise: .*, ./, .\, .^ `[1 2 3].*[4 5 6]`
- Matrix: *, /, \, ^ `A*B`
- Addition/subtraction: +, - `x+y`
- Transpose: .' (non-conjugate), ' (conjugate) `A.'` `A'`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Operators — relational and logical
- Relational: <, <=, >, >=, ==, ~= `x < 5`
- Logical: &, |, ~, xor `a & b` `~a`
- Short-circuit logical: &&, || (scalars only) `if a && b`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Precedence (high → low)
- Parentheses `(a + b)*c`
- Transpose/Power `A^2` `x.^2`
- Unary +, - `-x`
- Multiplication/Division `a*b` `a/b`
- Addition/Subtraction `a+b` `a-b`
- Relational `a < b`
- Element-wise logical &, | `a & b`
- Short-circuit logical &&, || `if a && b`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Control flow — conditionals
- if / elseif / else / end `if x > 0; disp('positive'); end`
- switch / case / otherwise / end `switch x; case 1; disp('one'); end`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Control flow — loops
- for / end `for i=1:10; disp(i); end`
- while / end `while x < 10; x = x+1; end`
- break (exit loop) `if x == 5; break; end`
- continue (skip to next iteration) `if x == 5; continue; end`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Functions and scripts
- Scripts: run in caller workspace `myscript.m`
- Functions: local workspace, inputs/outputs `function y = f(x)`
- Function files: primary + local functions `function local_f(x); end`
- Nested functions: access parent workspace `function nested(); end`
- Anonymous functions: inline function handles `f = @(x) x^2`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Arguments validation
- arguments block (size, type, validators) `arguments; x (1,:) double; end`
- varargin / varargout for variable arity `function f(varargin)`
- Name-value arguments `plot(x, y, 'Color', 'red')`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Arrays and dimensions
- Row-major display; column-major storage `[1 2; 3 4]`
- Size, length, numel for dimensions `size(A)` `length(v)` `numel(A)`
- Reshape, squeeze, permute for reordering `reshape(A, [2,3])` `squeeze(B)`
- Concatenation: horizontal, vertical, general cat `[A B]` `[A; B]` `cat(3,A,B)`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Vectors, matrices, and broadcasting
- Implicit expansion for compatible sizes `[1 2 3] + [10; 20]`
- Scalar expansion `A + 5`
- Element-wise operations default with dot operators `A.*B`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Scripts execution and path
- Current folder and MATLAB path determine visibility `cd('path')`
- addpath/rmpath/savepath manage search path `addpath('dir')`
- Packages (+pkg) namespace components `import pkg.func`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Names, files, and scope
- Function name must match file name (primary) `function myFunc in myFunc.m`
- Case-sensitive function names on some platforms `myFunc ≠ myfunc`
- Function precedence: variables > subfunctions > private > class > package > path

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## I/O and environment
- Workspace variables: assignin, evalin (use sparingly) `assignin('base','x',10)`
- Input/output: input, disp, fprintf `x = input('Enter value: ')`
- File operations: read/write with high-level functions `data = readtable('file.csv')`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## OOP basics
- Classdef with properties, methods `classdef MyClass; properties; x; end; end`
- Value vs handle classes `classdef MyClass < handle`
- Attributes: Sealed, Abstract, Static, Access `methods(Static=true)`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Vectorization and performance
- Prefer vectorized ops over loops when clear `sum(A)` vs `for loop`
- Preallocate arrays `A = zeros(100,100)`
- Use parfor (Parallel Toolbox) when applicable `parfor i=1:n`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Plotting (basics)
- High-level plotting functions create axes/figures `plot(x,y)`
- Graphics objects are handle-based `h = figure; h.Position = [0 0 500 500]`
- Hold and axis control layout and limits `hold on` `axis([0 1 0 1])`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Randomness
- rng to control seeds and streams `rng(42)` `rng('shuffle')`
- Pseudorandom number generation by distribution `rand(3,3)` `randn(1,10)`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Dates and times
- datetime for absolute timestamps `datetime('2022-08-01')`
- duration/calendarDuration for intervals `hours(2)` `calendarDuration(0,1,15)`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## String operations
- String arrays vs char arrays `str = "Hello"` vs `ch = 'Hello'`
- String concatenation `str1 + str2` or `strcat(str1, str2)`
- Char concatenation `[ch1 ch2]` or `[ch1, ch2]`
- String functions: contains, startsWith, endsWith `contains(str, "pattern")`
- Regular expressions with regexp `regexp(str, pattern)`
- String formatting with sprintf `sprintf("Value: %.2f", x)`
- String manipulation: strtrim, lower, upper `lower(str)` `strrep(str, "old", "new")`
- String conversion: num2str, str2num `num2str(123)` `str2double("123.45")`

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Line breaks and text formatting
- In scripts/functions: Use newline character `\n` in strings `fprintf('Line 1\nLine 2')`
- In Command Window: Press Enter key to execute and start new line
- In strings: Use newline function `str = "Line 1" + newline + "Line 2"`
- In display: Use disp with multiple calls `disp('Line 1'); disp('Line 2')`
- In formatted text: Use fprintf with newline `fprintf('Line 1\nLine 2\n')`
