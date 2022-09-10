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
* Don't overcomplicate things.

## Comments

```c
// Comment text should always be preceded by a space.
/* Multiline comments should be used when single line comments would span over
 * three lines of text (4+). Asterisks should exist for each line and lie on the
 * same column. The slash of the closing gesture should nestle cleanly within
 * the space left before each line of comments.
 */
```

When commenting out code, solely `//` is preferred, with or without the space,
whichever is most comfortable for you. Personally, I prefer to just do
`:+,+NUMLINESs/^/\/\/` (cool vim command that comments out the next NUMLINES
lines (not including current line)!)

## Identifiers

```c
int const variablesAreLikeThis;
int functions_are_like_this();
struct AllDataStructuresAreLikeThis { int andMembersAreTreatedAsVars; };
enum EvenEnums { ENUM_FIELDS_ARE_LIKE_THIS, }; // though shouldn't be this long

// Should match the name of the struct.
typedef struct AllDataStructuresAreLikeThis AllDataStructuresAreLikeThis;
// Same naming style as structs, but name should be descriptive for its use.
typedef uint64_t UserID;

#define MACROS_ARE_ALSO_LIKE_THIS
#define EVEN_IF_THEY_ARE_FUNCTION_MACROS(x)
// ...Goto labels should also be formatted like macros.
```

## Branching

Don't use goto's unless it makes your code much more readable by doing so. Use
functions if you're too many scopes deep. Otherwise, branch style should be like
this:

```c
// Notice the space after each branch instruction. The space should only exist
// for branches and NOT functions. Also, the { is on the same line as the branch
// as per Linux kernel standard.
if (x == 2) {
	// Code...
}

for (int i = 0; i < 10; i++) {
	// Code...
}

while (x != 4) {
	// Code
}

do {
	// Code...
} while (x != 4);

/* Case statements should align to the depth of the switch. The reason for this
 * is to emphasize that they are, truly, all at the same scope, and to prevent
 * from switch statements becoming too indented (a regular if/else if/else would
 * only need to be indented a minimum of one time, while a switch with
 * separately indented cases would require a minimum of two indents. Also, for
 * scopes within cases, they should be aligned to one indent past the switch,
 * with the starting brace one space from the case *: statement.
 */
switch (x) {
case 0:
	break;
case 1:
	return 4;
case 2: {
		int x = 2;
		x += 4;
	}
	break;
default:
	break;
}

/* If gotos and labels must be used, labels should be indented to the same level
 * of indent as the function declaration itself. Since this is C and nested
 * functions don't exist, that should in all cases be an indent of zero columns.
 * The reason for the lack of indent is to emphasize the frightening nature of
 * throwing away any respect for the scope within the function.
 */
int my_function(int x)
{
	if (x == 4) {
		goto QUICK_RETURN;
	} else if (x == 8) {
		return 2;
	}
QUICK_RETURN:
	return 28;
}
```

## Function Declarations & Definitions

```c
// Entire declaration should be on one line, unless the 80 column limit is
// reached. Opening and closing curly braces should be alone on their two lines.
int main(int const argc, char const *const *const argv)
{
	// Indent once, then begin code here.
}

// Notice two things: the single empty line separating the end of main and the
// next function, and that these are, for semantic reasons, two separate comment
// blocks.
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

## Pointers

Pointers should be right-aligned, including in casts. No const should be on the
left side of the data type.

```c
// In this example, i=integer, p=pointer, c=const, so "cpi" is "constant pointer
// to an integer"
int const ci;
int *pi;
int const *pci;
int const *const cpci;
int const *const *pcpci;
// And so on...

int const ci = 4;
int const *pci = &x;
uint32_t const *pcu32 = (uint32_t const *) pci;
```

## Headers

Headers should be included categorically in the following order, with a single
empty line separating each category, and alphabetically ordered within each
category:

1. Corresponding source file header (for example `main.c` requires `main.h`)
2. Project-specific headers (`main.c` requires `my_cool_header.h`)
3. Other headers (library, standard, etc.)

An example of how this might look:

```c
#include "main.h"

#include "my_cool_header.h"
#include "my_other_cool_header.h"

// Notice how there is a single empty line separating the above and below.
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

The order of functions declared in a header file should match the order they are
used in their corresponding C file, though this is not strictly necessary.

Header files should be structured consistently like this:

0. Header guard (preferably #pragma once)
1. Includes (following above rules)
2. Data structures (Structs, Unions, Enums, etc.)
3. Function declarations

The header guard and includes should be separated by a single empty newline.
Every other section should be separated by two empty lines.

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
void change_id(struct MyCoolStruct *const coolStruct, uint32_t const newId);
void change_bool(struct MyCoolStruct *const coolStruct, bool const myBool);

// Different "groups" of functions should be separated by a single empty line.
char *get_string_from_cool_data(struct MyCoolStruct const *const coolStruct);
```
