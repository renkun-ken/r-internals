# Strings (`CHARSXP`)

A character vector (`STRSXP`) is effectively an array of pointers to `CHARSXP`s, which are stored in a global string pool. This design allows individual `CHARSXP`'s to be shared between multiple character vectors, reducing memory usage. 

Each `CHARSXP` has an encoding associated with it:

```cpp
typedef enum {
  CE_NATIVE = 0,
  CE_UTF8   = 1,
  CE_LATIN1 = 2,
  CE_BYTES  = 3,
  CE_SYMBOL = 5,
  CE_ANY    = 99
} cetype_t;

cetype_t Rf_getCharCE(SEXP);
```

## Creating from C strings

```cpp
// Use current encoding; null terminated string
// [[SEXP creator]]
SEXP Rf_mkChar(const char* x);
// Use current encoding; specified length
// [[SEXP creator]]
SEXP Rf_mkCharLen(const char* x, int n);

// Create in specified encoding
// [[SEXP creator]]
SEXP Rf_mkCharCE(const char* x, cetype_t encoding);
// [[SEXP creator]]
SEXP Rf_mkCharLenCE(const char* x, int n, cetype_t encoding);
```

(Typically these calls don't need to be protected as they are immediately assigned into a `STRSXP`.)

If you need to re-encode strings from another encoding, use R's wrapper around iconv found in `R_ext/Riconv.h`. It provides cross-platform `Riconv_open()` and `Riconv`. It's usually best to convert to UTF-8.

## Convert to C string

To access the underlying C-string stored in a CHARSXP, use `CHAR()`:

```cpp
const char* R_CHAR(SEXP x);
#define CHAR(x) R_CHAR(x)
```

__NB__: To match the other vector accessor functions (`LOGICAL()`, `INTEGER()`, ...), this is typically called as `CHAR()`.

Three functions create `const char*` with the specified encoding:

```cpp
// Translated to native encoding
const char* Rf_translateChar(SEXP x);
// Returns as is if bytes; otherwise translates to native encoding
const char* Rf_translateChar0(SEXP x);
// Translates to UTF8
const char* Rf_translateCharUTF8(SEXP x);
```

__NB__: if re-encoding is necessary, the `char*` will be allocated by `R_alloc()` and will be automatically freed gc. If you want to save across calls, make a copy.

## Special values

```cpp
SEXP R_NaString; // Singleton CHARSXP
#define NA_STRING R_NaString

Rboolean Rf_StringBlank(SEXP);
SEXP R_BlankString; // CHARSXP
SEXP R_BlankScalarString; // STRSXP
Rboolean Rf_isBlankString(const char *);

Rboolean Rf_StringFalse(const char *);
Rboolean Rf_StringTrue(const char *);

// TYPEOF(x) == STRSXP && LENGTH(x) > 0 && TYPEOF(STRING_ELT(x, 0)) != NILSXP;
Rboolean Rf_isValidString(SEXP);
// isValidString(x) && CHAR(STRING_ELT(x, 0))[0];
Rboolean Rf_isValidStringF(SEXP);
```

## Helper functions

```cpp
typedef enum {Bytes, Chars, Width} nchar_type;
int R_nchar(SEXP string, nchar_type type_,
  Rboolean allowNA, Rboolean keepNA, const char* msg_name);
  
// Return a copy of a string using memory from R_alloc. Memory free on gc
char* Rf_acopy_string(const char *);

// Render any R object as a string (utils.c)
// Returns a CHARSXP
// [[SEXP creator]]
SEXP Rf_asChar(SEXP x); 

// Checks that s and t are equal (and not NA or "") (match.c)
// Translates both to UTF-8 for comparison
Rboolean Rf_NonNullStringMatch(SEXP s, SEXP t);

const char *Rf_reEnc(const char *x, cetype_t ce_in, cetype_t ce_out, int subst);
```
