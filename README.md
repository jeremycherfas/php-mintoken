# Mintoken

A minimal [IndieAuth][] compatible [Token Endpoint][].

Several times I have been asked if there is a token endpoint available to be used together with [Selfauth][]. A minimal solution that would just work for issuing access tokens that is self-hostable on a server with PHP.

I do not have a need for a token endpoint like this myself, thus developing one would go against my [selfdogfooding][] principles. But because I have a general interest in the IndieAuth specification, here is an implementation anyway!

[IndieAuth]: https://indieauth.net/
[Token Endpoint]: https://indieauth.spec.indieweb.org/#token-endpoint
[Selfauth]: https://github.com/Inklings-io/selfauth
[selfdogfooding]: https://indieweb.org/selfdogfood

## Setup

1. Download [the latest release](https://github.com/Zegnat/php-mintoken/releases/latest) from GitHub and extract the files.
   
2. Create an SQLite database by your favourite means, and use `schema.sql` to create the expected tables. This setup assumes the database file is called `tokens.db`.
   
   **Tip**: this can be done on the command line:
   
   ```bash
   sqlite3 tokens.db < schema.sql
   ```
   
3. Define trusted authorization endpoints in the `settings` table of the SQLite database. Mintoken will only check codes with these endpoints, and takes the `me` value they return as trusted without further verification.
   
   E.g. if we take [the example setup for Selfauth](https://github.com/Inklings-io/selfauth#setup), the endpoint `https://example.com/auth/` should be whitelisted.
   
   **Tip**: this can be done on the command line:
   
   ```bash
   sqlite3 tokens.db 'INSERT INTO settings VALUES ("endpoint", "https://example.com/auth/");'
   ```
   
4. Upload the SQLite database to a secure directory on your server. Make sure it is not publicly available to the web!
   
5. Edit `endpoint.php` so line 5 defines the correct path to the SQLite database as the value for `MINTOKEN_SQLITE_PATH`.
   
6. Put `endpoint.php` anywhere on your server where it is available to the web. (This could be in the same folder as Selfauth.)
   
7. Make the token endpoint discoverable. Either by defining a `Link` HTTP header, or adding the following to the `<head>` of the pages where you also link to your `authorization_endpoint`:
   
   ```html
   <link rel="token_endpoint" href="https://example.com/auth/endpoint.php">
   ```
   
   (The `href` value in this example must point at your uploaded `endpoint.php` file.)

## License

The BSD Zero Clause License (0BSD). Please see the LICENSE file for
more information.
