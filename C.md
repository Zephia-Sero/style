# C Style Guide

## General

* Tabs should be used instead of spaces, and code should be written with
8-column tabs in mind.
* Code should fit within 80 columns, however, code *can* be extended to 120
columns **only** if a line break would decrease readability.
* Declare as `const` now, remove `const` as needed.
* Features which obscure information (ie. typedefs, macros) and preprocessor
directives should be avoided unless they significantly increase readability or
compatibility. If they reduce readability, attempt to regain readability via
the use of comments.
* Comment sooner rather than later. Comment too much rather than too little.

## Comments

```c
// Comment text should always be preceded by a space.
/* Multiline comments should be used when single line comments would span over
 * three lines of text. Asterisks should exist for each line and lie on the
 * same column. The slash of the closing gesture should nestle cleanly within
 * the space left before each line of comments.
 */
```

When commenting out code, solely `//` is preferred, with or without the space,
whichever is most comfortable for you. Personally, I prefer to just do
`:+,+NUMLINESs/^/\/\/` (cool sed command that comments out the next NUMLINES
lines (not including current line)!)

## Identifiers

```c
int const variablesAreLikeThis;
int functions_are_like_this();
struct AllDataStructuresAreLikeThis { int andMembersAreTreatedAsVars; };
enum EvenEnums { ENUM_FIELDS_ARE_LIKE_THIS, }; // though shouldn't be this long

// Should match the name of the struct.
typedef struct AllDataStructuresAreLikeThis AllDataStructuresAreLikeThis;
// Same naming style as structs unless used like a integral or floating type.
typedef uint64_t UUID;
// If used as a number (ie. in arithmetic calculations):
typedef char char

#define MACROS_ARE_ALSO_LIKE_THIS
#define EVEN_IF_THEY_ARE_FUNCTION_MACROS(x)
```

## Function Declarations & Definitions

```c
// Entire declaration should be on one line, unless the 80 column limit is
// reached. Opening and closing curly braces should be alone on their two lines.
int main(int const argc, char const *const *const argv)
{
	// Indent once, then begin code here.
}

/* If the declaration is too long, consider splitting it into smaller functions
 * or make the function name shorter *without* sacrificing obviousness and
 * readability. If that isn't possible, split the arguments across multiple
 * lines, like so:
 */
int much_longer_function_maybe_a_bit_too_long_and_should_be_refactored(
	int const intArg, char const charParam, char nonConstCharParam,
	char *heresAStringToo)
{
	// Indent once, begin code here.
}
```

## Headers

Headers should be included categorically in the following order, with a single
newline separating each category, and alphabetically ordered within each
category:

1. Corresponding source file header (for example `main.c` requires `main.h`)
2. Project-specific headers (`main.c` requires `my_cool_header.h`)
3. Library headers (for example `math.h` or `pthread.h`)
4. `<std*.h>` headers. (for example `stdlib.h`)

An example of how this might look:

```c
#include "main.h"

#include "my_cool_header.h"
#include "my_other_cool_header.h"

#include <math.h>
#include <pthread.h>

#include <stdbool.h>
#include <stdio.h>
#include <stdlib.h>
```

Forward declaration should be avoided if possible. When searching for a function
name from top to bottom, it is most likely that you are searching for the
definition of the function itself, and not its forward declaration or other uses
of it.

The structure of a header file should look as follows:

```c
// Please use pragma once guards.
#pragma once

// Includes ONLY if needed since these will be included in each file which also
// includes this one. These should still follow the above include order rule.
#include "cool_data.h"

#include <stdbool.h>
#include <stdint.h>

// Data structures (Structs, Unions, Enums, etc.)
struct MyCoolStruct {
	bool myBool;
	uint32_t id;
	struct CoolData coolData;
};

// Function declarations.
void change_id(struct MyCoolStruct *coolStruct, uint32_t newId);
```
