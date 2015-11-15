# Environments (`ENVSXP`)

```cpp
Rboolean Rf_isEnvironment(SEXP s);
```

* `R_GlobalEnv`: The "global" environment
* `R_EmptyEnv`: An empty environment at the root of the environment tree
* `R_BaseEnv`: The base environment; formerly R_NilValue
* `R_BaseNamespace`: The (fake) namespace for base
* `R_NamespaceRegistry`: Registry for registered namespaces

## Accessors

```cpp
SEXP FRAME(SEXP x);
void SET_FRAME(SEXP x, SEXP v);

SEXP ENCLOS(SEXP x);
void SET_ENCLOS(SEXP x, SEXP v);

int  ENVFLAGS(SEXP x);
void SET_ENVFLAGS(SEXP x, int v);
```

## Get set objects in environment

`symbol` should be a `SYMSXP`; `environment` should be an `ENVSXP`.

```cpp
// Look up symbol in environment, following up enclosing envs
SEXP Rf_findVar(SEXP symbol, SEXP environment);

// Look up symbol in environment, ignoring non-functions
SEXP Rf_findFun(SEXP symbol, SEXP environment);

// Look in single environment
// Returns R_UnboundValue if not found
// doGet: if TRUE, returns value; if FALSE just checks for presence
SEXP Rf_findVarInFrame3(SEXP env, SEXP symbol, Rboolean doGet);

// Shortcut from findVarInFrame3(rho, symbol, TRUE)
SEXP Rf_findVarInFrame(SEXP env, SEXP symbol);

// Define value
void Rf_defineVar(SEXP symbol, SEXP value, SEXP env);

// Returns character vector
SEXP R_lsInternal3(SEXP env, Rboolean all_names, Rboolean sorted);

// Shorthand for R_lsInternal3(env, all, TRUE)
SEXP R_lsInternal(SEXP, Rboolean);

// Copy variables from (VECSXP) unless already present in to
void Rf_addMissingVarsToNewEnv(SEXP to, SEXP from);

```

## Bindings

```cpp
Rboolean R_IsPackageEnv(SEXP rho);
SEXP R_PackageEnvName(SEXP rho);
SEXP R_FindPackageEnv(SEXP info);
Rboolean R_IsNamespaceEnv(SEXP rho);
SEXP R_NamespaceEnvSpec(SEXP rho);
SEXP R_FindNamespace(SEXP info);
void R_LockEnvironment(SEXP env, Rboolean bindings);
Rboolean R_EnvironmentIsLocked(SEXP env);
void R_LockBinding(SEXP sym, SEXP env);
void R_unLockBinding(SEXP sym, SEXP env);
void R_MakeActiveBinding(SEXP sym, SEXP fun, SEXP env);
Rboolean R_BindingIsLocked(SEXP sym, SEXP env);
Rboolean R_BindingIsActive(SEXP sym, SEXP env);
Rboolean R_HasFancyBindings(SEXP rho);
Rboolean R_envHasNoSpecialSymbols(SEXP);
```

## Hashing functions

```cpp
SEXP HASHTAB(SEXP x);
void SET_HASHTAB(SEXP x, SEXP v);

int  HASHASH(SEXP x);
void SET_HASHASH(SEXP x, int v);

int  HASHVALUE(SEXP x);
void SET_HASHVALUE(SEXP x, int v);
```