---
tags: [ottimizzazione, gurobi, solver, ILP, MIP, python, java, tool]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/gurobi_introduction-3.pdf
---

# Gurobi

Solver commerciale per problemi di ottimizzazione (LP, QP, MIP, ...), gratuito per uso accademico. Scritto in C da Zonghao Gu, Edward Rothberg e Robert Bixby. Multi-threaded, ad alte prestazioni, ampiamente usato in industria e ricerca. Interfaccia tramite API per C++, Java, Python, .NET, MATLAB, R e shell interattiva.

## Punti chiave

- **Gurobi** risolve LP, QP, MIP in modo black-box: costruisci il modello matematico, Gurobi trova la soluzione.
- I **parametri** controllano il comportamento del solver (es. TimeLimit, MIPGap, Threads) e si impostano **prima** della ottimizzazione.
- Gli **attributi** descrivono lo stato del modello/variabili/vincoli e si leggono dopo la risoluzione (ObjVal, Status, X...).
- Ogni modifica al modello è **lazy**: viene applicata solo quando si chiama `model.update()` o `model.optimize()`.
- Le **Callbacks** permettono di iniettare logica personalizzata durante l'ottimizzazione (es. aggiungere lazy constraints nel B&C).
- Per TSP e modelli con numero esponenziale di vincoli, si usano **lazy constraints** aggiunte dinamicamente tramite callback.

## Installazione

1. Registrarsi su `www.gurobi.com` con email accademica.
2. Scaricare e installare Gurobi Optimizer.
3. Generare una licenza accademica gratuita.
4. Attivare la licenza: `grbgetkey YOUR_LICENCE_CODE` (rete universitaria necessaria).
5. Java: aggiungere al classpath `<GRB_INSTALL_DIR>/<YOUR_PLATFORM>/lib/gurobi.jar`.
6. Python: `pip install gurobipy`.

## Architettura API

```
[C++/Java/.NET/Python/MATLAB/R API]
           ↓
         C API
    ↙           ↘
Model Data    Gurobi Algorithms
                   ↓
             Solution Data
```

La shell interattiva di Gurobi bypassa le API e si collega direttamente al C API.

## Java API

### Flusso di lavoro

```java
// 1. Ambiente
GRBEnv env = new GRBEnv("mipExample.log");
// parametri ereditati dai modelli figli al momento della creazione

// 2. Parametri (PRIMA dell'ottimizzazione)
env.set(GRB.IntParam.Threads, 4);
env.set(GRB.IntParam.Presolve, 2);       // -1=auto, 0=off, 1=conserv, 2=aggress
env.set(GRB.DoubleParam.MIPGap, 1e-6);
env.set(GRB.DoubleParam.TimeLimit, 600);

// 3. Modello (contenitore del problema)
GRBModel model = new GRBModel(env);
model.set(GRB.IntParam.LazyConstraints, 1);  // se si usano lazy constraints

// 4. Variabili: addVar(lb, ub, obj_coeff, type, name)
GRBVar x = model.addVar(0, 1, 7, GRB.BINARY,    "x");
GRBVar y = model.addVar(0, 5, 8, GRB.INTEGER,   "y");
GRBVar z = model.addVar(0, 1.5, 9, GRB.CONTINUOUS, "z");
// Tipi: GRB.BINARY, GRB.INTEGER, GRB.CONTINUOUS

// 5. Funzione obiettivo
GRBLinExpr obj = new GRBLinExpr();
obj.addTerm(1.0, x); obj.addTerm(1.0, y); obj.addTerm(2.0, z);
model.setObjective(obj, GRB.MAXIMIZE);

// 6. Vincoli: addConstr(lhs, sense, rhs, name)
GRBLinExpr c0 = new GRBLinExpr();
c0.addTerm(1.0, x); c0.addTerm(2.0, y); c0.addTerm(3.0, z);
model.addConstr(c0, GRB.LESS_EQUAL, 4.0, "c0");
// Senses: GRB.LESS_EQUAL, GRB.GREATER_EQUAL, GRB.EQUAL

// 7. Ottimizzazione (applica anche i cambiamenti lazy)
model.optimize();

// 8. Ispezione risultati
if (model.get(GRB.IntAttr.SolCount) > 0) {
    GRBVar varX = model.getVar(0);
    GRBVar varY = model.getVarByName("y");
    varY.get(GRB.DoubleAttr.X);          // valore ottimo
    varX.get(GRB.StringAttr.VarName);    // nome variabile
    model.get(GRB.DoubleAttr.ObjVal);    // valore obiettivo
    model.get(GRB.DoubleAttr.Runtime);   // tempo esecuzione
    model.write("solution.lp");          // salva su file
} else {
    model.get(GRB.IntAttr.Status);       // GRB.OPTIMAL, INFEASIBLE, TIME_LIMIT...
}

// 9. Rilascio risorse
model.dispose();
env.dispose();
```

### Parametri chiave

| Parametro | Tipo | Default | Descrizione |
|-----------|------|---------|-------------|
| `Threads` | Int | 0 (auto) | Numero di thread |
| `Presolve` | Int | -1 (auto) | Livello presolve: -1/0/1/2 |
| `MIPGap` | Double | 1e-4 | Stop se `\|ObjVal−BestBound\|/\|ObjVal\| < MIPGap` |
| `TimeLimit` | Double | +∞ | Limite tempo in secondi |
| `LazyConstraints` | Int | 0 | Abilita lazy constraints via callback |

**MIPGap**: impostarlo a un valore sufficientemente basso: `1/|LpSolutionValue|` per evitare di perdere soluzioni ottime.

### Attributi chiave

| Attributo | Tipo | Associato a |
|-----------|------|------------|
| `ObjVal` | Double | Modello |
| `Status` | Int | Modello |
| `Runtime` | Double | Modello |
| `ModelSense` | Int | Modello |
| `Obj` e `X` | Double | Variabile |
| `LB` e `UB` | Double | Variabile |
| `VarName` | String | Variabile |
| `Sense` | Char | Vincolo |
| `RHS` | Double | Vincolo |
| `ConstrName` | String | Vincolo |

### Continuous relaxation

```java
GRBModel relaxed = model.relax();
relaxed.optimize();
// Copia indipendente con tutte le variabili di tipo CONTINUOUS
// GRB.DoubleAttr.RC = reduced cost (non disponibile per MIP)
```

## Python API

```python
from gurobipy import Env, GRB, Model

# Modello con ambiente di default
model = Model('model_name')
model.Params.LogFile = 'output.log'

# Variabili (argomenti opzionali)
x = model.addVar(vtype=GRB.BINARY, name="x")
y = model.addVar(vtype=GRB.BINARY, name="y")
z = model.addVar(vtype=GRB.BINARY, name="z")

# Obiettivo con operator overloading
model.setObjective(3*x + 4*y + 5*z, GRB.MAXIMIZE)

# Vincolo con operator overloading
model.addConstr(x + 2*y + 3*z <= 4, "c0")

# Attributi: property o setter/getter
model.timeLimit = 3600                    # property
model.setAttr('TimeLimit', 3600)          # setter esplicito
time_limit = model.getAttr('TimeLimit')   # getter esplicito
# Nomi attributi/parametri CASE INSENSITIVE

# Parametri tramite oggetto Params
model.params.MIPGap = 0
mip_gap = model.getParamInfo('MIPGap')

model.optimize()
```

**Performance con molte variabili**: l'operator overloading è più lento. Alternativa veloce:
```python
lhs = LinExpr(constr_coeffs, constr_vars)
```

## Callbacks

Le callbacks permettono di **intervenire durante l'ottimizzazione**, ad es. per aggiungere tagli nel [[exact-algorithms|B&C]].

**`where`** = punto del processo dove viene chiamata la callback:
- `GRB.CB_PRESOLVE`: dentro il presolve
- `GRB.CB_MIPNODE`: nuovo nodo MIP
- `GRB.CB_MIPSOL`: nuova soluzione MIP trovata

**ATTENZIONE**: all'interno di una callback NON si può interrogare `model` direttamente; usare i metodi custom della callback.

### Java callback

```java
public class UserCallback extends GRBCallback {
    protected void callback() {
        if (where == GRB.CB_MIPNODE) {
            double[] x = getNodeRel(vars);      // valori LP relaxation al nodo
            // getSolution(vars) solo in CB_MIPSOL
            // addCut(expr, GRB.GREATER_EQUAL, expr)  → valid cut (non taglia feasible)
            // addLazy(expr, GRB.GREATER_EQUAL, expr) → lazy constraint (può tagliare feasible)
        }
    }
}
model.setCallback(new UserCallback());
```

Metodi disponibili in callback Java:
- `getSolution(vars)` — valori soluzione (solo in CB_MIPSOL)
- `getNodeRel(vars)` — valori LP relaxation al nodo (solo in CB_MIPNODE)
- `addCut(expr, sense, expr)` — aggiunge un taglio globalmente valido (solo in CB_MIPNODE)
- `addLazy(expr, sense, expr)` — aggiunge lazy constraint (in CB_MIPNODE o CB_MIPSOL)
- `getDoubleInfo(what)`, `getIntInfo(what)`, `getStringInfo(what)`

### Python callback

```python
def user_callback(model, where):
    if where == GRB.Callback.MIPSOL:
        print(model.cbGetSol(model._vars))

model._vars = model.getVars()
model.optimize(user_callback)
```

Metodi Python: `cbGetSol`, `cbGetNodeRel`, `cbCut`, `cbLazy`, `cbGet`.

## Esempio: TSP con Lazy Constraints

Il **Symmetric TSP** ha O(2ⁿ) subtour elimination constraints → impossibile tenerle tutte in memoria.

**Formulazione** (grafo non orientato (V, E)):
```
min  Σₑ cₑxₑ
     Σₑ∈Eᵢ xₑ = 2           ∀i ∈ V   (degree constraint)
     Σₑ∈E(S) xₑ ≤ |S|−1    ∀S ⊆ V, |S| ≥ 2   (subtour elim - lazy!)
     xₑ ∈ {0,1}
```

**Soluzione**: aggiungere le subtour constraints lazily durante il B&C tramite callback.

```java
// Abilitare lazy constraints
model.set(GRB.IntParam.LazyConstraints, 1);
// Nella callback (dove where == CB_MIPSOL):
// 1. Trovare il subtour più piccolo nella soluzione corrente
// 2. Aggiungere addLazy(...) per quel subtour
```

```python
# Python
model.Params.lazyConstraints = 1
# Nella callback: model.cbLazy(...)
```

## Lettura log di Gurobi

```
Nodes | Current Node | Objective Bounds | Work
Expl Unexpl | Obj Depth IntInf | Incumbent BestBd Gap | It/Node Time
```
- `H` = nuova soluzione euristica trovata
- `Gap = |ObjVal − BestBd| / |ObjVal|`

## Articoli correlati

- [[exact-algorithms]]
- [[branch-and-cut-tsp]]
- [[ottimizzazione-combinatoria]]

## Fonti

- `raw/gurobi_introduction-3.pdf` — slide del corso Optimization Algorithms, Enrico Brambilla, UniBS 24 Ottobre 2025.
