# Formatting
There is no hard and fast rule for formatting, meaning source won't be rejected because it doesn't follow a standard.  And as far as what standard we apply, it's based on the default from Netbeans with some tweaking.  Can it be improved?  Sure.  Is it subject to change?  Yup.

## Setup
Import the formatting rules for netbeans from lightblue source.

1. Start Netbeans
2. Tools -> Options
3. Select "Import..." (bottom left)
4. Select "Browse..."
5. Select zip in lightblue source: docs/netbeans-formatting-java.zip
6. Check box next to "All"
7. Select "Ok"

## Application
Make sure you have no uncommitted or stagged changes before starting this.

1. Netbeans
    1. Open the "lightblue" project.
        * Shows up as "lightblue: com.redhat.lightblue|pom"
    2. Expand "Modules"
    3. Select all modules.
    4. SHIFT + ALT + F (shortcut for format)
    5. Select "Ok"
    6. Wait until done...
2. Terminal
    1. cd to the root directory of lightblue source (where changes were applied)
    2. Reset changes to non-java source:
```
git checkout `git status | grep -v src/.*/java|awk -F: '{print $2}'`
```
    3. Build to verify nothing broke
```
mvn clean install
```
    4. Commit and push
