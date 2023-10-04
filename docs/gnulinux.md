# GNU/Linux

>This tutorial is for **GNU/Linux Only**. [The Windows version is here.](./windows.md)

## Step 1  — Inspecting tools
First, we need to make sure that we have `git`, `gnupg` and `github-cli` installed.

```shell
[alialmasi@parch ~]$ git --version
git version 2.42.0

[alialmasi@parch ~]$ gpg --version
gpg (GnuPG) 2.2.41
libgcrypt 1.10.2-unknown

[alialmasi@parch ~]$ gh version
gh version 2.35.0 (2023-09-19)
https://github.com/cli/cli/releases/tag/v2.35.0
```

In most GNU/Linux distros, these are pre-installed (except for `github-cli`.) If you didn't have these installed, you can use your distro's package manager to install them.

[You can install `github-cli` with any GNU/Linux distro you have.](https://github.com/cli/cli/blob/trunk/docs/install_linux.md)

> Note: we'll use `gnupg` to generate a GPG key pair. we'll also use `github-cli` to authenticate ourselves to GitHub (logging into our account with our cli).

### Step 1.1 — Initialize your `git`
If you're new to Git, you probably have to initialize it first. (It's just a little "Name/Email" configuration, don't worry.) Open a terminal and do these:

1. Set your name:

```shell
git config --global user.name "YOUR NAME"
```

> Tip: You can confirm that you have set the username correctly by using `git config --global user.name`

2. Set your email:

```shell
git config --global user.email "YOUR@EMAIL.COM"
```

> Tip: You can confirm that you have set the email correctly by using `git config --global user.email`

### Step 1.2 — Authenticate to GitHub in cli

To push a local repository to GitHub, we need access. The easiest way to gain access is to authenticate using `github-cli`.  
> *GitHub CLI is an open-source tool for using GitHub from your computer's command line. When you're working from the command line, you can use the GitHub CLI to save time and avoid switching contexts.*

1. Authenticate with GitHub by running this command from your terminal:

```shell
gh auth login
```

2. Select `GitHub.com` and Follow the on-screen prompts.

> *GitHub CLI automatically stores your Git credentials for you when you **choose HTTPS as your preferred protocol** for Git operations and **answer "yes" to the prompt asking if you would like to authenticate to Git with your GitHub credentials**. This can be useful as it allows you to use `git push`, `git pull`, and so on, without needing to set up a separate credential manager or use SSH.*

## Step 2 — Generate a GPG key
Now that you've initialized your Git, you need to generate your own GPG key pair and add it to your GitHub account. This is where `gnupg` will help us. Open a terminal and do these:

1. Generate a GPG key pair:

```shell
gpg --full-generate-key
```

2. At the prompt, specify the kind of key you want, or press `Enter` to accept the default. (`RSA and RSA` is recommended.)

3. At the prompt, specify the key size you want, or press `Enter` to accept the default.

4. Enter the length of time the key should be valid. Press `Enter` to specify the default selection, indicating that the key doesn't expire. Unless you require an expiration date. (Default is recommended.)

5. Verify that your selections are correct.

6. Enter your information.

> Note: When asked to enter your email address, ensure that you enter the verified email address for your GitHub account.

7. Type a secure passphrase.

### Step 2.1 — Copy your public key
Congratulations, we've generated your GPG key pair. Now, you need to copy your public key to add it to your GitHub account.

1. Use the `gpg --list-secret-keys --keyid-format=long` command to list the long form of the GPG keys for which you have both a public and private key.

> Note: Some GPG installations on Linux may require you to use `gpg2 --list-keys --keyid-format LONG` to view a list of your existing keys instead. In this case, you will also need to configure Git to use `gpg2` by running `git config --global gpg.program gpg2`.

2. From the list of GPG keys, copy the long form of the GPG key ID you'd like to use. In this example, the GPG key ID is **3AA5C34371567BD2**:

```shell
$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2037-03-10 [expires: 2037-05-10]
uid                          YOUR NAME <YOUR@EMAIL.COM>
ssb   4096R/4BB6D45482678BE3 2037-03-10
```

3. Paste the text below, substituting the GPG key ID you'd like to use. In this example, the GPG key ID is **3AA5C34371567BD2**:

```shell
gpg --armor --export 3AA5C34371567BD2
```

> Prints the GPG key ID, in ASCII armor format.

4. Copy your GPG key, beginning with `-----BEGIN PGP PUBLIC KEY BLOCK-----` and ending with `-----END PGP PUBLIC KEY BLOCK-----`.

Now that you have copied your public key, head over to *Step 3* to add your public key to your GitHub account.

## Step 3 — Add your public key to your GitHub account

> *To sign commits associated with your account on GitHub, you can add a public GPG key to your account.  
You can add multiple public keys to your account on GitHub. Commits signed by any of the corresponding private keys will show as verified. If you remove a public key, any commits signed by the corresponding private key will no longer show as verified.  
To verify as many of your commits as possible, you can add expired and revoked keys. If the key meets all other verification requirements, commits that were previously signed by any of the corresponding private keys will show as verified and indicate that their signing key is expired or revoked.*

To add your public key to your GitHub account, open a terminal and do these:

1. In the upper-right corner of any page, click your profile photo, then click **Settings**.

2. In the "Access" section of the sidebar, click **SSH and GPG keys**.

3. Next to the "GPG keys" header, click **New GPG key**.

4. In the "Title" field, type a name for your GPG key.

5. In the "Key" field, paste the GPG key you copied when you generated your GPG key in *Step 2.1*.

6. Click **Add GPG key**.

7. To confirm the action, authenticate to your GitHub account.

And you're done adding your public key to your GitHub account. Now you have to tell `git` about your GPG key.

## Step 4 — Tell `git` about your GPG key.

> *If you're using a GPG key that matches your committer identity and your verified email address associated with your account on GitHub.com, then you can begin signing commits and signing tags.*

Open a terminal and follow along:

1. Use the `gpg --list-secret-keys --keyid-format=long` command to list the long form of the GPG keys for which you have both a public and private key. **Private key is required for signing commits**.

> Note: Some GPG installations on Linux may require you to use `gpg2 --list-keys --keyid-format LONG` to view a list of your existing keys instead. In this case, you will also need to configure Git to use `gpg2` by running `git config --global gpg.program gpg2`.

2. From the list of GPG keys, copy the long form of the GPG key ID you'd like to use. In this example, the GPG key ID is **3AA5C34371567BD2**:

```shell
$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot <hubot@example.com>
ssb   4096R/4BB6D45482678BE3 2016-03-10
```

3. To set your primary GPG signing key in Git, paste the text below, substituting the GPG primary key ID you'd like to use. In this example, the GPG key ID is **3AA5C34371567BD2**:

```shell
git config --global user.signingkey 3AA5C34371567BD2
```

4. To configure Git to sign all commits by default, enter the following command:

```shell
git config --global commit.gpgsign true
```

5. To add your GPG key to your .bashrc startup file, run the following command:

```shell
[ -f ~/.bashrc ] && echo -e '\nexport GPG_TTY=$(tty)' >> ~/.bashrc
```

Now you've completed setting up your GPG key pair to sign your Git commits on GitHub.

Try and make some commits, push them to GitHub and Check them out. There must be a little "Verified" tag on the commits you've made. 😃

## Read in other places
[My blog](https://note.al1almasi.ir/how-to-verify-git-commits)  
[dev.to](https://dev.to/alialmasi/how-to-verify-git-commits-easy-guide-for-beginners-432o)  

## Author
**[Ali Almasi](https://al1almasi.ir)**  
[GitHub profile](https://github.com/alialmasi)  
[Telegram channel](https://t.me/al_dot_dev)
