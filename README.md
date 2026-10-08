# Gator - CLI RSS Blog Aggregator

Gator is a multi-user command-line RSS feed aggregator written in Go and backed by PostgreSQL. It allows multiple users on the same machine to subscribe to RSS feeds, automatically collect new articles in the background, and browse posts directly from the terminal.

---

## Prerequisites

Before running Gator, ensure you have the following installed on your system:

1. **[Go](https://go.dev/dl/)** (version 1.20 or later)
2. **[PostgreSQL](https://www.postgresql.org/)** (version 15 or later)
3. **[Goose](https://github.com/pressly/goose)** (database migration tool)

---

## Installation

### Option 1: Install using `go install`

Install the compiled binary directly into your `$GOPATH/bin`:

```bash
go install github.com/xuerns/gator@latest
```

Make sure `$HOME/go/bin` is in your system's `PATH`:
```bash
export PATH="$PATH:$HOME/go/bin"
```

### Option 2: Build from Source

Clone the repository and build the binary:

```bash
git clone https://github.com/xuerns/blogArgregator.git
cd blogArgregator
go build -o gator
```

Now you can run `./gator` directly.

---

## Database Setup & Migrations

1. **Start PostgreSQL and create the database**:
   Make sure PostgreSQL is running on `localhost:5432`, and create a database named `gator`:
   ```bash
   createdb gator
   ```

2. **Install Goose** (if not already installed):
   ```bash
   go install github.com/pressly/goose/v3/cmd/goose@latest
   ```

3. **Run database migrations**:
   Apply all migrations from the `sql/schema` directory:
   ```bash
   cd sql/schema
   goose postgres "postgres://postgres:postgres@localhost:5432/gator" up
   cd ../..
   ```

---

## Configuration File

Gator reads and stores user sessions in a configuration file located at `~/.gatorconfig.json`.

Create or update `~/.gatorconfig.json` in your home directory:

```json
{
  "db_url": "postgres://postgres:postgres@localhost:5432/gator?sslmode=disable",
  "current_user_name": "your_username"
}
```

- `db_url`: PostgreSQL connection string (with `?sslmode=disable` for local dev).
- `current_user_name`: Name of the currently active user (automatically updated by `login` and `register`).

---

## Command Reference

### User Management
- **Register a new user**:
  ```bash
  gator register <username>
  ```
- **Login as an existing user**:
  ```bash
  gator login <username>
  ```
- **List all users**:
  ```bash
  gator users
  ```

### Feed Management
- **Add a new feed**:
  Adds a feed and automatically follows it for the current user:
  ```bash
  gator addfeed "The Boot.dev Blog" "https://blog.boot.dev/index.xml"
  ```
- **List all feeds**:
  ```bash
  gator feeds
  ```
- **Follow a feed by URL**:
  ```bash
  gator follow "https://blog.boot.dev/index.xml"
  ```
- **List feeds followed by current user**:
  ```bash
  gator following
  ```
- **Unfollow a feed**:
  ```bash
  gator unfollow "https://blog.boot.dev/index.xml"
  ```

### Aggregator & Browsing
- **Run the background aggregator**:
  Scrapes followed feeds periodically in an ongoing loop (can be stopped with `Ctrl+C`):
  ```bash
  gator agg 30s
  ```
- **Browse posts**:
  Views the latest collected posts from feeds you follow (optional limit defaults to 2):
  ```bash
  gator browse
  gator browse 10
  ```

### Utility
- **Reset database**:
  Deletes all users, feeds, feed follows, and posts from the database:
  ```bash
  gator reset
  ```

---

## License

This project is licensed under the MIT License.
