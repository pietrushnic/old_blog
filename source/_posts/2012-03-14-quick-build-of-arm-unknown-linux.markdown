--- layout: post title: "Quick build of arm-unknown-linux-gnueabi with crosstool-ng" date: 2012-03-14T23:42:00+01:00 categories: - embedded ---

You might be surprised at how much you have to make to correctly build arm-unknown-linux-gnueabi config based toolchain with crosstool-ng. As you can see examples of many open source projects, the man's work is a rare resource. The result of this economic fact is that the attempt to build configuration arm-unknown-linux-gnueabi is not a simple task and during an operation you can come across many problems. Although I am not afraid of problems and effectively try to fight them and of course sharing the results of my work. My build system parameters:  
Debian GNU/Linux wheezy/sid 3.2.0-2-amd64  

1. Clone crosstools-ng (you need mercurial)
hg clone http://crosstool-ng.org/hg/crosstool-ng
2. Create temporary directory and run
ct-ng arm-unknown-linux-eabi choose latest kernel (for me it was 3.2.9)
3. We probably want to change directory to which all stuff will be build (default is $HOME/x-tools):
4.
ct-ng menuconfig And go to:
Paths and misc options ---> (${HOME}/x-tools/${CT\_TARGET}) Prefix directory Change it according to your needs. Exit end save configuration.
5. Build (number depend on how many command we want to run simultaneously):
ct-ng build.4 It can take a lot of time. On my machine with 5k BogoMips it takes over 1h.

Problems that you can encounter:  

1. gcj - latest changeset 2916:6f758ed4c0b9 have trouble finding gcj binary, which it show using following message:
[ERROR] Missing: 'x86\_64-unknown-linux-gnu-gcj' or 'x86\_64-unknown-linux-gnu-gcj' or 'gcj' : either needed! To workaround this install gcj and link binary like this:
sudo ln -s /usr/bin/gcj-4.6 /usr/bin/gcj
2. duma - mentioned changeset also has problem with url to D.U.M.A library, apply below changes to workaround problems:
--- a/scripts/build/debug/200-duma.sh Mon Mar 12 21:19:26 2012 +0100 +++ b/scripts/build/debug/200-duma.sh Wed Mar 14 20:02:22 2012 +0100 @@ -4,7 +4,7 @@ # Downloading an non-existing file from sourceforge will give you an # HTML file containing an error message, instead of returning a 404. # Sigh... - CT\_GetFile "duma\_${CT\_DUMA\_VERSION}" .tar.gz http://kent.dl.sourceforge.net/sourceforge/duma/ + CT\_GetFile "duma\_${CT\_DUMA\_VERSION}" .tar.gz http://downloads.sourceforge.net/project/duma/duma/2.5.15 # Downloading from sourceforge may leave garbage, cleanup CT\_DoExecLog ALL rm -f "${CT\_TARBALLS\_DIR}/showfiles.php"\* }
3. mawk - if mawk return syntax error like this:
mawk: scripts/gen-sorted.awk: line 19: regular expression compile failed (bad class -- [], [^] or [) It could be fixed in two ways. First is to change line 19 in /path/to/tmp/dir/.build/src/glibc-2.9/scripts/gen-sorted.awk Is:
sub(/\/[^/]+$/, "", subdir); Should be:
sub(/\/[^\/]+$/, "", subdir); Or simply by installing gawk, reconfigure and recompile crosstools-ng.

This was my first post related to linux embedded enviroment. Hope it will be more. Enjoy!

## Comments

Jagan

Does croostool-ng is specific to linux versions?  
    
when I use crosstool-ng-1.9.3 it uses the linux-2.6.33.7, how  
can i modify to use the crosstool-ng to specific kernel version which is  
not available to menuconfig?  
    
Thanks, ./J

Piotr Król

Próbowałem wybudować najnowsze źródła crosstool'a (changeset:3033:9ddca166bb27) na Debianie 3.2.0-3-amd64. Niestety nie udało mi się, wywalił się na 'Installing final compiler' (.config: http://pastebin.com/P2izrvMH). Artykuł, który widzisz powyżej był pisany 5 miesięcy temu nie pamiętam dokładnie wszystkich ustawień a tym bardziej nie posiadam logów i configów.

Mateusz Maciaszek

Inaczej - byłbyś w stanie podrzucić pliczek .config ct-ng?

Mateusz Maciaszek

Downgrade do wersji 1.11.1 pomógł, przy czym, oczywiscie, nie obyło się pozniej bez mniejszych problemow, jak - przykladowo - zbyt stara wersja make (zejscie z 3.82 do 3.81).  
Teraz się męczę z instalacją glibc... Mógłbyś mi powiedzieć z jakich wersji gcc i glibc korzystałeś (w menuconfig ct-ng) oraz make?

Piotr Król

Z tego co widzę w Twoim build logu:  
/usr/share/aclocal-1.12/protos.m4:12: AM\_C\_PROTOTYPES is expanded from...  
Googlając można znaleźć coś takiego w release notach automake 1.12:  
"Support for automatic de-ANSI-fication has been removed."  
Spróbuj przerzucić się na automake 1.11 i daj znać czy to coś pomogło.

Mateusz

Podsylam screena + loga z pracy [ http://www.sendspace.pl/file/828578c39b349c935485e31 ].  
Jest to próba konfiguracji cross-compilatora [arm-unknown-linux-gnueabi-] pod Archem x86\_64 (kernel 3.6.0-rc1), croostool-ng 1.16.  
    
    
[autoconf, wersja 2.69]  
Jeśli chodzi o autom4te -- korzystając z przykładowego źródla http://git.savannah.gnu.org/gitweb/?p=autoconf-archive.git;a=blob\_plain;f=m4/ax\_absolute\_header.m4 i zapisując całość pod postacią a.m4 a następnie wykonując polecenie 'autom4te a.m4 -o test', całość przeszła pomyślnie. Niestety nie wiem czy jest to ostatecznie dobry i wiarygodny test poprawności działania programu. Jeśli masz lepszy pomysł, proszę, podziel się z nim.  
    
[autoconf, wersja 2.68, kompilacja ze źródeł]  
Niestety, błąd jest ten sam (czyt. żadnej poprawy).

Piotr Król

Niestety nie. Próbowałeś to zmienić ? Sprawdzałeś czy autom4te działa poprawnie ? Gdybyś podesłał mi szczegóły buildu spróbowałbym to odtworzyć i może do czegoś się dokopać.

Mateusz

Witam.  
Czy spotkałeś się może kiedykolwiek z podobnym problemem podczas buildowania toolchaina przy pomocy crosstool-ng?  
    
[INFO ] Extracting and patching toolchain components  
[ERROR] configure.in:294: error: automatic de-ANSI-fication support has been removed  
[ERROR] aclocal: error: /usr/bin/autom4te failed with exit status: 1

