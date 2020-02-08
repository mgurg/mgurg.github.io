---
layout: post
title: "Git: Podstawy"
categories: python
author: "Michał"
---



Praca w firmie z graficznym SVN dała mi podstawy idei systemów kontroli wersj. Był on używany w większości do plików binarnych, co powodowało że było to raczej bardziej rozbudowane narzędzie do tworzenia kopii przyrostowych. Do wersjonowania swoich projektów używałem na początku githuba poprzez desktopową aplikację GitHub Desktop. 

Pierwszym krokiem była doinstalowanie osobno [git](https://git-scm.com/) tak żeby móc korzystać z niego poprzez edytor (VS Code) który daje mi większą kontrolę niż GithubDesktop. Kolejnymi krokami ma być nauczenie się prawidłowego podejścia:



GIT Workflow (wg. [kursgita.pl](https://kursgita.pl/)):

### Nigdy nie commitujemy do mastera!

**01) Feature Branch**

- git checkout -b <feature branch>
- pracujemy na feature branches (konwencja: te gałęzie są „prywatne”)



**02) Wysłanie gałęzi na serwer **

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



**07) Zmiany na serwer***

* git push
* błąd?
  * wycofujemy merge: git reset --hard ORIG_HEAD
  * powtarzamy operację integracji


**08) Porządki**

* git push origin :<feature branch>
* git branch -d <feature branch>
* git remote update --prune




