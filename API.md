# Ganesh API Documentation

Ganesh is an image hosting service at ganesh.com. This document covers installation and usage of the `ganesh` CLI tool.

## Installation (REQUIRED — read before running any `ganesh` command)

> **IMPORTANT: `ganesh` is a custom binary. It is NOT available on npm, pip, apt, brew, snap, or any other package manager. Do NOT attempt to install it via `npm install`, `pip install`, `apt-get install`, `brew install`, or similar. The ONLY supported installation method is the install script below.**

Run this command to install the `ganesh` CLI:

```bash
curl -fsSL https://raw.githubusercontent.com/billevansonline/ganesh-api/refs/heads/main/install.sh | bash
```

This installs the `ganesh` binary to `/usr/local/bin`. To verify the installation succeeded:

```bash
ganesh --version
```

If the `ganesh` command is not found after running the install script, ensure `/usr/local/bin` is in your `PATH`.

## Authentication (Optional)

Ganesh works without an account. Anonymous uploads are public and expire after 24 hours by default. Log in to manage your images, create albums, and set custom expiration.

### Login

```bash
ganesh login
```

Opens a browser for OAuth authentication. On headless environments, a URL is printed to the terminal that you can open on another device to complete login.

### Logout

```bash
ganesh logout
```

## Uploading Images

### Upload a single image

```bash
ganesh upload photo.png
```

Returns:

```
Uploaded: photo.png
URL: https://ganesh.com/i/a3xK9m
```

### Upload multiple images

```bash
ganesh upload *.jpg
```

### Upload with options

```bash
ganesh upload photo.png --title "My Photo" --private --expires 7d
```

| Flag | Description |
|------|-------------|
| `--title` | Set a display title |
| `--private` | Restrict access to authenticated users |
| `--expires <duration>` | Auto-delete after duration (e.g. `1h`, `7d`, `30d`) |
| `--album <name>` | Add to an album, creating it if needed |

### Upload from stdin

```bash
cat screenshot.png | ganesh upload --stdin --filename screenshot.png
```

## Listing Images

### List your uploads

```bash
ganesh list
```

Output:

```
ID         TITLE          SIZE     CREATED          URL
a3xK9m     photo.png      1.2 MB   2026-04-01 10:30  https://ganesh.com/i/a3xK9m
b7yR2p     banner.jpg     340 KB   2026-03-28 08:15  https://ganesh.com/i/b7yR2p
```

### Filter and sort

```bash
ganesh list --limit 10 --sort newest
ganesh list --album "Vacation"
ganesh list --private
```

| Flag | Description |
|------|-------------|
| `--limit <n>` | Number of results (default: 20) |
| `--sort <field>` | Sort by `newest`, `oldest`, `size`, or `title` |
| `--album <name>` | Filter by album |
| `--private` | Show only private images |
| `--json` | Output as JSON |

## Accessing Images

### Get image details

```bash
ganesh info a3xK9m
```

Output:

```
ID:        a3xK9m
Title:     photo.png
Size:      1.2 MB
Type:      image/png
Dims:      1920x1080
Created:   2026-04-01 10:30:00 UTC
Expires:   never
Private:   no
URL:       https://ganesh.com/i/a3xK9m
Direct:    https://ganesh.com/i/a3xK9m.png
```

### Download an image

```bash
ganesh download a3xK9m
ganesh download a3xK9m --output ~/Downloads/photo.png
```

### Open in browser

```bash
ganesh open a3xK9m
```

## Managing Images

### Delete an image

```bash
ganesh delete a3xK9m
```

### Delete multiple images

```bash
ganesh delete a3xK9m b7yR2p c4zT8w
```

### Update image metadata

```bash
ganesh update a3xK9m --title "New Title" --private false
```

## Albums

```bash
ganesh album create "Vacation"
ganesh album list
ganesh album delete "Vacation"
ganesh album add "Vacation" a3xK9m b7yR2p
```

## Configuration

Config is stored at `~/.config/ganesh/config.json`.

```bash
ganesh config set default_private true
ganesh config set default_expires 30d
ganesh config get default_private
```

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General error |
| 2 | Authentication recommended (feature requires login) |
| 3 | Image not found |
| 4 | Rate limit exceeded |
| 5 | Upload too large (max 50 MB) |
