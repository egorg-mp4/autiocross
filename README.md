-------
## Error codes:

*~~300 — intermission to intercourse~~*

**404 — page not found**

*~~440 — page with pitchfork~~* 

**400 — no server token**

**403 — access denide**


## Methods
-------
 **GET `/api/spotify/playlists`**
 
- New: Lists all Spotify playlists for the currently authenticated local user using the server-stored Spotify token (pages through Spotify's /me/playlists). Returns an aggregated array of playlist objects as returned by Spotify.
- Auth: requires app JWT (Authorization: Bearer `<app-jwt>`). Use this if you've already persisted a Spotify token for the user (see `/spotify/login?state=... or/api/spotify/authorize-url`).
-------
**GET `/api/spotify/authorize-url`**

  - Returns JSON `{ "url": "https://accounts.spotify.com/authorize?...." }` with the authorize URL prefilled with `state=<username>` for the current logged-in user. Useful for single-page frontends.
  - Auth: requires app JWT.
-------
**POST `/api/spotify/playlists/{spotifyId}/import`**

  - New: Import the specified Spotify playlist into our DB for the current authenticated user, importing all tracks (pages through `/playlists/{id}/tracks` to collect all tracks).
  - Auth: requires app JWT.
  - Uses the server-stored Spotify token (refreshes it automatically if expired). If no server token is available, returns a 400 with no_spotify_token_available.
  - Response: the saved Playlist entity (includes saved tracks).
-------
**POST `/api/playlists/import?spotifyId={playlistId}`**
  - Legacy/import-by-header option: imports a single Spotify playlist (first-page or full if you call the server-side import) into our database under the authenticated user.
  - You can provide a Spotify access token in the header Spotify-Access-Token: `<token>` to import using a token you already have.
  - Auth: requires app JWT.
-------
**GET `/api/playlists`**
  - List playlists saved in our service for the current authenticated user.
  - Auth: requires app JWT.
-------
**POST `/api/auth/register`**
  - Registers a local user. Request JSON: `{ username, email, password }`
  - Response: created user object (password is omitted in the response / set to null).
-------
**POST `/api/auth/login`**
- Logs in and returns a JWT. Request JSON: `{ usernameOrEmail, password }`
- Response: `{ token: "<JWT>" }`
-------
**GET `/spotify/login?state=<username>`**
  - Redirects (302) the caller to the Spotify authorize page. If you include `state=<username>` the server will attempt to persist returned tokens for that username when Spotify redirects back to `/spotify/callback`.
  - Example: http://localhost:8080/spotify/login?state=alice
-----
**GET `/spotify/callback`**
  - The OAuth redirect URI registered with Spotify should point here. Spotify will call this with ?`code=<auth_code>&state=<state>` on success or `?error=...` on failure.
  - The server exchanges the code for tokens with Spotify and returns a JSON body containing token_response (the raw Spotify token response) and playlists (first page of playlists fetched using the returned access token). If state matches a local username, the token response will be persisted for that local user in `user_spotify_tokens`.
-----
























