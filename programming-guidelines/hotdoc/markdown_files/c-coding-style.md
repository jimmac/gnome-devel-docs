---
short-description: Our guidelines for C code in GNOME
...

# C Coding Style

This document presents the preferred coding style for C programs in
GNOME. While coding style is very much a matter of taste, in GNOME we
favor a coding style that promotes consistency, readability, and
maintainability.

We present examples of good coding style as well as examples of bad
style that is not acceptable in GNOME. Please try to submit patches that
conform to GNOME’s coding style; this indicates that you have done your
homework to respect the project’s goal of long-term maintainability.
Patches with GNOME’s coding style will also be easier to review!

> **Note**
> 
> This document is for C code. For other languages, check the [main
> page](index.md) of the GNOME Programming Guidelines.

These guidelines are heavily inspired by GTK’s CODING-STYLE document,
the Linux Kernel’s CodingStyle, and the GNU Coding Standards. These are
slight variations of each other, with particular modifications for each
project’s particular needs and culture, and GNOME’s version is no
different.

## The Single Most Important Rule

The single most important rule when writing code is this: *check the
surrounding code and try to imitate it*.

As a maintainer it is dismaying to receive a patch that is obviously in
a different coding style to the surrounding code. This is disrespectful,
like someone tromping into a spotlessly-clean house with muddy shoes.

So, whatever this document recommends, if there is already written code
and you are patching it, keep its current style consistent even if it is
not your favorite style.

## Line Width

Try to use lines of code between 80 and 120 characters long. This amount
of text is easy to fit in most monitors with a decent font size. Lines
longer than that become hard to read, and they mean that you should
probably restructure your code. If you have too many levels of
indentation, it means that you should fix your code anyway.

## Indentation

In general there are two preferred indentation styles for code in GNOME.

  - Linux Kernel style. Tabs with a length of 8 characters are used for
    the indentation, with K&R brace placement:
    
    ```c
    for (i = 0; i < num_elements; i++) {
        foo[i] = foo[i] + 42;
    
        if (foo[i] < 35) {
            printf ("Foo!");
            foo[i]--;
        } else {
            printf ("Bar!");
            foo[i]++;
        }
    }
    ```

  - GNU style. Each new level is indented by 2 spaces, braces go on a
    line by themselves, and they are indented as well.
    
    ```c 
    for (i = 0; i < num_elements; i++)
      {
        foo[i] = foo[i] + 42;
    
        if (foo[i] < 35)
          {
            printf ("Foo!");
            foo[i]--;
          }
        else
          {
            printf ("Bar!");
            foo[i]++;
          }
      }
    ```

Both styles have their pros and cons. The most important things is to
*be consistent* with the surrounding code. For example, the GTK+
library, which is GNOME’s widget toolkit, is written with the GNU style.
Nautilus, GNOME’s file manager, is written in Linux kernel style. Both
styles are perfectly readable and consistent when you get used to them.

Your first feeling when having to study or work on a piece of code that
doesn’t have your preferred indentation style may be, how shall we put
it, gut-wrenching. You should resist your inclination to reindent
everything, or to use an inconsistent style for your patch. Remember the
first rule: *be consistent* and respectful of that code’s customs, and
your patches will have a much higher chance of being accepted without a
lot of arguing about the right indentation style.

## Tab Characters

*Do not ever change the size of tabs in your editor*; leave them as 8
spaces. Changing the size of tabs means that code that you didn’t write
yourself will be perpetually misaligned.

Instead, set the *indentation size* as appropriate for the code you are
editing. When writing in something other than Linux kernel style, you
may even want to tell your editor to automatically convert all tabs to 8
spaces, so that there is no ambiguity about the intended amount of
space.

## Braces

Curly braces should not be used for single statement blocks:

```c
/* valid */
if (condition)
    single_statement ();
else
    another_single_statement (arg1);
```

The “no block for single statements” rule has only four exceptions:

  - In GNU style, if either side of an if-else statement has braces,
    both sides should, to match up indentation:
    
    ```c
    /* valid GNU style */
    if (condition)
      {
        foo ();
        bar ();
      }
    else
      {
        baz ();
      }
    ```
    
    ```c
    /* invalid */
    if (condition)
      {
        foo ();
        bar ();
      }
    else
      baz ();
    ```

  - If the single statement covers multiple lines, e.g. for functions
    with many arguments, and it is followed by `else` or `else if`:
    
    ```c
    /* valid Linux kernel style */
    if (condition) {
        a_single_statement_with_many_arguments (some_lengthy_argument,
                            another_lengthy_argument,
                            and_another_one,
                            plus_one);
    } else
        another_single_statement (arg1, arg2);
    
    /* valid GNU style */
    if (condition)
      {
        a_single_statement_with_many_arguments (some_lengthy_argument,
                                                another_lengthy_argument,
                                                and_another_one,
                                                plus_one);
      }
    else
      {
        another_single_statement (arg1, arg2);
      }
    ```

  - If the condition is composed of many lines:
    
    ```c
    /* valid Linux kernel style */
    if (condition1 ||
        (condition2 && condition3) ||
        condition4 ||
        (condition5 && (condition6 || condition7))) {
        a_single_statement ();
    }
    
    /* valid GNU style */
    if (condition1 ||
        (condition2 && condition3) ||
        condition4 ||
        (condition5 && (condition6 || condition7)))
      {
        a_single_statement ();
      }
    ```
    
    Note that such long conditions are usually hard to understand. A
    good practice is to set the condition to a boolean variable, with a
    good name for that variable. Another way is to move the long
    condition to a function.

  - Nested `if`s, in which case the block should be placed on the
    outermost `if`:
    
    ```c
    /* valid Linux kernel style */
    if (condition) {
        if (another_condition)
            single_statement ();
        else
            another_single_statement ();
    }
    
    /* valid GNU style */
    if (condition)
      {
        if (another_condition)
          single_statement ();
        else
          another_single_statement ();
      }
    ```
    
    ```c
    /* invalid */
    if (condition)
        if (another_condition)
            single_statement ();
        else if (yet_another_condition)
            another_single_statement ();
    ```

In general, new blocks should be placed on a new indentation level, like
this:

```c
int retval = 0;

statement_1 ();
statement_2 ();

{
    int var1 = 42;
    gboolean res = FALSE;

    res = statement_3 (var1);

    retval = res ? -1 : 1;
}
```

While curly braces for function definitions should rest on a new line
they should not add an indentation level:

```c
/* valid Linux kernel style*/
static void
my_function (int argument)
{
    do_my_things ();
}

/* valid GNU style*/
static void
my_function (int argument)
{
  do_my_things ();
}
```

```c
/* invalid */
static void
my_function (int argument) {
    do_my_things ();
}

/* invalid */
static void
my_function (int argument)
  {
    do_my_things ();
  }
```

## Conditions

Do not check boolean values for equality. By using implicit comparisons,
the resulting code can be read more like conversational English. Another
rationale is that a ‘true’ value may not be necessarily equal to
whatever the `TRUE` macro uses. For example:

```c
/* invalid */
if (found == TRUE)
    do_foo ();

/* invalid */
if (found == FALSE)
    do_bar ();
```

```c
/* valid */
if (found)
    do_foo ();

/* valid */
if (!found)
    do_bar ();
```

The C language uses the value 0 for many purposes. As a numeric value,
the end of a string, a null pointer and the `FALSE` boolean. To make the
code clearer, you should write code that highlights the specific way 0
is used. So when reading a comparison, it is possible to know the
variable type. For boolean variables, an implicit comparison is
appropriate because it’s already a logical expression. Other variable
types are not logical expressions by themselves, so an explicit
comparison is better:

```c
/* valid */
if (some_pointer == NULL)
    do_blah ();

/* valid */
if (number == 0)
    do_foo ();

/* valid */
if (str != NULL && *str != '\0')
    do_bar ();
```

```c
/* invalid */
if (!some_pointer)
    do_blah ();

/* invalid */
if (!number)
    do_foo ();

/* invalid */
if (str && *str)
    do_bar ();
```

## Functions

Functions should be declared by placing the returned value on a separate
line from the function name:

```c
void
my_function (void)
{
  …
}
```

The argument list must be broken into a new line for each argument, with
the argument names right aligned, taking into account pointers:

```c
void
my_function (some_type_t      type,
             another_type_t  *a_pointer,
             double_ptr_t   **double_pointer,
             final_type_t     another_type)
{
  …
}
```

If you use Emacs, you can use `M-x align` to do this kind of alignment
automatically. Just put the point and mark around the function’s
prototype, and invoke that command.

The alignment also holds when invoking a function without breaking the
line length limit:

```c
align_function_arguments (first_argument,
                          second_argument,
                          third_argument);
```

## Whitespace

Always put a space before an opening parenthesis but never after:

```c
/* valid */
if (condition)
    do_my_things ();

/* valid */
switch (condition) {
}
```

```c
/* invalid */
if(condition)
    do_my_things();

/* invalid */
if ( condition )
    do_my_things ( );
```

When declaring a structure type use newlines to separate logical
sections of the structure:

```c
struct _GtkWrapBoxPrivate
{
    GtkOrientation        orientation;
    GtkWrapAllocationMode mode;

    GtkWrapBoxSpreading   horizontal_spreading;
    GtkWrapBoxSpreading   vertical_spreading;

    guint16               vertical_spacing;
    guint16               horizontal_spacing;

    guint16               minimum_line_children;
    guint16               natural_line_children;

    GList                *children;
};
```

Do not eliminate whitespace and newlines just because something would
fit on a single line:

```c
/* invalid */
if (condition) foo (); else bar ();
```

Do eliminate trailing whitespace on any line, preferably as a separate
patch or commit. Never use empty lines at the beginning or at the end of
a file.

This is a little Emacs function that you can use to clean up lines with
trailing whitespace:

```
(defun clean-line-ends ()
  (interactive)
  (if (not buffer-read-only)
      (save-excursion
    (goto-char (point-min))
    (let ((count 0))
      (while (re-search-forward "[  ]+$" nil t)
        (setq count (+ count 1))
        (replace-match "" t t))
      (message "Cleaned %d lines" count)))))
```

## The `switch` Statement

A `switch` should open a block on a new indentation level, and each
`case` should start on the same indentation level as the curly braces,
with the case block on a new indentation level:

```c
/* valid Linux kernel style */
switch (condition) {
case FOO:
    do_foo ();
    break;

case BAR:
    do_bar ();
    break;
}

/* valid GNU style */
switch (condition)
  {
  case FOO:
    do_foo ();
    break;

  case BAR:
    do_bar ();
    break;
  }
```

```c
/* invalid */
switch (condition) {
  case FOO: do_foo (); break;
  case BAR: do_bar (); break;
}

/* invalid */
switch (condition)
  {
  case FOO: do_foo ();
    break;
  case BAR: do_bar ();
    break;
  }

/* invalid */
switch (condition)
  {
    case FOO:
    do_foo ();
    break;
    case BAR:
    do_bar ();
    break;
  }
```

It is preferable, though not mandatory, to separate the various cases
with a newline:

```c
switch (condition) {
case FOO:
    do_foo ();
    break;

case BAR:
    do_bar ();
    break;

default:
    do_default ();
}
```

The `break` statement for the `default` case is not mandatory.

If switching over an enumerated type, a `case` statement must exist for
every member of the enumerated type. For members you do not want to
handle, alias their `case` statements to `default`:

```c
switch (enumerated_condition) {
case HANDLED_1:
    do_foo ();
    break;

case HANDLED_2:
    do_bar ();
    break;

case IGNORED_1:
case IGNORED_2:
default:
    do_default ();
}
```

If most members of the enumerated type should not be handled, consider
using an `if` statement instead of a `switch`.

If a `case` block needs to declare new variables, the same rules as the
inner blocks apply (see above); the `break` statement should be placed
outside of the inner block:

```c
/* valid GNU style */
switch (condition)
  {
  case FOO:
    {
      int foo;

      foo = do_foo ();
    }
    break;

  …
  }
```

## Header Files

The only major rule for headers is that the function definitions should
be vertically aligned in three columns:

```c
return_type          function_name           (type   argument,
                                              type   argument,
                                              type   argument);
```

The maximum width of each column is given by the longest element in the
column:

```c
void         gtk_type_set_property (GtkType      *type,
                                    const gchar  *value,
                                    GError      **error);
const gchar *gtk_type_get_property (GtkType      *type);
```

It is also possible to align the columns to the next tab:

```c
void          gtk_type_set_prop           (GtkType *type,
                                           gfloat   value);
gfloat        gtk_type_get_prop           (GtkType *type);
gint          gtk_type_update_foobar      (GtkType *type);
```

As before, you can use `M-x align` in Emacs to do this automatically.

If you are creating a public library, try to export a single public
header file that in turn includes all the smaller header files into it.
This is so that public headers are never included directly; rather a
single include is used in applications. For example, GTK+ uses the
following in its header files that should not be included directly by
applications:

```c
#if !defined (__GTK_H_INSIDE__) && !defined (GTK_COMPILATION)
#error "Only <gtk/gtk.h> can be included directly."
#endif
```

For libraries, all headers should have inclusion guards (for internal
usage) and C++ guards. These provide the `extern
"C"` magic that C++ requires to include plain C headers:

```c
#ifndef MYLIB_FOO_H_
#define MYLIB_FOO_H_

#include <gtk/gtk.h>

G_BEGIN_DECLS

…

G_END_DECLS

#endif /* MYLIB_FOO_H_ */
```

## GObject Classes

GObject class definitions and implementations require some additional
coding style notices, and should always be [correctly
namespaced](namespacing.md#gobject).

Typedef declarations should be placed at the beginning of the file:

```c
typedef struct _GtkBoxedStruct       GtkBoxedStruct;
typedef struct _GtkMoreBoxedStruct   GtkMoreBoxedStruct;
```

This includes enumeration types:

```c
typedef enum
{
  GTK_SIZE_REQUEST_WIDTH_FOR_HEIGHT,
  GTK_SIZE_REQUEST_HEIGHT_FOR_WIDTH
} GtkSizeRequestMode;
```

And callback types:

```c
typedef void (* GtkCallback) (GtkWidget *widget,
                              gpointer   user_data);
```

Instance structures should be declared using `G_DECLARE_FINAL_TYPE` or
`G_DECLARE_DERIVABLE_TYPE`:

```c
#define GTK_TYPE_FOO (gtk_foo_get_type ())
G_DECLARE_FINAL_TYPE (GtkFoo, gtk_foo, GTK, FOO, GtkWidget)
```

For final types, private data can be stored in the object struct, which
should be defined in the C file:

```c
struct _GtkFoo
{
  GObject   parent_instance;

  guint     private_data;
  gpointer  more_private_data;
};
```

For derivable types, private data must be stored in a private struct in
the C file, configured using `G_DEFINE_TYPE_WITH_PRIVATE()` and accessed
using a `_get_instance_private()` function:

```c
#define GTK_TYPE_FOO gtk_foo_get_type ()
G_DECLARE_DERIVABLE_TYPE (GtkFoo, gtk_foo, GTK, FOO, GtkWidget)

struct _GtkFooClass
{
  GtkWidgetClass parent_class;

  void (* handle_frob)  (GtkFrobber *frobber,
                         guint       n_frobs);

  gpointer padding[12];
};
```

Always use the `G_DEFINE_TYPE()`, `G_DEFINE_TYPE_WITH_PRIVATE()`, and
`G_DEFINE_TYPE_WITH_CODE()` macros, or their abstract variants
`G_DEFINE_ABSTRACT_TYPE()`, `G_DEFINE_ABSTRACT_TYPE_WITH_PRIVATE()`, and
`G_DEFINE_ABSTRACT_TYPE_WITH_CODE()`; also, use the similar macros for
defining interfaces and boxed types.

Interface types should always have the dummy typedef for cast purposes:

```c
typedef struct _GtkFooable          GtkFooable;
```

The interface structure should have ‘Interface’ postfixed to the dummy
typedef:

```c
typedef struct _GtkFooableInterface     GtkFooableInterface;
```

Interfaces must have the following macros:

| Macro                      | Expands to                      |
| -------------------------- | ------------------------------- |
| `GTK_TYPE_iface_name`      | `iface_name_get_type`           |
| `GTK_iface_name`           | `G_TYPE_CHECK_INSTANCE_CAST`    |
| `GTK_IS_iface_name`        | `G_TYPE_CHECK_INSTANCE_TYPE`    |
| `GTK_iface_name_GET_IFACE` | `G_TYPE_INSTANCE_GET_INTERFACE` |

## Memory Allocation

When dynamically allocating data on the heap use `g_new()`.

Public structure types should always be returned after being zero-ed,
either explicitly for each member, or by using `g_new0()`.

See [](memory-management.md) for more details.

## Macros

Try to avoid private macros unless strictly necessary. Remember to
`#undef` them at the end of a block or a series of functions needing
them.

Inline functions are usually preferable to private macros.

Public macros should not be used unless they evaluate to a constant.

## Public API

Avoid exporting variables as public API, since this is cumbersome on
some platforms. It is always preferable to add getters and setters
instead. Also, beware global variables in general.

## Private API

Non-exported functions that are needed in more than one source file
should be prefixed with an underscore (‘_’), and declared in a private
header file. For example, `_mylib_internal_foo()`.

Underscore-prefixed functions are never exported.

Non-exported functions that are only needed in one source file should be
declared static.
