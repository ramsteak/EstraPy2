---
title: General syntax
parent: Commands
nav_order: 10
permalink: /commands/general-syntax/
math: katex
---

# General syntax

All EstraPy input files must follow a consistent syntax. When an input file is invoked, it is first parsed, and any syntax error will be reported. If parsing succeeds, the file will be executed.

## Header

The first line of every input file must declare the EstraPy version it was written for. If the file requires a newer version than the one installed, the program will prompt you to update. If the file uses an older version, the program will attempt to upgrade the input file to the current syntax.

```sh
# version: 1.0.1
```

## Directives

The first section of the file, right after the version line, is dedicated to directives, which are preceded by `%`. These are not data-processing commands. They are processed at parsing time, and control the working environment.

### Clear

```sh
% clear
```

This directive clears the output folder, removing the files generated by previous runs of the program. This way, the log file only contains the latest run of the program, and avoids confusion caused by leftover files.

### Define

```sh
% define <variable> <value>
```

The `% define` directive sets a parsing-time variable, for writing convenience. The value will be substituted in the input file, wherever the placeholder `${variable}` or `%variable%` appears. For example, this allows you to define a variable for the k-weighting factor, which can automatically be input into commands and plot labels or titles.

## Comments

A line is treated as a comment if its first character is `#`. Comments are ignored during parsing.

## Commands

The main content of the file consists of commands. Commands start at the beginning of a line and follow shell-command syntax. The specific options for each command are outlined in the following sections.

Commands can span multiple lines for better readability, making complex commands easier to read and maintain. In order to split a command into multiple parts, all continuation lines must start with at least one space, as in the following example.

```sh
plot --fig 1:1.2
     --xlabel "$Wavevector\ [Å^{-1}]$"
     --ylabel "$Signal\ intensity$"
     --title  "$\chi^{exp}(k)$"
```

Lines of a single command cannot be separated by comments. These two examples would lead to syntax errors, as the parser is unable to correctly associate the second line with the original command due to the formatting or comment placement.

```sh
plot --fig 1:1.2
--xlabel "$Wavevector\ [Å^{-1}]$"
```

```sh
plot --fig 1:1.2
# Set the label for the x axis
     --xlabel "$Wavevector\ [Å^{-1}]$"
```

## Number and unit specification

Some commands share the same syntax for some arguments, such as ranges and numbers with units. Some commands can automatically infer the axis to use from the specified units.

The units available in EstraPy are as follows:

- `eV`: electronvolts (eV)
- `k`: wavevector units (Å⁻¹)
- `A`: distance units (Å)

It also supports SI prefixes for these units, e.g. `15keV` is equivalent to `15000eV`.

In order to specify a data range, you can either specify a fixed range with numbers, or you can freely combine together them with the following aliases:

|Bound|Explanation|
|--|--|
|<span class="nowrap">`.. ..`</span>|Resolves to $$-\infty$$ ~ $$\infty$$|
|<span class="nowrap">`:. .:`</span>|External range, checks across all the files for the most extreme range.|
|<span class="nowrap">`.: :.`</span>|Internal range, checks across all the files for the most conservative range.|

For example:

```sh
rebin 0k :. 0.1k
```

Rebins the data with a spacing of 0.1Å⁻¹, starting from 0 and using the most conservative range.

```sh
preedge .. -80eV -k
```

Removes the preedge as calculated from $$-\infty$$ up to $$E_{0}$$ - 80eV.
