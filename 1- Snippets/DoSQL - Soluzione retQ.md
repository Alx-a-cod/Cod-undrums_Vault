---
tags:
  - SQL
  - Debugging
---

## Idea chiave

- Non usare `retQ` per determinare il successo.  

- Esegui commit **solo se tutte le query terminano senza eccezioni**.

```csharp
public static int DoSQL(List<string> queriesSQL)
{
    int totalAffectedRows = 0;
	    string connectionString = System.Configuration.ConfigurationManager.AppSettings["SQL"];

    using (SqlConnection connectionSQL = new SqlConnection(connectionString))
    {
        connectionSQL.Open();
        SqlTransaction transaction = connectionSQL.BeginTransaction(IsolationLevel.ReadCommitted);

        using (SqlCommand command = connectionSQL.CreateCommand())
        {
            command.Connection = connectionSQL;
            command.Transaction = transaction;

            try
            {
                foreach (string query in queriesSQL)
                {
                    command.CommandText = query;
                    System.Diagnostics.Debug.WriteLine("Eseguo query: " + query);

                    int affected = command.ExecuteNonQuery();
                    System.Diagnostics.Debug.WriteLine(" -> Righe interessate: " + affected);

                    totalAffectedRows += affected;
                }

                // Tutte le query sono andate bene
                transaction.Commit();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine("Errore durante l'esecuzione: " + ex.Message);
                transaction.Rollback();
                return -1; // indica errore
            }
        }
    }

    return totalAffectedRows;
}

```