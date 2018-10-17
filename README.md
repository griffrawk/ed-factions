# Purpose

A project to display faction based statistics for Elite Dangerous
on the command line.

# Usage

## Prerequisite

You must have java installed and available on the command line. Please follow java installation
instructions for this at https://java.com/en/download/help/download_options.xml

## Running stats

To run, use

    # Unix type systems
    ./gradlew stats -q -Pargs="-f 'The Order of Mobius'"

    # Windows
    gradlew stats -q -Pargs="-f 'The Order of Mobius'"

Also supports running a single system stats

    # gradlew stats -q -Pargs="-s 'Exioce'"

To display multiple factions, (or avoid quoting hell where the faction has single quotes in the name), put
the faction name(s) in a file and run it with:

    # gradlew stats -q -Pargs="-i /path/to/your/faction_list"

Similarly for multiple systems, use:

    # gradlew stats -q -Pargs="-j /path/to/your/system_list"


Any of the above will install gradle as required and run the application task to print the statistics out.

The full options are:
```
    -f <faction-name>  # Display all systems for named faction. Use quotes if the name has spaces in it
    -t YYYYMMDD        # override the date to display (default to latest in EDSM history)
    -a                 # show all factions in system
                       #   The default without specifying the flag is to show up to the named faction,
                       #   with a minimum of top 3
    -i <path/to/file>  # take faction names from <file>, one faction per line - avoids single quote issue of name on command line
    -j <path/to/file>  # take system names from <file>, one system per line
    -s <system-name>   # show information only for one system
    -d                 # additional debug output
    -g                 # Show "Gone" systems (retreated factions that are still appearing in history)
    -c                 # Highlight controlling factions
    -h                 # help output
```

Example output
```
    $ ./gradlew stats -Pargs="-caf 'The Order of Mobius'"

    Apathaam [3,506,390]                      OCT-16 OCT-15 OCT-13 OCT-09
    <Movement for Apathaam for...> Boom        30.80  -4.50  +2.20  +3.60
     The Order of Mobius           None        27.00 +11.50 +18.00 +15.20 Boom, Civil unrest
     Crimson State Industry        None        24.60  -4.80 -13.60 -14.80 Boom
     Apathaam Patron's Principles  Boom        11.80  -2.20  -6.60  -0.80 Outbreak
     Brib Purple Travel Partners   Retreat      2.50  -0.10  +1.10  +0.20 Boom
     Apathaam Brothers             None         1.80  +1.80  -0.60  -1.40
     Apathaam Federal Commodities  Outbreak     1.50  +0.10  -0.50  -2.00

    Azrael [4,500,000]                        OCT-16 OCT-15 OCT-13 OCT-09
     The Order of Mobius           None        31.63  +3.13 +11.85  +7.95 Boom, Civil unrest
    <Labour of Azrael>             Boom        25.13  +1.13  -3.45  +2.04 Outbreak
     Azrael Empire Consulate       None        15.82  -1.58  -3.76  -8.36 Boom, Outbreak, Election
     Apathaam Patron's Principles  Boom        15.82  -0.28  +2.63 +12.83 Outbreak, Election
     Azrael Crimson Galactic Ind   Civil war    4.40  -0.90  -3.39  -8.33
     Azrael Patron's Principles    Civil war    4.00  -0.80  -3.99  -8.33 Boom, Lockdown
     Azrael Mafia                  Boom         3.20  -0.70  +0.11  +2.21 Bust

    # ... other systems

    Leading/Trailing information for The Order of Mobius
    Apathaam              [SEP-18] leads by    42.86  -1.04  15.38   3.32
    # ... other systems
```
## Showing systems within a range of another system

You can also now find all the systems within given range of a named system with:
```
    $ ./gradlew within -q -Pargs="-s 'Exioce' -r 10.0"

    system               range    fc pf
    -----------------------------------
    Exioce               0.000     7  1
    NLTT 7789            8.300     7  0
    Azrael               8.426     8  1
```
Optionally add '-m <num>' to set maximum number of factions as an additional filter, e.g. '-m 7' would hide Azrael in the above output.

The column fc is 'Faction count' in the system, 'pf' is the number of player factions in the system.

# Data downloads

## EDDB.IO

The nightly dumps from EDDB.io are used to load all system and faction data into local maps.
See https://eddb.io/api for a list of available downloads and explanations.

These dump files are cached in data/ subdir with the timestamp of the file. Subsequent runs
will check the timestamp against the download file (using a HEAD request), and skip downloading
if the local cached file has the same timestamp, and then the cached versions are used to populate
the application state.

## EDSM

The EDSM API is used to fetch the historical influence values, and pending state information.
Once the systems/factions are known and loaded from EDDB downloads, the named faction's systems
are found and EDSM queried for the stats to be output.

# KNOWN ISSUES

- Historical output leaks into factions that are no longer in the system if they are still in EDSM's history

This only affects the -a output as it will show extra factions that should be ignored.

# TODO
