# migrate

[![Build Status](https://travis-ci.org/mattes/migrate.svg?branch=master)](https://travis-ci.org/mattes/migrate)
[![GoDoc](https://godoc.org/github.com/mattes/migrate?status.svg)](https://godoc.org/github.com/mattes/migrate)

migrate is a migration helper written in Go. Use it in your existing Golang code 
or run migration commands via the CLI. 

```
Go Code  import github.com/mattes/migrate/migrate
CLI      go get github.com/mattes/migrate
```


## Available Drivers

 * [PostgreSQL](https://github.com/mattes/migrate/tree/master/driver/postgres)
 * Bash (planned)

Need another driver? Just implement the [Driver interface](http://godoc.org/github.com/mattes/migrate/driver#Driver) and open a PR.


## Usage from Terminal

```bash
# install
go get github.com/mattes/migrate

# create new migration file in path
migrate -url driver://url -path ./migrations create migration_file_xyz

# apply all available migrations
migrate -url driver://url -path ./migrations up

# roll back all migrations
migrate -url driver://url -path ./migrations down

# roll back the most recently applied migration, then run it again.
migrate -url driver://url -path ./migrations redo

# run down and then up command
migrate -url driver://url -path ./migrations reset

# show the current migration version
migrate -url driver://url -path ./migrations version

# apply the next n migrations
migrate -url driver://url -path ./migrations migrate +1
migrate -url driver://url -path ./migrations migrate +2
migrate -url driver://url -path ./migrations migrate +n

# roll back the previous n migrations
migrate -url driver://url -path ./migrations migrate -1
migrate -url driver://url -path ./migrations migrate -2
migrate -url driver://url -path ./migrations migrate -n
```


## Usage in Go

See GoDoc here: http://godoc.org/github.com/mattes/migrate/migrate

```go
import "github.com/mattes/migrate/migrate"

// use synchronous versions of migration functions ...
allErrors, ok := migrate.UpSync("driver://url", "./path")
if !ok {
  fmt.Println("Oh no ...")
  // do sth with allErrors slice
}

// use the asynchronous version of migration functions ...
pipe := migrate.NewPipe()
go migrate.Up(pipe, "driver://url", "./path")
// pipe is basically just a channel
// write your own channel listener. see writePipe() in main.go as an example.
```

## Migration files

The format of migration files looks like this:

```
001_initial_plan_to_do_sth.up.sql     # up migration instructions
001_initial_plan_to_do_sth.down.sql   # down migration instructions
002_xxx.up.sql
002_xxx.down.sql
...
```

Why two files? This way you could still do sth like 
``psql -f ./db/migrations/001_initial_plan_to_do_sth.up.sql`` and there is no
need for any custom markup language to divide up and down migrations. Please note
that the filename extension depends on the driver.


## Credits

 * https://bitbucket.org/liamstask/goose


