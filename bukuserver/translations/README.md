## Bukuserver translations

This directory contains translations activated by installing Flask-Babel(Ex) and providing `BUKUSERVER_LOCALE` environment variable.
(If a locale is missing or incomplete, the original "keys" are used as translations; also `_gettext` converts strings using Flask-Admin translation files.)

The `__init__.py` file includes wrapper scripts for Babel CLI interface; a simple CLI is provided in `__main__.py`.

Translation files can be found as `<locale>/LC_MESSAGES/messages.po` (their corresponding _compiled_ versions have a `.mo` extension);
their layout is generated by collecting translation keys from Python/Jinja sources, and appending the `messages_custom.pot` contents
(for keys that can't be detected automatically).

### Script

The `__main__.py` file contains a script that does the following:
1. collect translation keys from Python and Jinja invocations of `gettext()`/`lazy_gettext()`/`pgettext()` +
   shorthands `_()`/`_l()`/`_p()`/`_lp()`, and generate a "template" translation file `messages.pot`
2. patch said "template" with initial values (most importantly a hardcoded creation date, which prevents "changes" from appearing in unchanged translations)
   and append contents of `messages_custom.pot` to it
3. update existing translation files (`*/LC_MESSAGES/messages.po`) to match the new "template" (note that if you've removed/renamed some translation keys,
   their current values will be commented out and moved to the bottom; so you'll need to edit _all_ translation files after that)
4. if any locale names are supplied, corresponding locale files are created (or updated if they exist already)
5. compile all translation files into matching `.mo` binaries

Steps 1-2 are implemented as `translations_generate()`, 3-4 as `translations_update()`, and 5 as `translations_compile()` (in the `__init__.py` file).

### Usage

```sh
python .
```
Will run the `__main__.py` script (if running from a different folder, pass relative path to it instead of `.`)
```sh
python -m bukuserver.translations
```
Has the same effect _when run **in a virtualenv**_ in which `buku` was _installed as an **`--editable`** package_.

Run the script in following events:
* after you've added/removed/renamed some translation keys in the source (note that in the latter two cases you'll need to _edit all translation files_,
  to either remove or restore commented out translations of keys no longer found in `messages.pot`; _alternatively_ you can edit these keys before running the script)
* after you've edited some translation file(s), to compile them
* when you want to add one or more new locales (provide their codes as additional parameters to the script)

Additionally, providing `-h` or `--help` will cause brief usage info to be printed out instead.