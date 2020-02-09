---
layout: post
title: "Git: Podstawy"
categories: python
author: "Michał"
---



Praca w firmie z graficznym SVN dała mi podstawy idei systemów kontroli wersji. Był on używany w większości do plików binarnych, co powodowało że było to raczej bardziej rozbudowane narzędzie do tworzenia kopii przyrostowych. Do wersjonowania swoich projektów używałem na początku githuba poprzez desktopową aplikację GitHub Desktop. 

Pierwszym krokiem była doinstalowanie osobno [git](https://git-scm.com/) tak żeby móc korzystać z niego poprzez edytor (VS Code) który daje mi większą kontrolę niż GithubDesktop. Kolejnymi krokami ma być nauczenie się prawidłowego podejścia.

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




