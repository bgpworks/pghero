# PgHero

Postgres 쿼리 성능을 확인하는 용도로 사용한다.

## Query Stats

https://github.com/ankane/pghero/blob/master/guides/Query-Stats.md

먼저 RDS에서 `shared_preload_libraries` 을 활성화 해야 한다.

## Historical Query Stats

https://github.com/ankane/pghero/blob/master/guides/Docker.md#historical-query-stats

별도 Stat 보관용 DB를 만들고 `PGHERO_STATS_DATABASE_URL` Env로 connection string을 설정한다.

Stat DB에 아래 table을 추가 해야 한다.

```
CREATE TABLE "pghero_space_stats" (
  "id" bigserial primary key,
  "database" text,
  "schema" text,
  "relation" text,
  "size" bigint,
  "captured_at" timestamp
);
CREATE INDEX ON "pghero_space_stats" ("database", "captured_at");
```

Scheduler를 통해 아래 커맨드를 10분에 한번씩 실행

```
rake pghero:capture_query_stats
```

Stat 데이터가 엄청 커질수 있기 때문에 아래 커맨드를 매일 한번씩 실행

```
rake pghero:clean_query_stats KEEP_DAYS=14
```
