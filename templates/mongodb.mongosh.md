# MongoDB Shell (mongosh) Template

This template outlines connection details for `mongosh` using environment variables.

## Connection Information (using environment variables)

- **Username:** Use environment variable `DIP_MONGODB_USER`
- **Password:** Use environment variable `DIP_MONGODB_PASS`
- **Server Address:** Use environment variable `DIP_MONGODB_SERVER` (e.g., `mongodb://localhost:27017` or `mongodb+srv://cluster.mongodb.net`)
- **Database Name:** Use environment variable `DIP_MONGODB_DBNAME`

## Example Connection Command

Connect using a command similar to this, substituting environment variables:

```bash
mongosh "$DIP_MONGODB_SERVER/$DIP_MONGODB_DBNAME" --username "$DIP_MONGODB_USER" --password "$DIP_MONGODB_PASS" --authenticationDatabase <auth_db>
```

*Note: Replace `<auth_db>` with the appropriate authentication database (often `admin` or the user's specific database).* 