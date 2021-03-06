# GovWifi Secrets

All secrets are stored in [govwifi-build][govwifi-build], and are encrypted using GPG.

All shell commands assume you are running from within the [`govwifi-terraform`][govwifi-terraform]
repository since the [govwifi-build][govwifi-build] repository is cloned in the
`.private` directory of [`govwifi-terraform`][govwifi-terraform].

## Tools

To use these secrets, you will need the [Password Store][passwordstore] tool installed on your machine.

You will also need an implementation of `gpg` installed. This will be installable under the name `gnupg`:

```
apt/brew/dnf/rpm/yum install gnupg
```

## Getting access

Once you have installed the tools, you will need someone to re-encrypt the secrets with your public key.

Give someone your full key ID:

```sh
gpg --list-keys '<name>@digital.cabinet-office.gov.uk'
```

An example of a key ID is: `06D20CF70AC370DE72F49EDC992939FDD5C5144C`

Please also ensure your public key is on a well known keyserver:

We suggest `hkps.pool.sks-keyservers.net` and `keyserver.ubuntu.com`, as they are known to be reliable.

```sh
gpg --keyserver keyserver.ubuntu.com --send-keys '<your key ID>'
gpg --keyserver hkps.pool.sks-keyservers.net --send-keys '<your key ID>'
```

## Giving Access

Once you have received someone's GPG Key ID, you must receive it, and trust it:

```sh
key_id='<their key ID>'
gpg --keyserver keyserver.ubuntu.com --receive-keys "$key_id"
echo "${key_id}:6" | gpg --import-ownertrust
```

Append the `.private/passwords/.gpg-id` file with their key:

```sh
echo "$key_id" >> '.private/passwords/.gpg-id'
```

then re-encrypt the passwords from within the [`govwifi-terraform`][govwifi-terraform] repo:

```sh
make rencrypt-passwords
```

## Getting a secret

Throughout the documentation, there will be references to specific secrets stored within the password store.

To read individual secrets, run the command:

```sh
PASSWORD_STORE_DIR=<password_store_dir> pass show <secret_name>
```

where:

1. `<password_store_dir>` is the path of the `passwords` directory of the
[govwifi-build](https://github.com/alphagov/govwifi-build) repository on your
local machine.

2. `<secret_name>` is the path of the secret that you want to display. You can omit
   this to get a list of all secret paths.

For example, if you want to access the Staging Bastion SSH Key:

```sh
PASSWORD_STORE_DIR=.private/passwords pass show keys/govwifi-staging-bastion-key
```


[passwordstore]: https://www.passwordstore.org/
[govwifi-terraform]: https://github.com/alphagov/govwifi-terraform
[govwifi-build]: https://github.com/alphagov/govwifi-build
