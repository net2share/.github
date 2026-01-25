# Staying Anonymous While Contributing

If you need to remain anonymous while contributing to Net2Share projects, follow these steps to protect your identity.

## 1. Create a New GitHub Account

Create a dedicated GitHub account for contributing to these projects. Use a pseudonym and avoid linking it to your real identity.

## 2. Generate a Separate SSH Key

Generate a new SSH key for your anonymous identity:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_anon
```

Add this key to your anonymous GitHub account at [github.com/settings/keys](https://github.com/settings/keys).

## 3. Configure SSH for Multiple Identities

Add the following to your `~/.ssh/config` file to use different keys for different identities:

```
Host github
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519

Host anongit
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_anon
```

## 4. Clone Using Your Anonymous Host Alias

When cloning repositories, use your anonymous alias:

```bash
git clone git@anongit:net2share/dnstm.git
```

Instead of the standard URL:
```bash
git clone git@github.com:net2share/dnstm.git
```

### Already Cloned a Repository?

Update the remote URL to use your anonymous alias:

```bash
git remote set-url origin git@anongit:net2share/dnstm.git
```

## 5. Configure Git Identity Per Repository

In each cloned repository, set your anonymous identity:

```bash
git config user.name "yourpseudonym"
git config user.email "ID+yourpseudonym@users.noreply.github.com"
```

## 6. Configure GitHub Privacy Settings

Go to [github.com/settings/emails](https://github.com/settings/emails) on your anonymous account and enable:

- **"Keep my email addresses private"** — Uses your noreply email in web-based operations
- **"Block command line pushes that expose my email"** — Prevents accidental exposure of other email addresses

Your noreply email address is displayed on this page in the format: `ID+username@users.noreply.github.com`

## Why This Setup Works

By using a separate SSH host alias and per-repository git config:

- You never accidentally commit with your real identity
- Push/pull operations always use the correct SSH key
- Each repository is isolated to use only your anonymous credentials

This setup ensures your contributions remain private and unlinked to your primary GitHub account.
