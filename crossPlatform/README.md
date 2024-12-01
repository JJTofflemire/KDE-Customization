# cross platform

## tree

aerc

- `~/.config/aerc/`

fastfetch

- `~/.config/fastfetch/`

kitty

- `~/.config/kitty/kitty.conf`

nvim

- `~/.config/nvim/`

spicetify

- `~/.config/spicetify/`

spotify-player

- `~/.config/spotify-player/`

starship

- `~/.config/starship.toml`

tmux

- `~/.tmux.conf`

yazi

- `~/.config/yazi/`

zellij

- `~/.config/zellij/`

## aerc

### college account oauth generation

- I generate the oauth token for my college email account using this gitignored script:

```
export AERC_CLIENT_ID={client id}
export AERC_CLIENT_SECRET={client secret}

python ~/.config/aerc/scripts/generateRefreshToken.py
```

- `generateRefreshToken.py` either generates or refreshes the oauth token `aerc` needs by using [this](https://jtof.dev/oauth/) redirect page for generating new token files

- before this website can generate refresh tokens, it needs to be explicitly set up in google cloud

  - in google cloud: create a new project and create an oauth client id in [apis & services](https://console.cloud.google.com/apis/credentials?pli=1), (choose web client) and add whichever website is used in the script as an authorized redirect uri

    - here you can grab the client id and secret to use in your template email file and in your `run.sh`

  - lastly, go to the oauth consent screen page and set it up as external, then click the `in production` button. there will be a popup about the app needing verification, but clicking okay lets you use the app without submitting a verification

- finally, the `generateAccountsConf.py` script uses a template college email file that gets filled out with saved information:

```
[{email}]
source = imaps+oauthbearer://{email address}%40{domain}:{refresh token}@imap.gmail.com:993?\
client_id={id}&\
client_secret={secret}&\
token_endpoint=https%3A%2F%2Faccounts.google.com%2Fo%2Foauth2%2Ftoken
outgoing = smtps+oauthbearer://{email address}%40{domain}:{refresh token}@smtp.gmail.com:465?\
client_id={id}&\
client_secret={secret}&\
token_endpoint=https%3A%2F%2Faccounts.google.com%2Fo%2Foauth2%2Ftoken
from = {name} <{email}>
cache-headers = true
```

- fill out the `{email}` fields, and the `{name}` field, and the script will fill in everything else

### protonmail

- I followed the [official guide](https://man.sr.ht/~rjarry/aerc/providers/protonmail.md) on how to get protonmail working

- the only thing that doesn't work out of the box is the TLS certificate; fix that problem by running the following:

- in Proton Mail Bridge, go to Settings > Advanced Settings > Export TLS Certificates

- then run `sudo trust anchor --store ~/.config/protonmail/bridge-v3/cert.pem`

### accounts folder

- the `accounts` folder holds text files of each email config file, with one email address config per file

- the college account config gets written to a text file in `accounts/` before getting assembled into the full `accounts.conf` in `aerc/`

## spicetify

- spicetify is very nice to have, but an absolute nightmare to install for the first time. for my own ease of use, I am including a copy of the `.config/spicetify` directory

- to set everything up, run some combination of the following:

```
chmod a+wr /var/lib/flatpak/app/com.spotify.Client/x86_64/stable/active/files/extra/share/spotify
chmod a+wr /var/lib/flatpak/app/com.spotify.Client/x86_64/stable/active/files/extra/share/spotify/Apps -R

spicetify backup apply
spicetify config inject_css 1
spicetify config replace_colors 1
spicetify config current_theme marketplace

spicetify config custom_apps marketplace
spicetify apply

spicetify config custom_apps betterLibrary
spicetify config custom_apps history-in-sidebar
spicetify config custom_apps stats
spicetify apply
```

- which should, theoretically, work. though, I'm not sure if this will sync over snippets, since I am `gitignore`ing the `Backup` and `Extracted` folders

### spicetify update script

- used to keep all the custom apps up to date

- place the `customapps update.sh` script in `~/.config/spicetify/CustomApps`, and run with `source customapps\ update.sh` from inside the `CustomApps` folder

## spotify-player

- I manually compile / update `spotify-player` from their git repo because I have had trouble with their aur packages in the past

```
cargo build --release --no-default-features --features streaming,pulseaudio-backend,lyric-finder,image,fzf,media-control,image
```

- and I add the compiled binary to my path:

```
export PATH="$PATH:/home/andya/gitRepos/spotify-player/target/release"
```
