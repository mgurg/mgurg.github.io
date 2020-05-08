---
layout: post
title: "Git: Podstawy"
categories: git
author: "Michał"
---



Praca w firmie z graficznym SVN dała mi podstawy idei systemów kontroli wersji. Był on używany w większości do plików binarnych, co powodowało że było to raczej bardziej rozbudowane narzędzie do tworzenia kopii przyrostowych. Do wersjonowania swoich projektów używałem na początku GitHub poprzez desktopową aplikację GitHub Desktop. 

Pierwszym krokiem była doinstalowanie osobno [git](https://git-scm.com/) tak żeby móc korzystać z niego poprzez edytor (*VS Code*) który daje mi większą kontrolę niż GithubDesktop. Kolejnymi krokami ma być nauczenie się prawidłowego podejścia.

[How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)

[Git Commit Message Style Guide](http://udacity.github.io/git-styleguide/)

### Teoria

Obszary:

- Stash - rodzaj schowka (zawartość `git stash list`, wyczyszczenie zawartości `git stash clear`)

- Working area

- Staged area (Index)  - zawartość która wyląduje w najbliższym commicie

- Repozytorium

| Stash                          | Working area                                  | Staged area (Index)      | Repozytorium |
| ------------------------------ | --------------------------------------------- | ------------------------ | ------------ |
|                                | file                                          | file                     | file         |
|                                | file1                                         | file1 (`git commit`) --> |              |
|                                | file2 (`git add`) -->                         |                          |              |
|                                | <--- file 3 (`git stash --include-untracked`) |                          |              |
| file3 (`git stash apply`)  --> |                                               |                          |              |

Wyświetlenie różnic pomiędzy Working area a Staged area:

```
git diff
```

Tak naprawdę pokazuje to co będę commitował.

Porównanie  Staged area i repozytorium:

```
git diff --cached
```





## .gitignore

Plik który ma zapisane informacje o tym które pliki mają być ignorowane. Predefiniowane [szablony dla różnych języków](https://github.com/github/gitignore) od Github.

Źródło: [link](https://www.atlassian.com/git/tutorials/saving-changes/gitignore#git-ignore-patterns)

| Pattern                                 | Example matches                                              | Explanation*                                                 |
| --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `**/logs `                              | `logs/debug.log`  `logs/monday/foo.bar`  `build/logs/debug.log` | You can prepend a pattern with a double asterisk to match directories anywhere in the repository. |
| `**/logs/debug.log `                    | `logs/debug.log`  `build/logs/debug.log`  *but not*  `logs/build/debug.log` | You can also use a double asterisk to match files based on their name and the name of their parent directory. |
| `*.log`                                 | `debug.log`  `foo.log`  `.log`  `logs/debug.log`             | An asterisk is a wildcard that matches zero or more characters. |
| `*.log `  `!important.log`              | `debug.log`  `trace.log`  *but not*  `important.log`  `logs/important.log` | Prepending an exclamation mark to a pattern negates it. If a file matches a pattern, but *also* matches a negating pattern defined later in the file, it will not be ignored. |
| `*.log `  `!important/*.log`  `trace.*` | `debug.log`  `important/trace.log`  *but not*  `important/debug.log` | Patterns defined after a negating pattern will re-ignore any previously negated files. |
| `/debug.log`                            | `debug.log`  *but not*  `logs/debug.log`                     | Prepending a slash matches files only in the repository root. |
| `debug.log`                             | `debug.log`  `logs/debug.log`                                | By default, patterns match files in any directory            |
| `debug?.log `                           | `debug0.log`  `debugg.log`  *but not*  `debug10.log`         | A question mark matches exactly one character.               |
| `debug[0-9].log `                       | `debug0.log`  `debug1.log`  *but not*  `debug10.log`         | Square brackets can also be used to match a single character from a specified range. |
| `debug[01].log `                        | `debug0.log`  `debug1.log`  *but not*   `debug2.log`  `debug01.log` | Square brackets match a single character form the specified set. |
| `debug[!01].log `                       | `debug2.log`  *but not*  `debug0.log`  `debug1.log`  `debug01.log` | An exclamation mark can be used to match any character except one from the specified set. |
| `debug[a-z].log `                       | `debuga.log`  `debugb.log`  *but not*  `debug1.log`          | Ranges can be numeric or alphabetic.                         |
| `logs`                                  | `logs`  `logs/debug.log`  `logs/latest/foo.bar`  `build/logs`  `build/logs/debug.log` | If you don't append a slash, the pattern will match both files and  the contents of directories with that name. In the example matches on  the left, both directories and files named *logs* are ignored |
| logs/                                   | `logs/debug.log`  `logs/latest/foo.bar`  `build/logs/foo.bar`  `build/logs/latest/debug.log` | Appending a slash indicates the pattern is a directory. The entire  contents of any directory in the repository matching that name –  including all of its files and subdirectories – will be ignored |
| `logs/ `  `!logs/important.log`         | `logs/debug.log`  `logs/important.log`                       | Wait a minute! Shouldn't `logs/important.log` be negated in the example on the left   Nope! Due to a performance-related quirk in Git, you *can not* negate a file that is ignored due to a pattern matching a directory |
| `logs/**/debug.log `                    | `logs/debug.log`  `logs/monday/debug.log`  `logs/monday/pm/debug.log` | A double asterisk matches zero or more directories.          |
| `logs/*day/debug.log `                  | `logs/monday/debug.log`  `logs/tuesday/debug.log`  *but not*  `logs/latest/debug.log` | Wildcards can be used in directory names as well.            |
| `logs/debug.log`                        | `logs/debug.log`  *but not*  `debug.log`  `build/logs/debug.log` | Patterns specifying a file in a particular directory are relative  to the repository root. (You can prepend a slash if you like, but it  doesn't do anything special.) |



## Instrukcja Krok po kroku

Inicjalizacja lokalnego repozytorium (utworzonego wcześniej):

```bash
> git init
Reinitialized existing Git repository in D:/Github/py_otomoto/.git/
```

Status lokalnych plików: 

```bash
> git status

On branch dev
Your branch is up to date with 'origin/dev'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   .gitignore
        modified:   README.md
        modified:   carDataParser.py
        modified:   plotData.py

no changes added to commit (use "git add" and/or "git commit -a")
mgu@DESKTOP-HUHLHHH /mnt/d/Github/py_otomoto
```

**Dodanie** nowego pliku:

```
git add nazwa_pliku
```

lub wielu plików:

```
git add .
```

Zatwierdzenie modyfikacji (do sprawdzenia: `git add -p`)

```bash
git add -u
```

**Usuwanie** plików

```bash
git rm
```

Dostępne opcje: osunięcie pliku wyłącznie z staged area:

```bash
git rm --cached nazwa_pliku
```

Całkowite usunięcie pliku (forced) z Working area i Staged area:

```bash
git rm -f nazwa_pliku
```



**Commit**:

```bash
git commit
```

jeżeli mamy krótki *commit message* to można zrobić to w jednej linii, bez przechodnia do edytora:

```bash
git commit -m "commit description"
```

Łączenie kilku commitów w jeden większy (dozwolone tylko lokalnie!):

```
git commit --amend
```



Status zmian:

```bash
> git log
commit 1f2d124d6737020ee40fdbdfac990605bc4e6855 (HEAD -> dev)
Author: mgurg <mgu@users.noreply.github.com>
Date:   Sat Feb 8 16:37:04 2020 +0100

    Changed readme.md

commit 1327212e335e8e6222cb9c3e20a2ed599c799a93 (origin/dev)
Author: mgurg <mgu@users.noreply.github.com>
Date:   Sat Jan 25 21:16:41 2020 +0100

    vs code test
```

Jeżeli potrzebna jest bardziej graficzna wersja można użyć komendy:

```bash
> git log --graph --decorate
* commit 1f2d124d6737020ee40fdbdfac990605bc4e6855 (HEAD -> dev, origin/dev)
| Author: mgurg <mgu@users.noreply.github.com>
| Date:   Sat Feb 8 16:37:04 2020 +0100
|
|     Changed readme.md
|
* commit 1327212e335e8e6222cb9c3e20a2ed599c799a93
| Author: mgurg <mgu@users.noreply.github.com>
| Date:   Sat Jan 25 21:16:41 2020 +0100
|
|     vs code test
```

Bardziej kompaktowo:

```bash
> git log --graph --decorate --oneline
* 1f2d124 (HEAD -> dev, origin/dev) Changed readme.md
* 1327212 vs code test
* 7cf04b3 Creat plotData
* 33da89d rewrited code
* f8ab4a0 merge csv - comleted
* 7f0788f Update carDataParser.py
* 37b49b2 Revert "merge csv files - initial code"
* 8aa8af1 merge csv files - initial code
* b2279ba merge csv files - initial code
* f51894a parser for data from scrappy
*   131800f Merge branch 'dev' of https://github.com/mgurg/py_otomoto into dev
|\
| * e4ad521 list csv files
* | a9ac3fb error handling
|/
* c3e6a50 update .gitignore
```

Historia wpisów w pliku:

```bash
git blame .\carDataParser.py
f51894a8 (mgurg 2020-01-02 19:11:22 +0100   1) # -*- coding: utf-8 -*-
f51894a8 (mgurg 2020-01-02 19:11:22 +0100   2)
f51894a8 (mgurg 2020-01-02 19:11:22 +0100   3) import requests, bs4, io, csv, datetime
f51894a8 (mgurg 2020-01-02 19:11:22 +0100   4) from timeit import default_timer as timer
f51894a8 (mgurg 2020-01-02 19:11:22 +0100   5) import re
f51894a8 (mgurg 2020-01-02 19:11:22 +0100   6) import os
b2279ba2 (mgurg 2020-01-04 13:44:06 +0100   7) import pandas as pd
33da89da (mgurg 2020-01-14 18:31:24 +0100   8) import shutil
f51894a8 (mgurg 2020-01-02 19:11:22 +0100   9)
f51894a8 (mgurg 2020-01-02 19:11:22 +0100  10) start = timer()
f51894a8 (mgurg 2020-01-02 19:11:22 +0100  11)
b2279ba2 (mgurg 2020-01-04 13:44:06 +0100  12) def parse_html2csv(html_file : str):
b2279ba2 (mgurg 2020-01-04 13:44:06 +0100  13)     #html_file = "otomoto_2019-12-31.html"
f8ab4a01 (mgurg 2020-01-13 21:40:56 +0100  14)     fname = 'otomoto_'+ html_file[8:18] 
b2279ba2 (mgurg 2020-01-04 13:44:06 +0100  15)     carFile = io.open(fname, 'w')
```



Wysłanie commitu na zdalny serwer Github (dodatkowa informacja: [autoryzacja komputera poprzez SSH](https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)):

```bash
git push
```

Bardziej zaawansowana forma:

```bash
#gałąź dev
git push -u origin dev

#gałąź master
git push -u origin master
```

### Branches

Lista dostępnych gałęzi w projekcie:

```bash
> git branch
* dev
  master
```

Dodanie nowej gałęzi

```bash
> git branch nazwa_galezi
```

Zmiana aktywnej gałęzi (HEAD wskazuje na gałąź która jest uznawana za główną)

```bash
git checkout nazwa_galezi
```

Łącznie gałęzi:

```
git merge nazwa_galezi 
```

Jeżeli chcę przeprowadzić eksperymenty, a nie mam pewności czy coś z tego wyjdzie to mogę skorzystać z detached HEAD (`git checkout` do SH1 commitu, bez podawania branch-a). Jeżeli chcę zachować zmiany muszę pamiętać o utworzeniu właściwej gałęzi na koniec.

 **merge & rebase**

Merge zachowuje historię.

Rebase przenosi równoległą gałąź na "górę". Modyfikuję historię 



**Poruszanie** się po gałęzi:

Wyświetlenie zawartości konkretnego commitu:

```
git show HEAD
git show HEAD^  # rodzic ostatniego commitu
git show HEAD^^ # "dziadek" ostatniego commmitu
git show HEAD~2 # "dziadek" ostatniego commmitu
```



```bash
git reset SHA1
```

Przesuwa HEAD do wybranego commita w branchu, możemy w ten sposób wrócić się do jakiegoś punktu w historii, jeżeli późniejsze modyfikacje okazały się zbędne/błędne. Dostępne opcje

```
--hard (Przesuwa HEAD w repozytorium, uaktualnia Working area i Staged area)
--mixed (Przesuwa HEAD w repozytorium, uaktualnia Staged area)
--soft (Przesuwa HEAD w repozytorium, nie zmienia Working area i staged area)
```



| Working area | Staged area (Index) | Repozytorium                        |
| ------------ | ------------------- | ----------------------------------- |
| <--          | <---                | (`git reset --hard`) commit #5asd3  |
|              | <--                 | (`git reset --mixed`) commit #5asd3 |
|              |                     | (`git reset --s`oft) commit #5asd3  |

Nadpisanie Working Area i Staged Area zawartością ostatniego commitu:

```
git reset --hard HEAD
```

Ta instrukcja zresetuje naszą pracę bez oglądania się na to co do tej pory mieliśmy zrobione od ostaniego commitu w repozytorium

**Powrót** ze stanem pliku (w Working area i Staged Area) do tego który jest aktualnie w ostatnim commicie w repozytorium (utracimy wszystkie wporwadzone w między czasie zmiany):

```
git checkout HEAD naza_pliku
```

### Modyfikacja commitu

Wskazujemy commit od którego zamierzamy modyfikować historię (znowu: tylko lokalnie)
```
git rebase -i origin/master
git rebase --interactive  origin/master
```





### Tagi

Dodawanie:

```
git tag -a nazwa
git tag nazwa
```

atrybut -a jest wykorzystywany do stwoerzenia bardziej rozbudowanego tagu ("annotated" z datą, autorem itd.) Różnica pomiędzy `branch` a `tag`: tag jest przytwierdzony do konkretnego commita, nie zmienia pozycji wraz z z kolejnymi aktualizacjami projektu

### Praca ze zdalnym repo:

`origin` - zdalne repo na github?

`fetch` (pobieranie zmian ze zdalnego serwera) + `merge` (skonsolidowanie zmian które mamy u siebie) -> `push` (wypchnięcie zmian na zdalny serwer)



## GIT Workflow (wg. [kursgita.pl](https://kursgita.pl/)):



`Nigdy nie commitujemy do mastera!`



**01) Feature Branch**

- git checkout -b <feature branch>
- pracujemy na feature branches (konwencja: te gałęzie są „prywatne”)



**02) Wysłanie gałęzi na serwer**

* git push origin -u <feature branch>
* nie bać się PUSH! (bo „konwencja prywatności”...)
* 3 cele:
  * cały zespół widzi co jest „in progress” (git branch -a)
  * potencjalnie code review
  * backup



**03) Praca (repeat)**

* git add -p
*  git commit
* regularny git push



**04) Porządki w historii**

* git rebase -i master
* squash / edit / fixup / reword
* regularny git push --force-with-lease (jeden z niewielu przypadków gdy jest to dozwolone!)



**05) Integracja**

* git checkout master

* git pull

* git checkout master -

* git rebase master -p

* Uwagi

  * robć często, regularnie!

  * tutaj konflikty (nie boimy się ich bo mamy RERERE)

    

**06) Merge**

* git checkout master
*  git merge --no-ff --edit -
* w „*commit message*” pełna treść zadania i uwagi



**07) Zmiany na serwer**

* git push
* błąd?
  * wycofujemy merge: git reset --hard ORIG_HEAD
  * powtarzamy operację integracji


**08) Porządki**

* git push origin :<feature branch>
* git branch -d <feature branch>
* git remote update --prune




