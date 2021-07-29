# Jekyll Actions

This repo will be built inside of an Ubuntu runner by Github Actions. This utilizes the [Setup-Ruby action](https://github.com/actions/setup-ruby).

The Ruby environment is configured as per the Gemfile so it's inclusion is important.

Jekyll will be configured and used to built the _site dir as per the normal functionality of Jekyll. This is done on Githubs side, so that you no longer need to do it from your local machine before deploying changes. This also means the *entire* site is built from *scratch* before every push to your endpoint.

The [`_config.yml`](https://github.com/surprise-boilerplates/jekyll-actions/blob/main/_config.yml) file contains an exclude directive to avoid submitting content you dont want on your site, like this README.md file.

We utilize ncftpput to push the _site directory to your endpoint. This uses Github Secrets to specify the FTP endpoint, username and password. The action presumes that your web files are in the root of the chrooted dir for your credentials.

## cPanel

I had some issues doing this using cPanels git implementation, so I fell back to Github for greator compatibility for my friends.

cPanel ProFTPd chroots FTP users to their specified directory. I typically create an FTP account per site, and have them chrooted to their web dir. This means that when you FTP into a sites FTP account and issue `ls -la /` it will return the contents of the web dir, not the systems dir.

In the action, you'll see the following code:

```
ncftpput -R -v -u "$ftp_username" -p "$ftp_password" $ftp_location / _site/*
```

The last two blocks of text are important. `/` specifies where to push it to, `_site/*` is what you're pushing there. This means that if you're logging in with the built-in FTP account for your cPanel user, you can change the `/` to `./public_html/`.

In the same vein, you can specify absolute paths:

```
ncftpput -R -v -u "$ftp_username" -p "$ftp_password" $ftp_location /home/user/public_html/ _site/*
```

You should, however, use a relative declaration for the source dir.

## Greetz

Pretty much all of this was worked out by [Mike Conroy](https://mikeconroy.com/2020/04/automating-jekyll-deployments-github-actions/). His site goes into greater detail breaking down the action. The entire thing is actually his work, I'm just archiving it to my new boilerplates company for prosperity.