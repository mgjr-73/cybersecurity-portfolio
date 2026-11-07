# Patch Tuesday, Every Day

October's patch bulletin just landed. Every server is supposed to be at or above each fixed version, and the auditors are on their way.

You have the installed-package list from `records-web01` and the bulletin. One package didn't get the memo. Find it.

The flag format is in the bulletin.

Patch Bulletin:

```
ODAPEEKA STATE IT - MONTHLY PATCH BULLETIN (October)
Applies to: all Ubuntu 22.04 servers, including records-web01

Every server must be at or ABOVE the fixed version for each package below.

PACKAGE           FIXED VERSION
libssl3           3.0.2-0ubuntu1.18
curl              7.81.0-1ubuntu1.18
libxml2           2.9.13+dfsg-1ubuntu0.4
libexpat1         2.4.7-1ubuntu0.5
sudo              1.9.9-1ubuntu2.4
openssh-server    1:8.9p1-3ubuntu0.10

Servers that miss a fix get flagged in the audit. The flag for this challenge is:
C6S{<package_name>_was_never_patched}
...where <package_name> is the one package on records-web01 still below its fixed version,
written exactly as it appears in the package list.

```

records-web01

```
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name                 Version                                   Architecture Description
+++-====================-=========================================-============-=================================
ii  adduser              3.118ubuntu5                              amd64        adduser package
ii  apt                  2.4.12                                    amd64        apt package
ii  base-files           12ubuntu4.6                               amd64        base-files package
ii  bash                 5.1-6ubuntu1.1                            amd64        bash package
ii  bsdutils             1:2.37.2-4ubuntu3.4                       amd64        bsdutils package
ii  ca-certificates      20240203~22.04.1                          amd64        ca-certificates package
ii  coreutils            8.32-4.1ubuntu1.2                         amd64        coreutils package
ii  cron                 3.0pl1-137ubuntu3                         amd64        cron package
ii  curl                 7.81.0-1ubuntu1.18                        amd64        curl package
ii  dash                 0.5.11+git20210903+057cd650a4ed-3build1   amd64        dash package
ii  dbus                 1.12.20-2ubuntu4.1                        amd64        dbus package
ii  dpkg                 1.21.1ubuntu2.3                           amd64        dpkg package
ii  e2fsprogs            1.46.5-2ubuntu1.2                         amd64        e2fsprogs package
ii  findutils            4.8.0-1ubuntu3                            amd64        findutils package
ii  gawk                 1:5.1.0-1ubuntu0.1                        amd64        gawk package
ii  git                  1:2.34.1-1ubuntu1.11                      amd64        git package
ii  gnupg                2.2.27-3ubuntu2.1                         amd64        gnupg package
ii  grep                 3.7-1build1                               amd64        grep package
ii  gzip                 1.10-4ubuntu4.1                           amd64        gzip package
ii  hostname             3.23ubuntu2                               amd64        hostname package
ii  iproute2             5.15.0-1ubuntu2                           amd64        iproute2 package
ii  less                 590-1ubuntu0.22.04.3                      amd64        less package
ii  libc6                2.35-0ubuntu3.8                           amd64        libc6 package
ii  libexpat1            2.4.7-1ubuntu0.2                          amd64        libexpat1 package
ii  libssl3              3.0.2-0ubuntu1.18                         amd64        libssl3 package
ii  libxml2              2.9.13+dfsg-1ubuntu0.4                    amd64        libxml2 package
ii  login                1:4.8.1-2ubuntu2.2                        amd64        login package
ii  logrotate            3.19.0-1ubuntu1.1                         amd64        logrotate package
ii  mawk                 1.3.4.20200120-3                          amd64        mawk package
ii  nano                 6.2-1ubuntu0.1                            amd64        nano package
ii  nginx                1.18.0-6ubuntu14.5                        amd64        nginx package
ii  openssh-server       1:8.9p1-3ubuntu0.10                       amd64        openssh-server package
ii  passwd               1:4.8.1-2ubuntu2.2                        amd64        passwd package
ii  perl                 5.34.0-3ubuntu1.3                         amd64        perl package
ii  procps               2:3.3.17-6ubuntu2.1                       amd64        procps package
ii  python3              3.10.6-1~22.04.1                          amd64        python3 package
ii  rsyslog              8.2112.0-2ubuntu2.2                       amd64        rsyslog package
ii  sed                  4.8-1ubuntu2                              amd64        sed package
ii  sudo                 1.9.9-1ubuntu2.4                          amd64        sudo package
ii  systemd              249.11-0ubuntu3.12                        amd64        systemd package
ii  tar                  1.34+dfsg-1ubuntu0.1.22.04.2              amd64        tar package
ii  tzdata               2024a-0ubuntu0.22.04.1                    amd64        tzdata package
ii  unzip                6.0-26ubuntu3.2                           amd64        unzip package
ii  util-linux           2.37.2-4ubuntu3.4                         amd64        util-linux package
ii  vim                  2:8.2.3995-1ubuntu2.17                    amd64        vim package
ii  wget                 1.21.2-2ubuntu1.1                         amd64        wget package
ii  zlib1g               1:1.2.11.dfsg-2ubuntu9.2                  amd64        zlib1g package

```

<details>
  <summary><strong>Solution</strong></summary>
  
  Coming Soon!
  
</details>

&nbsp;
