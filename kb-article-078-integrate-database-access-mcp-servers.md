# How to integrate database access through MCP servers

**Article ID**: KB-078
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 25-30 minutes

## Overview
This guide shows you how to integrate database access in Claude Code Web through Model Context Protocol (MCP) servers. MCP is an open, standardized protocol that enables Claude to securely connect to and query databases like PostgreSQL, MySQL, SQLite, and many others, allowing you to perform natural language data analysis and database operations.

## Prerequisites
- Claude Code Web environment with MCP support
- Basic understanding of database concepts and SQL
- Access to a database instance (local or remote)
- Database connection credentials (preferably read-only)
- Node.js/npm installed (for most MCP servers)
- Familiarity with command-line operations

## Steps

### Step 1: Understand MCP Server Architecture

MCP (Model Context Protocol) is a client-server protocol built on JSON-RPC 2.0 that allows Claude to access external data sources securely. Database MCP servers act as bridges between Claude and your databases.

**Key Components:**
- **MCP Client**: Built into Claude Code
- **MCP Server**: Runs locally or remotely to access your database
- **Transport Layer**: Communication method (stdio or SSE)
- **Database Connection**: Your actual database instance

### Step 2: Choose Your Database MCP Server

Select the appropriate MCP server based on your database type:

**Official/Popular Database Servers:**

- **PostgreSQL**: `@modelcontextprotocol/server-postgres` or `@bytebase/dbhub`
- **MySQL/MariaDB**: `@modelcontextprotocol/server-mysql` or `@bytebase/dbhub`
- **SQLite**: `@modelcontextprotocol/server-sqlite`
- **Multi-Database**: `@bytebase/dbhub` (supports PostgreSQL, MySQL, MariaDB, SQL Server)
- **Specialized**: Astra DB, Apache Doris, AnalyticDB, ClickHouse, and more

For this guide, we'll use examples for the most common databases.

### Step 3: Configure PostgreSQL Database Access

Add a PostgreSQL MCP server using the `claude mcp add` command:

```bash
claude mcp add --transport stdio postgres -- npx -y @bytebase/dbhub \
  --dsn "postgresql://readonly:password@localhost:5432/analytics"
```

**Alternative using environment variables (more secure):**

```bash
# Set environment variable first
export DATABASE_URL="postgresql://readonly:password@localhost:5432/analytics"

# Add MCP server with environment variable
claude mcp add postgres -e DATABASE_URL=$DATABASE_URL -- \
  npx -y @modelcontextprotocol/server-postgres
```

**Connection string format:**
```
postgresql://[username]:[password]@[host]:[port]/[database]
```

### Step 4: Configure MySQL Database Access

For MySQL or MariaDB databases:

```bash
claude mcp add --transport stdio mysql -- npx -y @modelcontextprotocol/server-mysql \
  "mysql://user:password@localhost:3306/database"
```

**Using environment variables:**

```bash
export MYSQL_URL="mysql://user:password@localhost:3306/database"

claude mcp add mysql -e MYSQL_URL=$MYSQL_URL -- \
  npx -y @modelcontextprotocol/server-mysql
```

**Connection string format:**
```
mysql://[username]:[password]@[host]:[port]/[database]
```

### Step 5: Configure SQLite Database Access

SQLite is simpler since it uses local file-based databases:

```bash
claude mcp add --transport stdio sqlite -- npx -y @modelcontextprotocol/server-sqlite \
  --db-path /path/to/your/database.db
```

**Example with specific database file:**

```bash
claude mcp add sqlite -- npx -y @modelcontextprotocol/server-sqlite \
  --db-path /home/user/projects/myapp/data.db
```

### Step 6: Configure Multi-Database Access

For projects with multiple databases, use a universal database server like DBHub:

```bash
# Add the server
claude mcp add --transport stdio databases -- npx -y @bytebase/dbhub \
  --dsn "postgresql://user:pass@host:5432/db1" \
  --dsn "mysql://user:pass@host:3306/db2"
```

**Using JSON configuration for complex setups:**

```bash
claude mcp add-json "database-mcp" '{
  "command": "uvx",
  "args": ["database-mcp"],
  "env": {
    "POSTGRES_URL": "postgresql://localhost:5432/analytics",
    "MYSQL_URL": "mysql://localhost:3306/products"
  }
}'
```

### Step 7: Verify MCP Server Installation

List all configured MCP servers to confirm your database connection:

```bash
claude mcp list
```

You should see your database server(s) listed with their configuration details.

**Test the connection** by asking Claude to query your database:

```
"Show me the tables in the database"
"Describe the schema of the users table"
"Query the first 10 rows from the customers table"
```

### Step 8: Implement Security Best Practices

**Critical security measures:**

1. **Use read-only credentials:**
```sql
-- PostgreSQL: Create read-only user
CREATE USER readonly_user WITH PASSWORD 'secure_password';
GRANT CONNECT ON DATABASE analytics TO readonly_user;
GRANT USAGE ON SCHEMA public TO readonly_user;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly_user;
```

2. **Store credentials securely:**
```bash
# Never hardcode credentials in configuration
# Use environment variables instead
export DB_USER="readonly_user"
export DB_PASSWORD="secure_password"
export DB_HOST="localhost"
export DB_NAME="analytics"

# Reference in connection string
export DATABASE_URL="postgresql://${DB_USER}:${DB_PASSWORD}@${DB_HOST}:5432/${DB_NAME}"
```

3. **Restrict database access:**
   - Use firewall rules to limit database connections
   - Enable SSL/TLS for remote connections
   - Never connect to production databases directly
   - Create separate analytics or reporting databases

4. **Configure connection limits:**
```bash
# Limit concurrent connections
claude mcp add postgres -e DATABASE_URL=$DATABASE_URL \
  -e MAX_CONNECTIONS=5 -- \
  npx -y @modelcontextprotocol/server-postgres
```

### Step 9: Use Database Capabilities in Claude Code

Once configured, you can interact with your database using natural language:

**Schema exploration:**
```
"What tables are available in the database?"
"Show me the structure of the orders table"
"What columns are in the customers table?"
```

**Data queries:**
```
"Find all customers who made purchases in the last 30 days"
"Calculate the average order value by product category"
"Show me the top 10 selling products"
```

**Analysis tasks:**
```
"Identify any null values in the email column"
"Compare sales trends between Q1 and Q2"
"Generate a summary of user activity by region"
```

### Step 10: Manage and Update MCP Servers

**Remove a database server:**
```bash
claude mcp remove postgres
```

**Update server configuration:**
```bash
# Remove old configuration
claude mcp remove mysql

# Add new configuration
claude mcp add mysql -e MYSQL_URL=$NEW_MYSQL_URL -- \
  npx -y @modelcontextprotocol/server-mysql
```

**View server details:**
```bash
claude mcp list --verbose
```

## Expected Results

After successfully integrating database access through MCP servers, you should be able to:

1. **See your database server** listed when running `claude mcp list`
2. **Query database schemas** by asking Claude about table structures
3. **Execute read queries** through natural language requests to Claude
4. **Analyze data** without writing SQL directly
5. **Receive structured responses** with query results formatted in tables or JSON
6. **Handle errors gracefully** with clear messages about connection or query issues

**Example interaction:**
```
You: "Show me all tables in the database"

Claude: "I can see the following tables in your analytics database:
- users (15,432 rows)
- orders (48,921 rows)
- products (1,234 rows)
- categories (45 rows)

Would you like me to explore any of these tables?"
```

## Troubleshooting

### Connection Refused or Timeout
**Problem**: "Error: connect ECONNREFUSED" or connection timeout
**Solution**:
- Verify your database is running: `sudo systemctl status postgresql` (or mysql/mariadb)
- Check firewall settings allow the connection
- Confirm the host and port in your connection string are correct
- For remote databases, ensure your IP is whitelisted
- Test connection manually: `psql -h localhost -U username -d database`

### Authentication Failed
**Problem**: "Error: password authentication failed"
**Solution**:
- Double-check username and password in your connection string
- Verify the user exists in the database
- Check if the user has necessary permissions
- Ensure special characters in passwords are URL-encoded
- For PostgreSQL, check `pg_hba.conf` authentication settings

### MCP Server Not Found
**Problem**: "Server 'postgres' not found in configuration"
**Solution**:
- Run `claude mcp list` to see configured servers
- Ensure you used `claude mcp add` successfully (check for error messages)
- Verify the server name matches what you used in the add command
- Try removing and re-adding: `claude mcp remove postgres && claude mcp add ...`

### Permission Denied on Tables
**Problem**: "ERROR: permission denied for table users"
**Solution**:
- Grant SELECT permissions to your database user:
  ```sql
  GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly_user;
  ```
- For future tables, set default privileges:
  ```sql
  ALTER DEFAULT PRIVILEGES IN SCHEMA public
  GRANT SELECT ON TABLES TO readonly_user;
  ```

### npx Package Installation Fails
**Problem**: "Error: Failed to install @modelcontextprotocol/server-postgres"
**Solution**:
- Ensure Node.js and npm are installed: `node --version && npm --version`
- Clear npm cache: `npm cache clean --force`
- Try installing the package globally first: `npm install -g @modelcontextprotocol/server-postgres`
- Check your internet connection and npm registry access
- Use alternative registry if needed: `npm config set registry https://registry.npmjs.org/`

### SSL/TLS Certificate Errors
**Problem**: "Error: self signed certificate in certificate chain"
**Solution**:
- For development only, you can disable SSL verification (not recommended for production):
  ```bash
  export NODE_TLS_REJECT_UNAUTHORIZED=0
  ```
- Better: Add the CA certificate to your system's trust store
- Use `sslmode=require` in PostgreSQL connection string for proper SSL
- For PostgreSQL: `postgresql://user:pass@host:5432/db?sslmode=require`

## Additional Tips

- **Start with read-only access**: Always configure MCP servers with read-only database credentials to prevent accidental data modifications
- **Use connection pooling**: For high-traffic scenarios, configure the MCP server with connection pooling to manage database connections efficiently
- **Test with sample databases**: Before connecting to important data sources, test your MCP configuration with sample or development databases
- **Monitor query performance**: Be aware that complex natural language requests may generate expensive SQL queries; monitor database performance
- **Use environment variables**: Store all sensitive information (passwords, connection strings) in environment variables, never in configuration files
- **Create dedicated users**: Set up specific database users for MCP access with minimal required permissions
- **Log queries for debugging**: Enable query logging in your database to understand what SQL is being generated from natural language requests
- **Version control safe configs**: If you commit MCP configurations to git, use environment variable references only, never hardcoded credentials
- **Use database views**: Create database views for complex queries or to limit data exposure, then grant MCP user access only to these views
- **Set query timeouts**: Configure appropriate timeout values to prevent long-running queries from blocking resources
- **Document your schema**: Add comments to tables and columns in your database; some MCP servers can use these to better understand your data
- **Backup before experimenting**: Even with read-only access, test your MCP setup on database backups or replicas first

## Related Articles
- KB-061: How to connect MCP servers to external data sources
- KB-075: How to create custom MCP server integrations
- KB-077: How to implement secure credential management for MCP servers

## Sources
- Model Context Protocol Servers GitHub Repository - https://github.com/modelcontextprotocol/servers (Accessed: November 16, 2025)
- Database Connections via MCP - Developer Toolkit - https://developertoolkit.ai/en/shared-workflows/mcp-ecosystem/database-mcp/ (Accessed: November 16, 2025)
- PostgreSQL MCP Server - PulseMCP - https://www.pulsemcp.com/servers/modelcontextprotocol-postgres (Accessed: November 16, 2025)
- SQLite MCP Server - PulseMCP - https://www.pulsemcp.com/servers/modelcontextprotocol-sqlite (Accessed: November 16, 2025)
- How to install a MySQL MCP server - 4sysops - https://4sysops.com/archives/how-to-install-a-mysql-mcp-server/ (Accessed: November 16, 2025)
- Introducing the Model Context Protocol - Anthropic - https://www.anthropic.com/news/model-context-protocol (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
