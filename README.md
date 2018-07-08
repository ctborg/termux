# Installing Google-Cloud-SDK on a Chromebook
A short set of instructions to allow you to install the google-cloud-sdk on a chromebook. 

## Install Termux
First install termux from the Google Play Store (https://termux.com/)

The do one of the following:
## install google-cloud-sdk

    scratch=$(mktemp -d -t tmp.XXXXXXXXXX) 

    cd $scratch

    curl -k https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-207.0.0-linux-x86_64.tar.gz -O

    tar -xzf google-cloud-sdk-134.0.0-linux-x86_64.tar.gz 

    cd google-cloud-sdk

    # get list of files which need shebang changed
    find $(pwd) -type f -exec awk '
      /^#!.*/{print FILENAME}
      {nextfile}' {} + > ../lines.txt

    # create termux-fix-shebang (if not on android)
    vi ~/bin/termux-fix-shebang
    "
    ###
    ###!/data/data/com.termux/files/usr/bin/sh
    ###PREFIX=/data/data/com.termux/files/usr

    if [ $# = 0 -o "$1" = "-h" ]; then
            echo 'usage: termux-fix-shebang <files>'
            echo 'Rewrite shebangs in specified files for running under Termux,'
            echo 'which is done by rewriting #!*/bin/binary to #!$PREFIX/bin/binary.'
            exit 1
    fi

    for file in $@; do
            # Do realpath to avoid breaking symlinks (modify original file):
            sed -i -E "1 s@^#\!(.*)/bin/(.*)@#\!/data/data/com.termux/files/usr/bin/\2@" $@
    done
    "

    chmod 755 ~/bin/termux-fix-shebang

    # apply fix
    xargs -0 -n 1 termux-fix-shebang < <(tr \\n \\0 < ../lines.txt)


    tar -czf google-cloud-sdk-207.0.0-linux-x86_64__TERMUX.tar.gz google-cloud-sdk/


### OR - download and install 
    export CLOUDSDK_PYTHON=/data/data/com.termux/files/usr/bin/python2.7
    curl -k https://storage.googleapis.com/termux-gcloud-sdk/google-cloud-sdk-207.0.0-linux-x86_64__TERMUX.tar.gz -O
    tar -xzf google-cloud-sdk-207.0.0-linux-x86_64__TERMUX.tar.gz
    cd google-cloud-sdk
    bash ./install.sh
    
    echo 'export CLOUDSDK_PYTHON="python2.7"' >> $HOME/.bashrc
    echo 'export PATH="/data/data/com.termux/files/home/google-cloud-sdk/bin:$PATH"' >> $HOME/.bashrc

Depending on your version of ChromeOS, you may not be able to edit permissions on your /bin folder.  Simply run the commands usings `bash`.  e.g. `bash gcloud app deploy`
