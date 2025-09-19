---
tags:
  - SQL
  - Debugging
---
---
#### Descrizione
Il metodo `DoSQL` usa `retQ` (somma di `ExecuteNonQuery()`) come <span style="color: #2d98da">contatore di query riuscite.</span>

- `ExecuteNonQuery()` non restituisce un booleano di successo:
  - `INSERT/UPDATE/DELETE` → numero di righe modificate (0 se nessuna riga)
  - `CREATE/ALTER/DROP` → -1
- La condizione `retQ >= queriesSQL.Count - 1` fallisce anche se tutte le query sono valide.

#### Conseguenze
- Transazione `rollback` anche con query corrette
- Logica inattendibile

💡 Vedi la soluzione → [[DoSQL - Soluzione retQ]]

---

#### Metodo `DoSQL`

```csharp
public static int DoSQL(List<string> queriesSQL) 
{
    int retQ = 0;
    string connectionString = System.Configuration.ConfigurationManager.AppSettings["SQL"].ToString();

    using (SqlConnection connectionSQL = new SqlConnection(connectionString))
    {
        connectionSQL.Open();
        SqlCommand command = connectionSQL.CreateCommand();
        SqlTransaction transaction;

        transaction = connectionSQL.BeginTransaction(IsolationLevel.ReadCommitted);
        command.Connection = connectionSQL;
        command.Transaction = transaction;

        try
        {
            foreach (string query in queriesSQL)
            {
                command.CommandText = query;
                int i = command.ExecuteNonQuery(); // <-- ritorno variabile
                retQ += i;
            }

            if (retQ >= queriesSQL.Count - 1)
                transaction.Commit();
            else
            {
                transaction.Rollback();
                return -1;
            }
        }
        catch (Exception ex)
        {
            transaction.Rollback();
            return -1;
        }
    }

    return retQ;
}
```

#### Comportamento osservato ```-->``` Rollback

Il check di commit:

```csharp
if (retQ >= queriesSQL.Count - 1)
    transaction.Commit();
else
{
    transaction.Rollback();
    return -1;
}
```

**fallisce costantemente**.

Motivo tecnico:

1. `retQ` viene calcolato come **somma dei valori restituiti da `ExecuteNonQuery()`**.
  
2.  `ExecuteNonQuery()` <span style="color: #2d98da">NON RESTIRUISCE</span> sempre "1" per ogni query.  
    Restituisce:
    - Numero di righe modificate per `INSERT`, `UPDATE`, `DELETE` (può essere `0` se non ci sono righe corrispondenti)
    - `-1` per query DDL (`CREATE`, `ALTER`, `DROP`)
    
```-->``` La logica `retQ >= queriesSQL.Count - 1` **non corrisponde al successo di tutte le query**.


##### Analisi tecnica step-by-step

Esempio query:

```sql
1) INSERT INTO Clienti (Nome) VALUES ('Mario');       -- ritorna 1
2) UPDATE Clienti SET Nome = 'Luigi' WHERE Id = 1;   -- ritorna 1
3) DELETE FROM Clienti WHERE Id = 999;               -- ritorna 0
4) CREATE TABLE Test (Id INT);                       -- ritorna -1
```

Calcolo `retQ`:

```csharp
int retQ = 0;

foreach (string query in queriesSQL)
{
    int i = command.ExecuteNonQuery();
    retQ += i; // somma dei ritorni
}

if (retQ >= queriesSQL.Count - 1)
    transaction.Commit();
else
{
    transaction.Rollback();
    return -1;
}
```
##### Step by step

|Step|Query|ExecuteNonQuery()|retQ|
|---|---|---|---|
|1|INSERT|1|1|
|2|UPDATE|1|2|
|3|DELETE (nessuna riga)|0|2|
|4|CREATE TABLE|-1|1|

Condizione finale:
```c#
queriesSQL.Count = 4
queriesSQL.Count - 1 = 3
retQ = 1
```
  -  `retQ >= 3` → **false**
  - Transazione → `Rollback`
  - 
##### Diagnosi tecnica

- `ExecuteNonQuery()` **non è un indicatore booleano di successo**, ma un <span style="color: #2d98da">contatore righe modificate</span> o -1 per DDL.
    
- La somma `retQ` non rappresenta “quante query sono andate a buon fine”.
    
- Conseguenza: **tutte le transazioni che contengono query DDL o UPDATE senza righe modificate falliscono il commit**, anche se tutte le query sono sintatticamente corrette.