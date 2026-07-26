# $\mathcal{P}$gPrism — Laws, Semantics, Easy Wins

---

## $\S 0$. The One-Line Correction

The name is right and the theorem is wrong, and the name says why.

A **prism** in optics is a *partial* isomorphism with a **one-sided** law:

$$\text{preview} \circ \text{review} = \text{Some} \qquad \text{(always)}$$

$$\text{review} \circ \text{preview} \neq \text{id} \qquad \text{(only on the matching subset)}$$

You chose the exact term for *lossy one way, faithful the other* — then boxed two identity laws. Drop them.

$$\boxed{\;\mathcal{F} \circ \mathcal{R} \circ \mathcal{F} = \mathcal{F} \qquad \mathcal{R} \circ \mathcal{F} \circ \mathcal{R} = \mathcal{R}\;}$$

Idempotence to a normal form — a **retraction pair**, not an isomorphism. Provable. And on the supported subsets $S^\*, T^\*$ with normalizers $N_S, N_T$:

$$\mathcal{R}(\mathcal{F}(s)) = N_S(s)\quad \forall s \in S^\* \qquad \mathcal{F}(\mathcal{R}(t)) = N_T(t)\quad \forall t \in T^\*$$

Canonical equivalence. Not textual identity.

---

## $\S 1$. The IR Is the White Light

Marketing may say the schema is the white light. The **architecture** must not.

```
PostgreSQL DDL  ──┐                          ┌──→ Rows.fs        (persistence)
Live pg_catalog ──┼──→  Canonical Schema IR ─┼──→ Domain.fs      (uplift)
Annotated F#    ──┘         (white light)    ├──→ Validators.fs
                                             ├──→ Generators.fs
                                             ├──→ validators.ts
                                             ├──→ openapi.yaml
                                             ├──→ EVIDENCE.md
                                             └──→ LOSS.md
```

Frontends and projections are independent. Cost of $N$ dialects $\times$ $M$ targets:

$$N \times M \;\longrightarrow\; N + M$$

And the testable law becomes IR-primary, so FsCheck generates the **IR** directly:

$$\boxed{\;\text{Parse}(\text{Render}(ir)) = ir \qquad \text{Decode}(\text{Emit}(ir),\, manifest) = ir\;}$$

This is CanonFlow's own thesis: schema as axioms, targets as theorems, **no theorem-to-theorem edges**. A direct `SQL ↔ F#` arrow violates it.

---

## $\S 2$. Do Not Write the Grammar

`libpg_query` extracts PostgreSQL's own parser and returns its authoritative parse tree. It compiles to WASM — so **one front end serves CLI and browser**.

$$\text{PgPrism does not implement } 3\% \text{ of the grammar.}$$
$$\text{It interprets } 3\% \text{ of the authoritative parse tree.}$$

The 500-line FParsec estimate is unsafe not because `CREATE TABLE` is hard, but because the checklist smuggles in the full **expression** grammar: precedence, casts, function calls, arrays, `IS DISTINCT FROM`, `CASE`, user-defined operators with user-defined precedence. That is where `gram.y`'s difficulty actually lives.

Reserve FParsec for a deliberately restricted PgPrism DSL, if ever.

---

## $\S 3$. Three-Valued Logic Is P0

**The most dangerous line in the whole design.** PostgreSQL admits a row unless the check evaluates to `FALSE`:

$$\boxed{\;\text{PgAdmits}(c, r) \iff \text{Eval}_{pg}(c, r) \neq \textsf{False}\;}$$

`UNKNOWN` **passes**. A naive two-valued F# validator becomes *stricter than the database* — it rejects rows already sitting in production.

Straight from your own Tier-1 checklist:

```sql
CREATE TEMP TABLE t (
    col   text,
    other text,
    CONSTRAINT chk CHECK (col <> 'X' OR other IS NOT NULL)
);

INSERT INTO t VALUES (NULL, NULL);   -- ✅ SUCCEEDS
-- col <> 'X'          → UNKNOWN
-- other IS NOT NULL   → FALSE
-- UNKNOWN OR FALSE    → UNKNOWN     → admitted
```

Naive translation: `col <> "X" || other.IsSome` $\Rightarrow$ `false || false = false` $\Rightarrow$ **rejected**. Divergence on row one.

### Kleene $K_3$, not `bool`

Lattice: $\textsf{False} < \textsf{Unknown} < \textsf{True}$, with $\wedge = \min$, $\vee = \max$, $\neg$ order-reversing.

```fsharp
/// PostgreSQL truth. Never collapse this to bool before the acceptance test.
type SqlTruth =
    | True
    | Unknown
    | False

module SqlTruth =

    let negate = function
        | True    -> False
        | False   -> True
        | Unknown -> Unknown

    let conj a b =
        match a, b with
        | False, _   | _, False   -> False      // absorbing — checked first
        | Unknown, _ | _, Unknown -> Unknown
        | True, True              -> True

    let disj a b =
        match a, b with
        | True, _    | _, True    -> True       // absorbing — checked first
        | Unknown, _ | _, Unknown -> Unknown
        | False, False            -> False

    /// The acceptance predicate. The ONLY place SqlTruth becomes bool.
    let admits = function
        | True | Unknown -> true
        | False          -> false

    /// Comparisons are Unknown-propagating; IS NULL is not.
    let compare3 (f: 'a -> 'a -> bool) (l: 'a option) (r: 'a option) =
        match l, r with
        | Some a, Some b -> if f a b then True else False
        | _              -> Unknown

    let isNull (v: 'a option) = if Option.isNone v then True else False
```

**Invariant:** `admits` is the sole `SqlTruth -> bool` in the codebase. Make it an FsAssay rule.

---

## $\S 4$. Fidelity Is a Type, Not a Percentage

Every translated constraint carries its own verdict. Silence is not evidence.

```fsharp
type Fidelity =
    /// F# verdict ≡ PostgreSQL verdict on all inputs.
    | Exact
    /// Equivalent within a stated domain (e.g. declared precision/scale).
    | ExactWithin  of domain: string
    /// F# rejects rows PostgreSQL admits.  ⚠ breaks reads of existing data.
    | Stronger     of reason: string
    /// F# admits rows PostgreSQL rejects.  ⚠ breaks writes; surfaces at INSERT.
    | Weaker       of reason: string
    /// Not expressible in a value: UNIQUE, FK, EXCLUDE, cross-row.
    | DatabaseOnly of reason: string
    /// Recognised, not modelled. Reported in LOSS.md.
    | Unsupported  of reason: string

type Loss =
    | Dropped      of construct: string * reason: string
    | Normalized   of before: string * after: string
    | Approximated of construct: string * fidelity: Fidelity
```

Strict-mode gate — note that **`DatabaseOnly` passes**; it is an honest hand-off, not a defect:

$$\text{Pass} \iff \forall c.\; \text{fid}(c) \in \{\textsf{Exact},\, \textsf{ExactWithin},\, \textsf{DatabaseOnly}\}$$

$$\textsf{Stronger} \;\vee\; \textsf{Weaker} \;\vee\; \textsf{Unsupported} \;\Longrightarrow\; \text{Block}$$

### Type mappings return capability, never a bare type

```fsharp
type Mapping<'a> =
    | MExact       of 'a
    | MExactWithin of bounds: string * 'a
    | MLossy       of reason: string * 'a
    | MUnsupported of reason: string
```

| PostgreSQL | F# | Verdict |
|---|---|---|
| `NUMERIC(10,2)` | `decimal` | `MExactWithin "p≤28, s=2"` |
| `NUMERIC` (unbounded) | `decimal` | `MLossy "PG range ⊃ System.Decimal"` |
| `TIMESTAMPTZ` | `Instant` (NodaTime) | `MExact` |
| `TIMESTAMP` | `LocalDateTime` | `MExact` |
| `TEXT` + `citext`/collation | `string` | `MLossy "ordinal ≠ PG collation"` |
| `JSONB` | `JsonDocument` | `MLossy "no schema"` |
| `TEXT[]` | `string array` | `MExactWithin "1-D only"` |

---

## $\S 5$. Constraint Classes Do Not Collapse

A smart constructor validates the **shape** of a value. It cannot prove no other row holds it.

$$\text{Unique}(sku) \;\not\equiv\; \text{ValidSku}(sku)$$

| Class | Example | Correct home |
|---|---|---|
| Scalar-local | `price > 0` | Smart constructor |
| String-local | regex / length | Smart constructor **if semantics match** |
| Row-local | `total = subtotal + tax` | Row validator |
| Relational | `UNIQUE(sku)` | `DatabaseOnly` |
| Referential | FK | Typed id + persistence check |
| Cross-row | `EXCLUDE` | `DatabaseOnly` |
| Default | `DEFAULT now()` | Creation policy, not a type |
| Generated | `GENERATED AS` | Read-only computed field |
| Transition | status rules | State-transition function |
| Trigger | server behaviour | `Unsupported`, declared |

---

## $\S 6$. Primitives Are Layered, Not Banned

⚠️ *"Don't enforce no-primitives-leaked"* is too broad as stated. The rule isn't wrong — its **scope** was.

```fsharp
// Layer 0 — persistence. Primitives are CORRECT here. This is unparsed input.
type ProductRow =
    { Id: Guid; Sku: string; Price: decimal; WeightGrams: int option }

// Layer 1 — the parse boundary. This is where the law lives.
let parseProduct (row: ProductRow) : Result<Product, ParseError list> =
    // applicative accumulation — collect all failures, not the first
    ...

// Layer 2 — domain. Zero primitives. Non-negotiable.
type Product =
    { Id: ProductId; Sku: Sku; Price: Price; WeightGrams: Weight option }
```

$$\text{Row} \xrightarrow{\;\text{parse}\;} \text{Result}\langle \text{Domain} \rangle$$

FsAssay scoping, not FsAssay softening:

| Path | Primitives | I/O | Exceptions |
|---|---|---|---|
| `Generated/Rows/` | ✅ required | ❌ | ❌ |
| `Generated/Domain/` | ❌ | ❌ | ❌ |
| `Infrastructure/` | ✅ | ✅ | ❌ (`Result` at boundary) |

---

## $\S 7$. Reverse Reads the Manifest, Not the Body

`type Sku = private Sku of string` does not contain `^[A-Z0-9-]{6,30}$`. The regex lives in the **body** of `create` — arbitrary F#, early returns, helper calls. Recovering it means decompiling a decision procedure into a SQL boolean expression. Don't.

$$\mathcal{R} : (\text{F\#} \times \text{Manifest}) \rightarrow \text{IR}$$

$$\mathcal{R}(\text{arbitrary F\#}) = \textsf{Inconclusive}$$

Every generated file carries provenance:

```fsharp
//------------------------------------------------------------
// <auto-generated>
//   PgPrism 0.1.0 · IR digest sha256:9f2a…
//   Source: 01-schema.sql @ git 4c1de2a · PostgreSQL 16.3
//   Do not edit. Fix the IR mapping or the generator template.
// </auto-generated>
//------------------------------------------------------------
```

Manifest for v1 (can desync; the digest detects it). **Reified witnesses** later — those cannot desync, at the cost of type machinery. Know which one you took.

---

## $\S 8$. Coverage Is Four Ratios

$$C_{parse} = \frac{\text{constraints parsed}}{\text{constraints present}} \qquad C_{model} = \frac{\text{constraints landing in the IR}}{\text{constraints parsed}}$$

$$C_{verify} = \frac{\text{translations differentially verified}}{\text{translations emitted}} \qquad C_{evid} = \frac{\text{evidence complete}}{\text{evidence required}}$$

$$\text{Accept} \iff C_{parse} = C_{model} = C_{verify} = C_{evid} = 1 \;\wedge\; |\textsf{Unsupported}| = 0$$

$C_{model}$ is the one that hurts, and **47/47 on a schema you authored measures nothing** — you picked the constraints knowing the target model. Before writing a line: run the IR against Discourse, Gitea, Keycloak, Sentry, Mastodon. One day. De-risks eighty.

---

## $\S 9$. Evidence, Not Proof

`PROOF.md` claims discharged obligations. You have none yet. Ship `EVIDENCE.md` + `evidence.json` + **`LOSS.md`**.

| Source | Projection | Fidelity | Verification |
|---|---|---|---|
| `chk_price` | `Price.create` | `Exact` | PG differential, 10k cases |
| `chk_sku` | `Sku.create` | `ExactWithin "POSIX∩PCRE"` | PG differential, 10k cases |
| `UNIQUE(sku)` | — | `DatabaseOnly` | PG-owned |
| `chk_ship_window` | — | `Unsupported "3-col temporal"` | **LOSS.md** |

**`LOSS.md` is the honest artifact, and it is not among your 25 features.**

### Analyzer of record

"Every output verified by FsAssay" is false — FsAssay cannot judge Zod, OpenAPI, Mermaid, or SQL.

| Artifact | Analyzer of record |
|---|---|
| F# types/validators | `dotnet build` → FSharpLint → FsAssay |
| SQL | PostgreSQL itself, pinned version |
| Zod / TS | `tsc` + differential vs PG |
| OpenAPI | OpenAPI 3.1 validator |
| Generators | FsCheck vs validators **and** vs PG |

FsAssay judges whether required evidence **exists**. It does not become the evidence.

### Assurance ladder (browser must not lie)

`Preview` → `Compiled` → `Linted` → `Assayed` → `PG-validated` → `Fidelity-verified`

The browser shows `Preview validation: passed · Full FsAssay: not executed in browser`. That honesty *raises* trust. `FsAssayLite` displaying "Grade A" does not.

---

## $\S 10$. The Oracle Test

The single artifact that makes PgPrism trustworthy. PostgreSQL **is** the oracle — don't reimplement its semantics, differentially test against them.

$$\forall v.\quad \text{admits}(\text{fsharp}(v)) \;=\; \text{admits}_{pg}(v)$$

```fsharp
let ``generated validator agrees with PostgreSQL`` (conn: NpgsqlConnection) =
    let prop (v: string) =
        let fsharpVerdict = Sku.create v |> Result.isOk
        let pgVerdict =
            // savepoint isolates the failure; never throws out
            match tryInsertWithSavepoint conn "products" [ "sku", box v ] with
            | Ok ()   -> true
            | Error _ -> false
        fsharpVerdict = pgVerdict
    Check.One({ Config.QuickThrowOnFailure with MaxTest = 10_000 }, prop)
```

```sql
-- one savepoint per candidate; constraint violation rolls back, harness survives
SAVEPOINT probe;
  INSERT INTO products (sku) VALUES ($1);
ROLLBACK TO SAVEPOINT probe;
```

Pinned container. Same major version recorded in the manifest.

---

## $\S 11$. Easy Wins

Cheap, high-leverage, do these before anything architectural.

**1. Anchor every generated regex — `\A…\z`, never `^…$`.** In .NET `$` also matches *before a trailing newline*:

```fsharp
Regex.IsMatch(v, @"^[A-Z0-9-]{6,30}$")     // "PROD-001\n" ✅ PASSES  ← bug
Regex.IsMatch(v, @"\A[A-Z0-9-]{6,30}\z")   // "PROD-001\n" ❌ rejected ← correct
```

PostgreSQL's `~` rejects the newline. One-line generator fix; silently corrupts every string type until made.

**2. `length()` counts characters; `String.Length` counts UTF-16 units.** Diverges on the first emoji or surrogate pair:

```fsharp
// PG: length('👍') = 1     .NET: "👍".Length = 2
let pgLength (s: string) = s.EnumerateRunes() |> Seq.length
```

**3. Emit a range guard for unbounded `NUMERIC`** — PG's range exceeds `System.Decimal`. Mark `MLossy` and guard, don't silently truncate.

**4. Introspect instead of parse for the CLI path** — PG normalizes constraints for you, free, versioned:

```sql
SELECT c.conname,
       c.contype,                          -- c=check p=pk f=fk u=unique x=exclude
       pg_get_constraintdef(c.oid) AS def, -- PG's own normalized rendering
       t.relname
FROM   pg_constraint c
JOIN   pg_class t ON t.oid = c.conrelid
JOIN   pg_namespace n ON n.oid = t.relnamespace
WHERE  n.nspname = 'public'
ORDER  BY t.relname, c.conname;
```

**5. `SELECT version()` into the manifest.** Fidelity claims are version-scoped or they are noise.

**6. `TIMESTAMPTZ → Instant`, `TIMESTAMP → LocalDateTime` (NodaTime).** `DateTime` conflates them and loses the distinction permanently.

**7. Ship `LOSS.md` in the very first slice.** Empty at first. It makes the honest habit structural rather than aspirational.

---

## $\S 12$. $\pi_0$ — One Table, End to End

```
libpg_query  →  IR  →  Rows.fs + Domain.fs + Validators.fs
                    →  dotnet build → FSharpLint → FsAssay
                    →  differential test vs pinned PG 16
                    →  EVIDENCE.md + LOSS.md + evidence.json
```

Scope: `CREATE TABLE`, scalar types, `NOT NULL`, single-column PK, one numeric `CHECK`, one regex `CHECK`. That is all.

**Deferred:** reverse, multi-DB, migrations, OpenAPI, triggers, browser, "proof."

**Prerequisite, unconditional:** FsAssay's fabricated locations and non-executing tests are fixed first. A Grade A from a broken grader is strictly worse than no grade — it is false confidence in a proof-shaped wrapper, which is the precise failure constitution-first discipline exists to prevent.

---

## The Corrected Slogan

> One schema. Many projections. **Every translation accounted for.**
> No unsupported construct silently treated as proof.

$$\text{FsAssay proves the generated F\# obeys the constitution.}$$
$$\text{The fidelity engine proves it still } \textit{means} \text{ what PostgreSQL meant.}$$

$$\blacksquare$$



# Yes. JSON In. F# Out. Here's the Actual Pipeline.

---

## What pgsqlparser Returns

`Parser.ParsePlpgsql(sql)` returns a **JSON string** — PostgreSQL's internal parse tree serialized as JSON. Your example produces something like:

```json
{
  "stmts": [{
    "stmt": {
      "CreateFunctionStmt": {
        "funcname": [{"String": {"str": "get_all_foo"}}],
        "returnType": {"names": [{"String": {"str": "foo"}}]},
        "options": [
          {"DefElem": {"defname": "language", "arg": {"String": {"str": "plpgsql"}}}}
        ]
      }
    }
  }]
}
```

**It's JSON. You deserialize it. You walk the tree. You extract what you need.**

---

## But CanonFlow Doesn't Need Functions

Your example parses a `CREATE FUNCTION`. CanonFlow needs `CREATE TABLE`:

```fsharp
// What CanonFlow actually parses:
let sql = """
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    sku VARCHAR(30) NOT NULL,
    price NUMERIC(10,2) NOT NULL,
    CONSTRAINT chk_sku CHECK (sku ~ '^[A-Z0-9-]{6,30}$'),
    CONSTRAINT chk_price CHECK (price > 0 AND price <= 999999.99)
);
"""

let result = Parser.Parse(sql)  // ← Parse, not ParsePlpgsql
// result.Value = JSON parse tree
```

| Parse Method | What It Handles | CanonFlow Needs? |
|---|---|---|
| `Parser.Parse(sql)` | SQL statements (DDL, DML) | ✅ **Yes** |
| `Parser.ParsePlpgsql(sql)` | PL/pgSQL functions | ❌ No |
| `Parser.ParseType(sql)` | Type definitions | 🟡 Maybe (ENUMs) |

---

## The Full Pipeline: JSON → F#

### Step 1: Parse (pgsqlparser does this)

```fsharp
open PgSqlParser
open System.Text.Json

let parseSchema (sql: string) : JsonElement =
    let result = Parser.Parse(sql)
    if result.Error <> null then
        failwith $"Parse error: {result.Error}"
    JsonDocument.Parse(result.Value).RootElement
```

### Step 2: Decode JSON → Canonical IR (YOU write this)

```fsharp
// PgPrism/Decode.fs — ~200 lines. This is your IP.

module PgPrism.Decode

open System.Text.Json

// ── The Canonical IR ──────────────────────────────────────
type SqlType =
    | Uuid | Text | Varchar of int | Int | BigInt
    | Numeric of precision: int * scale: int
    | Bool | TimestampTz | Timestamp | Date
    | TextArray | Jsonb

type CheckExpr =
    | LengthRange of column: string * min: int * max: int
    | RegexMatch of column: string * pattern: string
    | InList of column: string * values: string list
    | NumericRange of column: string * min: decimal option * max: decimal option
    | NonNegative of column: string
    | ArithmeticEq of lhs: string * rhs: ArithExpr
    | TemporalOrder of nullableCol: string * requiredCol: string
    | And of CheckExpr * CheckExpr
    | Or of CheckExpr * CheckExpr
    | IsNull of CheckExpr
    | IsNotNull of CheckExpr
    | Unsupported of string

and ArithExpr =
    | Col of string
    | Lit of decimal
    | Add of ArithExpr * ArithExpr
    | Sub of ArithExpr * ArithExpr
    | Mul of ArithExpr * ArithExpr

type ColumnDef = {
    Name: string
    Type: SqlType
    NotNull: bool
    PrimaryKey: bool
    Default: string option
    Unique: bool
}

type ConstraintDef = {
    Name: string
    Expr: CheckExpr
}

type TableDef = {
    Name: string
    Columns: ColumnDef list
    Constraints: ConstraintDef list
}

// ── The Decoder ───────────────────────────────────────────

let decodeSchema (root: JsonElement) : TableDef list =
    root.GetProperty("stmts")
    |> Seq.map (fun stmtNode ->
        let stmt = stmtNode.GetProperty("stmt")
        if stmt.TryGetProperty("CreateStmt") |> fst then
            Some (decodeCreateTable (stmt.GetProperty("CreateStmt")))
        else None)
    |> Seq.choose id
    |> Seq.toList

and decodeCreateTable (ct: JsonElement) : TableDef =
    let name = ct.GetProperty("relation").GetProperty("relname").GetString()
    let elts = ct.GetProperty("tableElts")

    let columns =
        elts |> Seq.choose (fun elt ->
            if elt.TryGetProperty("ColumnDef") |> fst then
                Some (decodeColumn (elt.GetProperty("ColumnDef")))
            else None)
        |> Seq.toList

    let constraints =
        elts |> Seq.choose (fun elt ->
            if elt.TryGetProperty("Constraint") |> fst then
                let c = elt.GetProperty("Constraint")
                let contype = c.GetProperty("contype").GetInt32()
                if contype = 4 then  // CONSTR_CHECK
                    Some { Name = c.GetProperty("conname").GetString()
                           Expr = decodeExpr (c.GetProperty("raw_expr")) }
                else None
            else None)
        |> Seq.toList

    { Name = name; Columns = columns; Constraints = constraints }

and decodeColumn (cd: JsonElement) : ColumnDef =
    let name = cd.GetProperty("colname").GetString()
    let typeName = cd.GetProperty("typeName")
    let sqlType = decodeType typeName
    let constraints =
        if cd.TryGetProperty("constraints") |> fst then
            cd.GetProperty("constraints") |> Seq.toList
        else []
    let notNull = constraints |> List.exists (fun c ->
        c.GetProperty("Constraint").GetProperty("contype").GetInt32() = 1)
    let pk = constraints |> List.exists (fun c ->
        c.GetProperty("Constraint").GetProperty("contype").GetInt32() = 2)
    { Name = name; Type = sqlType; NotNull = notNull; PrimaryKey = pk
      Default = None; Unique = false }

and decodeType (tn: JsonElement) : SqlType =
    let names = tn.GetProperty("names")
    let lastName = names.[names.GetArrayLength() - 1].GetProperty("String").GetProperty("str").GetString()
    match lastName with
    | "uuid" -> Uuid
    | "text" -> Text
    | "varchar" ->
        let typemod = if tn.TryGetProperty("typemod") |> fst then tn.GetProperty("typemod").GetInt32() else -1
        if typemod > 0 then Varchar (typemod - 4) else Text
    | "int4" | "integer" -> Int
    | "int8" | "bigint" -> BigInt
    | "numeric" ->
        let typemod = if tn.TryGetProperty("typemod") |> fst then tn.GetProperty("typemod").GetInt32() else -1
        if typemod > 0 then
            let precision = ((typemod - 4) >>> 16) &&& 0xFFFF
            let scale = (typemod - 4) &&& 0xFFFF
            Numeric (precision, scale)
        else Numeric (0, 0)  // unbounded
    | "bool" -> Bool
    | "timestamptz" -> TimestampTz
    | "timestamp" -> Timestamp
    | "date" -> Date
    | "_text" -> TextArray
    | "jsonb" -> Jsonb
    | other -> Text  // fallback

and decodeExpr (expr: JsonElement) : CheckExpr =
    // A_Expr: binary operations (>, <, =, ~, AND, OR)
    if expr.TryGetProperty("A_Expr") |> fst then
        let ae = expr.GetProperty("A_Expr")
        let opName = ae.GetProperty("name").[0].GetProperty("String").GetProperty("str").GetString()
        match opName with
        | "~" ->
            let col = getColumnRef (ae.GetProperty("lexpr"))
            let pattern = getStringConst (ae.GetProperty("rexpr"))
            RegexMatch (col, pattern)
        | "AND" ->
            And (decodeExpr (ae.GetProperty("lexpr")), decodeExpr (ae.GetProperty("rexpr")))
        | "OR" ->
            Or (decodeExpr (ae.GetProperty("lexpr")), decodeExpr (ae.GetProperty("rexpr")))
        | ">" | ">=" | "<" | "<=" | "=" | "!=" ->
            let col = getColumnRef (ae.GetProperty("lexpr"))
            let value = getNumericConst (ae.GetProperty("rexpr"))
            match opName with
            | ">" when value = 0m -> NonNegative col  // price > 0
            | _ -> NumericRange (col, Some value, None)  // simplified
        | _ -> Unsupported $"Operator: {opName}"

    // NullTest: IS NULL / IS NOT NULL
    elif expr.TryGetProperty("NullTest") |> fst then
        let nt = expr.GetProperty("NullTest")
        let nulltestType = nt.GetProperty("nulltesttype").GetInt32()
        let arg = decodeExpr (nt.GetProperty("arg"))
        if nulltestType = 0 then IsNull arg else IsNotNull arg

    // FuncCall: length(), lower(), etc.
    elif expr.TryGetProperty("FuncCall") |> fst then
        let fc = expr.GetProperty("FuncCall")
        let funcName = fc.GetProperty("funcname").[0].GetProperty("String").GetProperty("str").GetString()
        match funcName with
        | "length" ->
            let arg = decodeExpr (fc.GetProperty("args").[0])
            LengthRange (getColName arg, 0, 0)  // bounds decoded from AND
        | _ -> Unsupported $"Function: {funcName}"

    // ColumnRef: column reference
    elif expr.TryGetProperty("ColumnRef") |> fst then
        Unsupported "bare column ref"

    else
        Unsupported "unknown expression"

and getColumnRef (expr: JsonElement) : string =
    let cr = expr.GetProperty("ColumnRef")
    cr.GetProperty("fields").[0].GetProperty("String").GetProperty("str").GetString()

and getStringConst (expr: JsonElement) : string =
    expr.GetProperty("A_Const").GetProperty("sval").GetProperty("str").GetString()

and getNumericConst (expr: JsonElement) : decimal =
    let ac = expr.GetProperty("A_Const")
    if ac.TryGetProperty("ival") |> fst then
        decimal (ac.GetProperty("ival").GetProperty("ival").GetInt32())
    elif ac.TryGetProperty("fval") |> fst then
        decimal (ac.GetProperty("fval").GetProperty("fval").GetString())
    else 0m

and getColName (expr: CheckExpr) : string =
    match expr with
    | Unsupported s -> s
    | _ -> "unknown"
```

### Step 3: IR → F# Types (CanonFlow projection)

```fsharp
// CanonFlow/ProjectDomain.fs — IR → smart constructors

module CanonFlow.ProjectDomain

let generateDomainType (table: TableDef) : string =
    let fields =
        table.Columns
        |> List.map (fun col ->
            let fsharpType =
                match col.Type with
                | Uuid -> "Guid"
                | Text -> "string"
                | Varchar _ -> "string"
                | Int -> "int"
                | BigInt -> "int64"
                | Numeric (_, scale) when scale > 0 -> "decimal"
                | Numeric _ -> "decimal"
                | Bool -> "bool"
                | TimestampTz -> "Instant"
                | Timestamp -> "LocalDateTime"
                | Date -> "DateOnly"
                | TextArray -> "string array"
                | Jsonb -> "JsonDocument"
            let nullability = if col.NotNull then "" else " option"
            $"    {col.Name}: {fsharpType}{nullability}")
        |> String.concat "\n"

    $"""type {table.Name} =
{{
{fields}
}}"""

let generateSmartConstructor (constraintDef: ConstraintDef) : string =
    match constraintDef.Expr with
    | RegexMatch (col, pattern) ->
        $"""type {col} = private {col} of string
module {col} =
    let value ({col} v) = v
    let create (v: string) : Result<{col}, CanonFlowError> =
        if System.Text.RegularExpressions.Regex.IsMatch(v, @"\A{pattern}\z")
        then Ok ({col} v)
        else Error {{ Field = "{col}"; Message = "Constraint {constraintDef.Name} failed" }}"""

    | NumericRange (col, min, max) ->
        let minCheck = min |> Option.map (fun m -> $"v > {m}m") |> Option.defaultValue "true"
        let maxCheck = max |> Option.map (fun m -> $"v <= {m}m") |> Option.defaultValue "true"
        $"""type {col} = private {col} of decimal
module {col} =
    let value ({col} v) = v
    let create (v: decimal) : Result<{col}, CanonFlowError> =
        if {minCheck} && {maxCheck}
        then Ok ({col} v)
        else Error {{ Field = "{col}"; Message = "Constraint {constraintDef.Name} failed" }}"""

    | NonNegative col ->
        $"""type {col} = private {col} of decimal
module {col} =
    let value ({col} v) = v
    let create (v: decimal) : Result<{col}, CanonFlowError> =
        if v >= 0m
        then Ok ({col} v)
        else Error {{ Field = "{col}"; Message = "Constraint {constraintDef.Name} failed" }}"""

    | _ -> $"// Unsupported: {constraintDef.Name}"
```

### Step 4: The Complete Pipeline

```fsharp
// Program.fs — The whole thing in 20 lines

open PgSqlParser
open System.Text.Json
open PgPrism.Decode
open CanonFlow.ProjectDomain

[<EntryPoint>]
let main argv =
    let sqlFile = argv.[0]
    let outputDir = argv.[1]
    let sql = System.IO.File.ReadAllText(sqlFile)

    // Step 1: Parse SQL → JSON parse tree (pgsqlparser)
    let result = Parser.Parse(sql)
    if result.Error <> null then
        eprintfn "Parse error: %s" result.Error
        1
    else
        let json = JsonDocument.Parse(result.Value).RootElement

        // Step 2: Decode JSON → Canonical IR (your decoder)
        let tables = decodeSchema json

        // Step 3: Project IR → F# types (CanonFlow)
        for table in tables do
            let domainCode = generateDomainType table
            System.IO.File.WriteAllText(
                System.IO.Path.Combine(outputDir, $"{table.Name}.fs"), domainCode)

            for constraintDef in table.Constraints do
                let scCode = generateSmartConstructor constraintDef
                System.IO.File.AppendAllText(
                    System.IO.Path.Combine(outputDir, "Types.fs"), scCode + "\n\n")

        printfn "Generated %d tables" tables.Length
        0
```

---

## The Answer to Your Question

**Yes.** pgsqlparser creates JSON. You convert that JSON into F#. The pipeline is:

```
SQL text
  → pgsqlparser (NuGet, P/Invoke, libpg_query)
  → JSON parse tree (PostgreSQL's internal format)
  → Your decoder (~200 lines F#)
  → Canonical Schema IR (F# discriminated unions)
  → CanonFlow projections (Types.fs, Validators.fs, Generators.fs, Zod, OpenAPI)
  → FsAssay verification (scan generated code)
```

**The JSON is the intermediate format. The IR is the real product. The projections are the output.**

$$\text{SQL} \xrightarrow{\text{pgsqlparser}} \text{JSON} \xrightarrow{\text{your decoder}} \text{IR} \xrightarrow{\text{CanonFlow}} \text{F\#, TS, YAML, MD}$$

The JSON is not the destination. It's the **raw material.** Your decoder turns it into the Canonical IR. CanonFlow turns the IR into everything else.

$$\blacksquare$$
