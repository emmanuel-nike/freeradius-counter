# Freeradius Limit Data Usage (Mikrotik)

The Mikrotik-Total-Limit radius attribute for radreply only limits the data for a session and still allows the user to login again and use another total limit for that session continuously.
If you want to limit the data usage daily or monthly or forever an additional implementation in the sql counter section is added here. These are namely **noresetquotalimit**, **dailyquotalimit**, **monthlyquotalimit**.
The sqlcounter module is updated to include all the sections for the data limits and the counter folder presents all queries in mysql and pgsql dialect.

## Usage

Simply replace the counter directory with the one here, update your sqlcounter module with this one and also update the enabled site authorize section where the counters are to include the counters here and also shown in the default file in sites-available.

## License

This software is licensed under the MIT License.
