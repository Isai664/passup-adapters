Passup-adapters
===============

Update all of your passwords at once
------------------------------------

**This repository contains the community-provided adapters used by Passup.js to update each website. You can find the Passup.js core, docs, and installation instructions [here](https://github.com/alanctkc/passup.js).**

Security experts recommend updating account passwords frequently to mitigate the damage inflicted by a compromised password. However, most of us maintain multiple web accounts and have hardly updated a handful of passwords before it's time to rotate again.

**Passup.js** is a password updater built on CasperJS and PhantomJS that takes some of the pain out of updating web passwords.

Passup includes simple adapters for major websites and uses a headless web browser to automate the process of changing your passwords. Passup allows you to configure a list of sites you use for each password and update each group of sites by typing the password only once. So, for example, if you use the same password for all your social media sites and throwaway accounts, you can bulk update them with a single command.

### Contributors Needed

**Don't be shy!** The more developers that contribute Passup.js adapters and use them for their own accounts, the more valuable the repository becomes for everyone. Please send pull requests for any adapters you create for the sites you use, regardless of their size or popularity. Additionally, if you find a way to improve an existing adapter, please feel free to submit your contribution.

Creating Adapters
-----------------

Adapaters are written in CasperJS and run on the headless PhantomJS browser.

The easiest way to contribute an adapter is simply to copy an existing adapter and make modifications as necessary.

Here's an example of a simple `github.js` adapter:

    exports.adapter = {
        "name": "GitHub",
        "passwordRegExp": /\w/,
        "update": function(data) {
            casper.start('https://github.com/login');

            casper.then(function() {
                this.fill('form[action="/session"]', {
                    'login': data.site.login,
                    'password': data.oldPassword
                }, true);
            });

            casper.then(function() {
                this.open('https://github.com/settings/admin');
            });

            casper.then(function() {
                this.fill('form#change_password', {
                    'user[old_password]': data.oldPassword,
                    'user[password]': data.newPassword,
                    'user[password_confirmation]': data.newPassword
                }, true);
            });
        }
    };

The adapter `name` is a human-readable name for the adapter that will be printed to the terminal when it is updated. `passwordRegExp` is a JavaScript regular expression that specifies the password requirements for the given site.

The update function sets up the CasperJS steps to update the password on that site. View the full CasperJS API documentation [here](http://casperjs.org/api.html). The function should take a parameter called `data`, which provides two special properties, `data.oldPassword` and `data.newPassword`, in addition to a `data.site` object that contains all the site's properties from the user's `~/.passup.json`.

The filename of your adapter, minus the `.js` extension (CommonJS specification), will be the identifier used to include the adapter in a user's `~/.passup.json` configuration. Be sure to use sensible names, usually just the main string of the website's domain. In the `github.js` example above, the adapter identifier used in configuration files is `github`.

After creating your adapter, add an example configuration to a password group in `config.example.js`. For the example above, it looks like this:

    {
        "adapter": "github",
        "login": "username"
    }

If you need additional data in your update function, be sure to include it in the example configuration. It might look something like this:

    {
        "adapter": "bankOfAmerica",
        "login": "username",
        "state": "MO"
    }

When you're relatively sure your adapter works reliably, send a pull request and it will be added to the repository to be shared with other users.
