### About 
⚡ High performance dynamic vector tiles for web maps with HTTP caching.  ⚡   
 If you're looking for blazing fast, production ready dynamic vector tiles for web maps and other spatial applications, then you've come to the right place.

Built for Docker with postgres, pg_tileserv and varnish.

What is [pg_tileserv?](https://github.com/CrunchyData/pg_tileserv)  
 A very thin Postgres/PostGIS-only tile server in Go. Takes in HTTP tile requests, executes SQL, returns MVT tiles.

What is [varnish?](https://github.com/eea/eea.docker.varnish)  
 Varnish is an HTTP accelerator designed for content-heavy dynamic web sites as well as APIs.

### Instructions
#### 1. Pull a Postgres container for testing which supports PostGIS.  
 (skip this if you already have a Postgres instance)

```bash
docker pull postgis/postgis
docker run --name postgres -p 15432:5432 -e POSTGRES_PASSWORD=admin1234 -d postgis/postgis
```

#### 2. Install PostGIS in your Postgres instance if it isn't already installed.

```sql
create extension postgis;
select postgis_full_version();
```

#### 3. Grab your Postgres environment connection string and paste at the bottom of `docker-compose.yml`  
 eg. for a local instance of Postgres on Docker:

```bash
postgres://postgres:admin1234@host.docker.internal:15432/postgres
```

#### 4. Make configuration changes as required

You can make configuration changes to the tile server and varnish settings within `docker-compose.yml` and `/config/pg_tileserv.toml`.

Although the default settings in these files should be suitable to fit most generic needs.

#### 5. Set permissions on the app, run Docker compose `build`, and then run pg_tileserv and varnish together

```bash
cd ../path/to/tileserv-varnish
chmod a+x ../path/to/tileserv-varnish/pg_tileserv
docker-compose build --no-cache
docker-compose up
```

#### 6. Voilà !

The pg_tileserv service should now be running behind the varnish HTTP cache.

#### 7. Testing

Access the service at `<host>:80`, view some spatial tiles and check the pg_tileserv logs in the terminal. 

You'll notice that at first, logs will appear as you pan around on the map. Afterwards, the cache will be serving them so logging stops until the cache is refreshed.

#### 8. Metrics/Logs

Tile metrics are enabled too, so if you need to see metrics for pg_tileserv for Grafana, they can be accessed at `<host>:80/metrics`.

Varnish dashboard can be viewed at `<host>:6085/stats` with the credentials set in `docker-compose.yml`.

#### Note

Guidance provided by the experts over at [CrunchyData](https://blog.crunchydata.com/).
