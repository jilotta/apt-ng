# apt-ng
APT-NG -- the wrapper over APT written in Bash.

**What can it do?**  
APT-NG supports short Zypper-like keys (e.g. `apt-ng in package` instead `apt-ng install package`). You can install, remove packages, search the package in repos and more. If the key specified is not recognised as an APT-NG key, APT is being run with that key.

**What does APT-NG require?**  
APT-NG uses `bash`, `apt`, `apt-get`, `dpkg`. If you have an up-to-date Debian-based system you must have them.

**How to use it?**  
Key              | Short key | Sense
----------------:|-----------|:---------------------
install          | is/in     | Installs package(s) from repo
install-deb      | id        | Installs from DEB package
remove           | rm        | Removes package(s)
clean            | cl        | Removes 'orphans' and old DEB packages from cache
search           | sr        | Searches in repo
search-installed | si        | Searches in installed packages
show/info        | sh/if     | Shows info about package
update           | up        | Updates cache
upgrade          | ug        | Upgrades system
version          | vr        | Shows version of Apt-ng (/usr/bin/apt-ng)
help             | he        | Shows help
<details>
    <summary><b>Source code</b></summary>
  
```bash
#!/bin/bash
# Apt-ng v0.1 -- the wrapper over APT
# License: GNU GPL v3

KEY=`echo "$1" | tr '[:upper:]' '[:lower:]'`
VERSION=0.1
ARGS="${@:2}"
help(){
    echo "Apt-ng ($0) v$VERSION"
     echo "===================================================="
     cat <<EOF
Key              | Short key | Sense
----------------:|-----------|:---------------------
install          | is/in     | Installs package(s) from repo
install-deb      | id        | Installs from DEB package
remove           | rm        | Removes package(s)
clean            | cl        | Removes 'orphans' and old DEB packages from cache
search           | sr        | Searches in repo
search-installed | si        | Searches in installed packages
show/info        | sh/if     | Shows info about package
update           | up        | Updates cache
upgrade          | ug        | Upgrades system
version          | vr        | Shows version of Apt-ng (/usr/bin/apt-ng)
help             | he        | Shows help
EOF
}

case $KEY in
    'install' | 'is' | 'in' ) sudo apt-get install $ARGS;;
    'install-deb' | 'id' ) sudo dpkg -i $ARGS;;
    'remove' | 'rm' ) sudo apt-get remove $ARGS 2>/dev/null;;
    'clean'  | 'cl' ) sudo apt-get autoremove; sudo apt-get clean;;
    'search' | 'se' ) apt-cache search $ARGS;;
    'search-installed' | 'si' ) apt-cache search $ARGS 2>/dev/null | grep '\[installed\]';;
    'show' | 'info' | 'sh' | 'if' ) apt-cache show $ARGS;;
    'update' | 'up' ) sudo apt-get update;;
    'upgrade' | 'ug' )
        if [[ $ARGS ]]; then
            sudo apt-get update && sudo apt-get upgrade &&\
            sudo apt-get install $ARGS
        else
            sudo apt-get update && sudo apt-get upgrade
        fi
    ;;
    'dpkgcmd' | 'dc' ) sudo dpkg $ARGS;;
    'aptcmd' | 'ac' ) sudo apt $ARGS;;
    'version' | 've' ) echo $VERSION;;
    'help' | 'he' ) help;;
    '' )  echo "Error: no keys found"; help;;
    * )  
        echo -e "Error: $0 $1 ($0 $KEY): not found, trying to execute apt with that key...\n===========================\n"
        sudo apt $@ && echo -e '\n===========================\n\nSuccess!' ||\
        echo -e "\n===========================\n\nError from apt received."
        help;;
esac
```
</details>
