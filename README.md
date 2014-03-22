rddblockparser
===========

    Credits:
    --------

        Modified by the Official Reddcoin Project
        Originally written by znort987@yahoo.com
        
    What:
    -----

        A fairly fast, quick and dirty bitcoin and reddcoin whole blockchain parser.

    Why:
    ----

        . Few dependencies: openssl-dev, boost

        . Very quickly extract information from the entire blockchain.

        . Code is simple and helps to understand how the data structure underlying bitcoin works.

    Build it:
    ---------

        . Turn your x86-64 Ubuntu box on

        . Make sure you have an up to date satoshi client blockchain in ~/.bitcoin or ~/.reddcoin

        . Run this:

            sudo apt-get install libssl-dev build-essential g++-4.4 libboost-all-dev libsparsehash-dev git-core perl
            git clone git://github.com/reddcoin-project/rddblockparser.git
            cd rddblockparser
            make

    Try it:
    -------

        . Compute simple blockchain stats, full chain parse (< 1 second)

            ./parser simpleStats

        . Extract all transactions for popular address RctBPR4vuQmgDuqABUnrqCWjEDwaqF2FWd (20 seconds)

            ./parser transactions RctBPR4vuQmgDuqABUnrqCWjEDwaqF2FWd

        . Compute the closure of an address, that is the list of addresses that provably belong to the same person (20 seconds):

            ./parser closure RctBPR4vuQmgDuqABUnrqCWjEDwaqF2FWd

        . Compute and print the balance for all keys ever used in a TX since the beginning of time in a styled txt-format (30 seconds):

            ./parser allBalances >allBalances.txt
            
        . Compute and print the balance for all keys ever used in a TX since the beginning of time in the JSON-format (30 seconds):

            ./parser richlist2JSON >balances.json

        . See all the block rewards and fees:

            ./parser rewards >rewards.txt

    Caveats:
    --------

        . You need an x86-84 ubuntu box and a recent version of GCC(>=4.4), recent versions of boost
          and openssl-dev. The whole thing is very unlikely to work or even compile on anything else.

        . It needs quite a bit of RAM to work. Never exactly measured how much, but the hash maps will
          grow quite fat. I might switch them to something different that spills over to disk at some
          point. For now: it works fine with 8 Gigs.

        . The code isn't particularly clean or well architected. It was just a quick way for me to learn
          about bitcoin/reddcoin. There isnt much in the way of comments either.

        . OTOH, it is fairly simple, short, and efficient. If you want to understand how the blockchain
          data structure works, the code in parser.cpp is a solid way to start.

    Hacking the code:
    -----------------

        . parser.cpp contains a generic parser that mmaps the blockchain, parses it and calls
          "user-defined" callbacks as it hits interesting bits of information.

        . util.cpp contains a grab-bag of useful bitcoin/reddcoin related routines. Interesting examples include:

            showScript
            getBaseReward
            solveOutputScript
            decompressPublicKey

        . cb/allBalances.cpp    :   code to get all the balances of all addresses ever used in a TX.
        . cb/closure.cpp        :   code to compute the transitive closure of an address
        . cb/dumpTX.cpp         :   code to display a transaction in very great detail
        . cb/help.cpp           :   code to dump detailed help for all other commands
        . cb/pristine.cpp       :   code to show all "pristine" (i.e. unspent) blocks
        . cb/rewards.cpp        :   code to show all block rewards (including fees)
        . cb/simpleStats.cpp    :   code to compute simple stats.
        . cb/sql.cpp            :   code to product an SQL dump of the blockchain
        . cb/taint.cpp          :   code to compute the taint from a given TX to all TXs.
        . cb/transactions.cpp   :   code to extract all transactions pertaining to an address.


        . You can very easily add your own custom command. You can use the existing callbacks in
          directory ./cb/ as a template to build your own:

                cp cb/allBalances.cpp cb/myExtractor.cpp
                Add to Makefile
                Hack away
                Recompile
                Run

        . You can also read the file callback.h (the base class from which you derive to implement your
          own new commands). It has been heavily commented and should provide a good basis to pick what
          to overload to achieve your goal.

        . The code makes heavy use of the google dense hash maps. You can switch it to use sparse hash
          maps (see util.h, search for: DENSE, undef it). Sparse hash maps are slower but save quite a
          bit of RAM.
          
    Installing the Reddcoin Linux Daemon:
    --------
        To have a up-to-date copy of the blockchain on your system, you need to download, compile and run the reddcoin Linux daemon. Here's how you do it:
    
        IN YOUR LINUX (ubuntu) SHELL:
    
        sudo apt-get install git screen make automake build-essential libboost-all-dev yasm binutils libcurl4-openssl-dev openssl libssl-dev libdb++-dev
    
        cd ~
        git clone https://github.com/reddcoin/reddcoin
        cd ~/reddcoin/src
        make -f makefile.unix USE_UPNP=-
        cp reddcoind ..
        cd ~
        ./reddcoin/reddcoind  
    
        cat << "CONFIG" >> ~/.reddcoin/reddcoin.conf
        server=1
        rpcuser=yourusername
        rpcpassword=yourpassword
        daemon=1
        gen=0
        maxconnection=100
        addnode=198.199.75.10
        addnode=107.170.253.235
        addnode=162.243.209.188
        addnode=188.226.135.181
        addnode=188.226.135.184
        addnode=108.174.49.42
        rpcport=45443
        CONFIG
    
        ~/reddcoin/reddcoind

    License:
    --------

        Code is in the public domain.

