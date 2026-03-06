# APPS - gnome-keyring

- A system-wide secret vault that applications talk to through a standard API(`org.freedesktop.secrets`); not a password manager UI and not a browser extension.
- Apps never read encrypted files themselves.  
- They always ask a running service for secrets.
- When an application needs a credential:
  `App → libsecret → D-Bus → Secret Service API  → gnome-keyring-daemon → Encrypted keyring file on disk`
- Secrets are only decrypted in RAM inside the daemon.

## Main use:
Used automatically by:
- Chromium → login tokens, cookies
- VSCode → settings sync credentials
- Git → HTTP credentials (via libsecret)
- SSH → key passphrases (agent)
- NetworkManager → WiFi/VPN secrets
- Native Debian apps

## ✖ Not a Browser Vault
Browser extensions like LastPass or Bitwarden:
- Manage their own vaults
- Store secrets separately
- Do `not` use gnome-keyring

## The Components
### 1. libsecret (Client Library)
- Applications don't implement secret storage themselves. They link against: `libsecret`
- `libsecret` does:
    - Implements the Secret Service protocol
    - Sends requests over D-Bus
    - Talks to: `org.freedesktop.secrets`
- So the flow is always: App → libsecret → D-Bus
- Never: App → encrypted file
- libsecret is basically: `A D-Bus client that implements the Secret Service protocol`

### 2. D-Bus (Transport Layer)
- D-Bus is local IPC used for secure communication between user services.
- Applications:
    - Send method calls
- Services:
    - Listen on named APIs
- Example flow:
1. App requests a secret from:`org.freedesktop.secrets` 
2. D-Bus routes request to:`gnome-keyring-daemon`
3. Daemon:
   - decrypts the secret
   - returns it to the app
4. Apps never need to know:
   - where secrets live
   - how encryption works
   - what backend is used

### 3. gnome-keyring-daemon (Secret Manager)
- This is the actual secret backend.
- Responsibilities:
  - Encrypt secrets before writing to disk
  - Decrypt secrets into RAM
  - Track lock/unlock state
  - Provide SSH agent support
  - Expose Secret Service API
  
Encrypted keyrings are stored at:`~/.local/share/keyrings/`
Secrets remain encrypted at rest.
- A `secret` = one stored credential (e.g. API token, password, SSH passphrase). Internally, each secret is an `item`
- A `keyring` = a container that holds many secrets
  - Like:
    - secret = file
    - keyring = directory
- Keyrings are stored on disk as encrypted files in:
  - `~/.local/share/keyrings/`
- The `entire keyring file is encrypted`, not each app’s config separately
- When the keyring is:
  - `Locked` → secrets are encrypted on disk
  - `Unlocked` → daemon decrypts into RAM temporarily
- Apps never access:
  - the keyring file
  - the encryption key
  - other secrets
- Apps only ask:
  - “give me the secret matching these attributes”

## Session Behavior
- Keyrings are:
  - Locked when the session starts
  - Unlocked once (usually at login)
  - Kept decrypted in daemon memory only
- Apps can retrieve secrets without repeated prompts.

## Storing Custom Secrets (CLI)
- Use: `secret-tool`
- Example:
  ```bash
    secret-tool store 
    --label='db-pass' 
    service myapp 
    user foo
  ```
- Secrets are indexed by attributes.
- Retrieve:
  ```bash
    secret-tool lookup service myapp user foo
  ```

## Why Use a Daemon?
- It is a daemon becaus Secrets must be:
  - decrypted safely
  - kept in memory
  - locked automatically
  - shared across apps
- name: `gnome-keyring-daemon`
- Handles:
  - encryption
  - unlock state
  - secure memory storage

## Why Use the Secret Service API?
- Apps just talk to:`org.freedesktop.secrets`
- standard interface
- backend independence
- access control via session bus

You can replace GNOME Keyring with:
- KeePassXC Secret Service
- other compliant providers

## Required Debian Packages

Install everything:

```

sudo apt install 
gnome-keyring 
libsecret-1-0 
libsecret-tools

```

Installing only the daemon is not enough.

---

## Headless Startup (No GNOME Session)

Start secrets + SSH agent manually:

```

eval "$(
gnome-keyring-daemon 
--start 
--components=secrets,ssh 
--foreground
)"

```

Environment variables must be exported from this call  
(e.g. `DBUS_SESSION_BUS_ADDRESS`).

---

## Cheat Sheet

### Start daemon

```

eval "$(gnome-keyring-daemon --start --components=secrets,ssh)"

```

### Store secret

```

secret-tool store --label='API' service github user foo

```

### Lookup secret

```

secret-tool lookup service github user foo

```

### Remove secret

```

secret-tool clear service github user foo

```

### SSH agent

```

ssh-add ~/.ssh/id_ed25519
ssh-add -l

```
```