# csv2psql

[![Build Status](https://travis-ci.org/nmccready/csv2psql.png?branch=master)](https://travis-ci.org/nmccready/csv2psql)
[![GitHub stars](https://img.shields.io/github/stars/nmccready/csv2psql.svg?style=social)](https://github.com/nmccready/csv2psql)

**Convert CSV files to PostgreSQL tables with a single command.** Supports primary keys, unique indexes, data type overrides, merge operations, and more.

## Install

```bash
python setup.py install
```

## Quick Start

```bash
# Generate SQL from CSV
csv2psql --schema=public --key=student_id,class_id example/enrolled.csv > enrolled.sql

# Load into PostgreSQL
psql -f enrolled.sql

# Or pipe directly
cat data.csv | csv2psql --schema=public --key=id | psql

# Push to database immediately
cat data.csv | csv2psql --now --postgres_url=postgresql://user:pass@localhost/mydb
```

## Usage

```
csv2psql [options] input.csv > output.sql
cat input.csv | csv2psql [options] | psql
cat input.csv | csv2psql --now [options]
```

## Options

### Connection & Output

| Option | Description |
|--------|-------------|
| `--now` | Pipe SQL directly to PostgreSQL |
| `--postgres_url=URL` | PostgreSQL connection URL |
| `--dumptype=TYPE` | Output format: `copy` (PSQL COPY) or `sql` (INSERT/UPDATE) |

### Schema & Table

| Option | Description |
|--------|-------------|
| `--schema=NAME` | PostgreSQL schema name |
| `--tablename=NAME` | Override table name (defaults to CSV filename) |
| `--databasename=NAME` | Database name (required for merge operations) |
| `--role=NAME` | Database role for the transaction |

### Keys & Indexes

| Option | Description |
|--------|-------------|
| `--key=a:b:c` | Create primary key on columns a, b, c |
| `--unique=a:b:c` | Create unique index on columns a, b, c |
| `--primaryfirst=BOOL` | Put primary key first (default: false) |

### Data Types & Columns

| Option | Description |
|--------|-------------|
| `--datatype=NAME[,NAME]:TYPE` | Override data type for specified columns |
| `--dates=KEY1,KEY2:FORMAT` | Specify date columns and their format |
| `--serial=NAME` | Add auto-incrementing SERIAL column |
| `--timestamp=NAME` | Add insertion timestamp column |
| `--sniff=N` | Rows to scan for type detection (default: 1000) |
| `--utf8` | Force UTF8 client encoding |

### Table Operations

| Option | Description |
|--------|-------------|
| `--append` | Skip table creation, insert only |
| `--cascade` | Drop tables with CASCADE |
| `--is_merge` | Create temp table for merge operations |
| `--is_dump` | Use pg_dump for schema, then generate merge SQL |
| `--new_table_name=NAME` | Rename table (use with `--is_dump`) |
| `--delete_temp_table` | Delete temp table after merge |

### Advanced

| Option | Description |
|--------|-------------|
| `--joinkeys=KEY1,KEY2:KEYNAME` | Combine columns into a joined key |
| `--analyze_table=BOOL` | Run ANALYZE after import |
| `--do_add_cols` | Add timestamp/serial columns on final run |
| `--append_sql` | Read raw SQL from stdin |
| `--modified_timestamp=NAME` | Override modified_time column name |
| `--skipp_stored_proc_modified_time` | Skip stored proc (default: false) |

### Environment Variables

| Variable | Description |
|----------|-------------|
| `CSV2PSQL_SCHEMA` | Default value for `--schema` |
| `CSV2PSQL_ROLE` | Default value for `--role` |

## Examples

### Basic import

```bash
csv2psql --schema=public --key=id data.csv > import.sql
psql -f import.sql
```

### Merge data into existing table

```bash
csv2psql --schema=public --key=id --is_merge --databasename=mydb \
  --is_dump --delete_temp_table data.csv > merge.sql
psql -f merge.sql
```

### Custom data types

```bash
csv2psql --datatype=price:numeric --datatype=name:varchar \
  --dates=created_at,updated_at:%Y-%m-%d data.csv > output.sql
```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

1. Fork the repo
2. Create your feature branch
3. Commit your changes
4. Open a Pull Request

## Sponsor

If you find this project useful, consider [sponsoring @nmccready](https://github.com/sponsors/nmccready) to support ongoing maintenance and development. ❤️

## License

See [LICENSE](./LICENSE) for details.
