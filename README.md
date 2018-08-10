<a href="https://www.dyne.org/software/tomb">
	<img src="https://raw.githubusercontent.com/dyne/Tomb/master/extras/images/tomb_and_bats.png"
       align="right" height="200"/>
</a>

Tomb
================================================================

[![][travis-badge]][travis-link]
[![][codacy-badge]][codacy-link]
[![][release-badge]][release-link]
[![][irc-badge]][irc-link]

> Lead Maintainer: [jaromil][jaromil-link]

**A minimalistic commandline tool to manage encrypted volumes aka The Crypto Undertaker**

<a href="https://www.dyne.org">
  <img src="https://zenroom.dyne.org/img/software_by_dyne.png"
       alt="Software by Dyne" align="right" height="75" />
</a>

* **[Official Website][tomb]**
* **[Signed release for production use][release]**
* **[Installation instructions][install]**



# ![MM] What is Tomb, the crypto undertaker?

Tomb aims to be a free and open source system for easy encryption and
backup of personal files, written in code that is easy to review and
links well reliable GNU/Linux components.

Tomb's ambition is to provide military-grade security by way of:

- a minimalist design consisting in small and well readable code
- facilitation of good practices, i.e: key/storage physical separation
- adoption of a few standard and well tested implementations.

At present, Tomb consists of a simple shell script (Zsh) using
standard filesystem tools (GNU) and the cryptographic API of the Linux
kernel (cryptsetup and LUKS). Tomb can also produce machine parsable
output to facilitate its use inside graphical applications.

# ![MM] How does it work?

To create a Tomb, do:
```
 $ tomb dig -s 100 secret.tomb
 $ tomb forge secret.tomb.key
 $ tomb lock secret.tomb -k secret.tomb.key
```
To open it, do
```
 $ tomb open secret.tomb -k secret.tomb.key
```
and after you are done
```
 $ tomb close
```
or if you are in a hurry
```
 $ tomb slam all
```

```
  Syntax: tomb [options] command [arguments]

  Commands:

   // Creation:
   dig     create a new empty TOMB file of size -s in MiB
   forge   create a new KEY file and set its password
   lock    installs a lock on a TOMB to use it with KEY

   // Operations on tombs:
   open    open an existing TOMB (-k KEY file or - for stdin)
   index   update the search indexes of tombs
   search  looks for filenames matching text patterns
   list    list of open TOMBs and information on them
   ps      list of running processes inside open TOMBs
   close   close a specific TOMB (or 'all')
   slam    slam a TOMB killing all programs using it
   resize  resize a TOMB to a new size -s (can only grow)

   // Operations on keys:
   passwd  change the password of a KEY (needs old pass)
   setkey  change the KEY locking a TOMB (needs old key and pass)

   // Backup on paper:
   engrave makes a QR code of a KEY to be saved on paper

   // Steganography:
   bury    hide a KEY inside a JPEG image (for use with -k)
   exhume  extract a KEY from a JPEG image (prints to stdout)

  Options:

   -s     size of the tomb file when creating/resizing one (in MiB)
   -k     path to the key to be used ('-k -' to read from stdin)
   -n     don't process the hooks found in tomb
   -o     options passed to commands: open, lock, forge (see man)
   -f     force operation (i.e. open even if swap is active)
   -g     use a GnuPG key to encrypt a tomb key
   -r     provide GnuPG recipients (separated by comma)
   -R     provide GnuPG hidden recipients (separated by comma)
   --kdf  forge keys armored against dictionary attacks

   -h     print this help
   -v     print version, license and list of available ciphers
   -q     run quietly without printing information
   -D     print debugging information at runtime
```

# ![MM] What is this for, exactly?

This tool can be used to dig .tomb files (LUKS volumes), forge keys
protected by a password (GnuPG encryption) and use the keys to lock
the tombs. Tombs are like single files whose contents are inaccessible
in the absence of the key they were locked with and its password.

Once open, the tombs are just like normal folders and can contain
different files, plus they offer advanced functionalities like bind
and execution hooks and fast search, or they can be slammed close even
if busy. Keys can be stored on separate media like USB sticks, NFC,
on-line SSH servers or Bluetooth devices to make the transport of data
safer: one always needs both the tomb and the key, plus its password,
to access it.

The tomb script takes care of several details to improve user's
behaviour and the security of tombs in everyday usage such as:
* protects the typing of passwords from keyloggers,
* facilitates hiding keys inside images,
* indexes and search a tomb's contents,
* mounts directories in place,
* lists open tombs and selectively closes them,
* warns the user about free space and last time usage.

# ![MM] How secure is this?

Death is the only sure thing in life. That said, Tomb is a pretty
secure tool especially because it is kept minimal, its source is
always open to review (even when installed) and its code is easy to
read with a bit of shell script knowledge.

All encryption tools being used in Tomb are included as default in
many GNU/Linux operating systems and therefore are regularly peer
reviewed: we do not add anything else to them really, just a layer of
usability.

The file [KNOWN_BUGS.md](KNOWN_BUGS.md) contains some notes on known
vulnerabilities and threat model analysis.

In absence or malfunction of the Tomb script, it is always possible to
access the contents of a Tomb only using a dm-crypt enabled Linux
kernel, cryptsetup, GnuPG and any shell interpreter issuing the
following commands as root:
```
lo=$(losetup -f)
losetup -f secret.tomb
pass="$(gpg -d secret.key)"
echo -n -e "$pass" | cryptsetup --key-file - luksOpen $lo secret
mount /dev/mapper/secret /mnt
unset pass
```
One can change the last argument `/mnt` to where the Tomb has to be
mounted and made accessible. To close the tomb then use:
```
umount /mnt
cryptsetup luksClose /dev/mapper/secret
```

# ![MM] Stage of development

Tomb is an evolution of the 'mknest' tool developed for the
[dyne:bolic](http://www.dynebolic.org) 100% Free GNU/Linux
distribution in 2001: its 'nesting' mechanism allowed the liveCD users
to encrypt and make persistent home directories. Since then the same
shell routines kept being maintained and used for dyne:bolic until
2007, when they were ported to work on more GNU/Linux distributions.

As of today, Tomb is a very stable tool also used in mission critical
situations by a number of activists in dangerous zones. It has been
reviewed by forensics analysts and it can be considered to be safe for
military grade use where the integrity of information stored depends
on the user's behaviour and the strength of a standard AES-256 (XTS
plain) encryption algorithm.

## Compatibility

Tomb can be used in conjunction with some other software applications,
some are developed by Dyne.org, but some also by third parties.

### Included extra applications

- [GTomb](extras/gtomb) is a graphical interface using zenity
- [gtk-tray](extras/gtk-tray) is a graphical tray icon for GTK panels
- [qt-tray](extras/qt-tray) is a graphical tray icon for QT panels
- [tomber](extras/tomber) is a wrapper to use Tomb in Python scripts

<p align="center">
  <img src="https://raw.githubusercontent.com/dyne/Tomb/master/extras/images/python_for_tomb.png"
       alt="skulls and pythons">	    
</p>

### External applications

- [Secrets](https://secrets.dyne.org) is a software that can be operated on-line and on-site to split a Tomb key in shares to be distributed to peers: some of them have to agree to combine back the shares in order to retrieve the key.

- [zuluCrypt](https://mhogomchungu.github.io/zuluCrypt/) is a graphical application to manage various types of encrypted volumes on GNU/Linux, among them also Tombs, written in C++.

- [Mausoleum](https://github.com/mandeep/Mausoleum) is a graphical interface to facilitate the creation and management of tombs, written in Python.

- [pass-tomb](https://github.com/roddhjav/pass-tomb) is a console extension of the excellent password keeping program [pass](https://www.passwordstore.org) that helps to keep the whole tree of password encrypted inside a tomb. It is written in Bash.

If you are writing a project supporting tomb volumes or wrapping tomb, let us know!


## Compliancy

Tomb qualifies as sound for use on information rated as "top secret"
when used on an underlying stack of carefully reviewed hardware
(random number generator and other components) and software (Linux
kernel build, crypto modules, device manager, compiler used to built,
shell interpreter and packaged dependencies).

Tomb volumes are fully compliant with the FIPS 197 advanced encryption
standard published by NIST and with the following industry standards:

- Information technology -- Security techniques -- Encryption algorithms
	- [ISO/IEC 18033-1:2015](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=54530)  -- Part 1: General
	- [ISO/IEC 18033-3:2010](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=54531) -- Part 3: Block ciphers

Tomb implementation is known to address at least partially issues raised in:

- Information technology -- Security techniques -- Key management
	- [ISO/IEC 11770-1:2010](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=53456)  -- Part 1: Framework
	- [ISO/IEC 11770-2:2008](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=46370)  -- Part 2: Mechanisms using symmetric techniques
- [ISO/IEC 27005:2011](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=56742) Information technology -- Security techniques -- Information security risk management
- [ISO/IEC 24759:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=59142) Information technology -- Security techniques -- Test requirements for cryptographic modules

Any help on further verification of compliancy is very welcome, as the
access to ISO/IEC document is limited due to its expensive nature.


# ![MM] Use stable releases in production!

Anyone planning to use Tomb to store and access secrets should not use
the latest development version in Git, but use the .tar.gz
release on https://files.dyne.org/tomb instead. The stable version will
always ensure backward compatibility with older tombs: we make sure it
creates sane tombs and keys by running various tests before releasing
it. The development version in Git might introduce sudden bugs and is
not guaranteed to produce backward- or forward-compatible tombs and keys.
The development version in Git should be used to report bugs, test new
features and develop patches.

**So be warned:** do not use the latest Git version in production
environments, but use a stable release versioned and packed as
tarball on https://files.dyne.org/tomb

<p align="center">
  <img src="https://raw.githubusercontent.com/dyne/Tomb/master/extras/images/DayOfTheDead.jpg"
       alt="Day of the dead">	    
</p>

# ![MM] How can you help

Donations are very welcome, please go to https://www.dyne.org/donate

Translations are also welcome: they can be contributed editing sending
the .po files in [extras/translations](extras/translations).

The code is pretty short and readable. There is also a collection of
specifications and design materials in the [doc](doc) directory.

To contribute code and reviews visit https://github.com/dyne/Tomb

If you plan to commit code into Tomb, please keep in mind this is a
minimalist tool and its code should be readable. Guidelines on the
coding style are illustrated in [doc/HACKING.txt](doc/HACKING.txt).

Tomb's developers can be contacted using the issues on GitHub or over
IRC on https://irc.dyne.org channel **#dyne** (or direct port 9999 SSL)

# ![MM] Licensing

Tomb is Copyright (C) 2007-2018 by the Dyne.org Foundation and
maintained by Denis Roio <jaromil@dyne.org>. More information on all
the developers involved is found in the [AUTHORS](AUTHORS.md) file.

This source code is free software; you can redistribute it and/or
modify it under the terms of the GNU Public License as published by
the Free Software Foundation; either version 3 of the License, or
(at your option) any later version.

This source code is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  Please refer
to the GNU Public License for more details.

You should have received a copy of the GNU Public License along with
this source code; if not, write to: Free Software Foundation, Inc.,
675 Mass Ave, Cambridge, MA 02139, USA.

[tomb]: https://www.dyne.org/software/tomb
[release]: https://files.dyne.org/tomb
[install]: INSTALL.md
[MM]: https://raw.githubusercontent.com/dyne/Tomb/master/extras/images/monmort.png

[travis-badge]: https://img.shields.io/travis/dyne/Tomb/master.svg?style=flat-square
[travis-link]: https://travis-ci.org/dyne/Tomb
[codacy-badge]: https://img.shields.io/codacy/grade/ea6cb4eb6f0e4520a1271d29c7875891/master.svg?style=flat-square
[codacy-link]: https://www.codacy.com/app/roddhjav/Tomb
[release-badge]: https://img.shields.io/github/tag/dyne/Tomb.svg?maxAge=600&style=flat-square
[release-link]: https://files.dyne.org/tomb
[irc-badge]: https://img.shields.io/badge/irc.dyne.org-%23dyne-blue.svg?style=flat-square
[irc-link]: https://irc.dyne.org/?channels=dyne
[jaromil-link]: https://jaromil.dyne.org/
