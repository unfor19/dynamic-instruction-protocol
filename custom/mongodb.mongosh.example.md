# MongoDB Shell (mongosh) Custom Preferences Example

This file shows an example configuration based on the template.

## Connection Information (using environment variables)

- **Username:** Stored in environment variable `$DIP_MONGODB_USER`
- **Password:** Stored in environment variable `$DIP_MONGODB_PASS`
- **Server Address:** Stored in environment variable `$DIP_MONGODB_SERVER` (e.g., `mongodb://localhost:27017`)
- **Database Name:** Stored in environment variable `$DIP_MONGODB_DBNAME`

## Example Connection Command

You can connect using a command like this in your shell:

```bash
mongosh "$DIP_MONGODB_SERVER/$DIP_MONGODB_DBNAME" --username "$DIP_MONGODB_USER" --password "$DIP_MONGODB_PASS" --authenticationDatabase admin
```

*Note: Adjust `--authenticationDatabase` if your user is defined in a different database.* 