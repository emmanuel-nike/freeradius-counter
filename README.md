# Freeradius Limit Data Usage (Mikrotik)

The Mikrotik-Total-Limit radius attribute for radreply only limits the data for a session and still allows the user to login again and use another total limit for that session continuously.
If you want to limit the data usage daily or monthly or forever an additional implementation in the sql counter section is added here. These are namely **noresetquotalimit**, **dailyquotalimit**, **monthlyquotalimit**.
The sqlcounter module is updated to include all the sections for the data limits and the counter folder presents all queries in mysql and pgsql dialect.

You may experience an error that says ```Reference "${modules.sql.dialect}" not found``` when you run freeradius. To fix this issue, ensure the sql module is loaded first in the radiusd.conf file. Edit section "modules" like this:

```modules { $INCLUDE ${confdir}/mods-enabled/sql $INCLUDE ${confdir}/mods-enabled/ }```


## Usage

Simply replace the counter directory with the one here, update your sqlcounter module with this one and also update the enabled site authorize section where the counters are to include the counters here and also shown in the default file in sites-available.

## Duplicate accounting / auth hardening

A misbehaving NAS (for example a MikroTik that has the RADIUS server listed twice, or one whose RADIUS timeout is short enough to retransmit) can send the same `Access-Request` / `Accounting-Request` more than once. Without protection, each copy is processed and written to the database, producing **duplicate `radpostauth` rows** (identical `Access-Accept`, same `authdate`) and **duplicate `radacct` rows** (two rows with the same `Acct-Session-Id`).

`sites-available/default` is hardened against this:

- **`cleanup_delay = 5`** on every `listen { }` socket (auth and acct, IPv4 and IPv6). FreeRADIUS caches each reply for this many seconds; a duplicate that arrives within the window is answered from the cache instead of being reprocessed, so it is not written to the database again. Raise the value if your NAS retransmit interval is longer than 5s.
- **`acct_unique`** in the `preacct { }` section computes a stable `Acct-Unique-Session-Id`, so duplicate accounting packets map to the same session rather than creating a new row.

> Note: this suppresses *duplicate/retransmitted packets*. If a NAS is configured to send to this server as two distinct destinations, also fix it on the router side (`/radius print` — the server IP should appear once) and raise its RADIUS `timeout`.

## License

This software is licensed under the MIT License.
