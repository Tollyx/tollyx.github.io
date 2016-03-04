---
layout: post
title: "Code Review: Grupp 9, spelarklass"
date: "2016-03-04 13:31:05 +0100"
author: "Adrian Hedqvist"
categories: game
comments: true
tags:
 - 5SD023
---

Det allra första som jag tänkte på när jag kollade igenom spelarklassen var det att ni använder en inputklass, men ni använder den till saker som SFML redan har inbyggt. `sf::Mouse` och `sf::Keyboard` har redan det ni behöver, varför återskapa det? Det enda som jag kan komma på som man skulle kunna använda en egen inputklass till som SFML inte redan gör åt en är keybinds, vilket ni kanske har planerat.

Men ett bättre sätt för att få bort input ifrån spelarklassen skulle nog vara att kolla input utanför klassen för att sedan anropa t.ex. `giraffe.fire()` om skjutknappen var nedtryckt. En annan idé (som jag inte har provat kan vara värt att nämna) skulle vara att skicka in via update eller någon annan funktion en "event" enum (t.ex. `EVENT_MOVELEFT`, `EVENT_FIRE` osv) som karaktären använder för att göra saker med. En rolig sak med det här är att om man gör så att AI'n "kör" fienderna på samma sätt så skulle man lätt kunna ta kontroll över fienderna och AI'n ta kontroll över spelarkaraktären.

Och då kommer vi till entitymanagern som ni använder för att skapa nya projektiler. Jag skulle personligen vilja hålla vad för projektiler och hur inne i spelarklassen, så medans man skulle kunna göra en `isFiring()` funktion och flytta all spawning till entitymanagern eller gamestatet, så skulle jag inte vilja göra det. En annan lösning skulle kunna vara att man har någon funktion som returnerar alla objekt som klassen skapar, men det tror jag inte är någon alltför bra idé det heller. Utöver det så vet jag inte riktigt, jag skulle vilja få ut entitymanagern därifrån, men eftersom jag inte vet någon bra lösning så skulle jag lämna den där.

Trots det jag har skrivit så tycker jag att det ser bra ut. Men jag kan inte låta bli att bli lite nyfiken på varför ni har bestämt att skippa en hel del saker som SFML kan ta hand om åt en, som att ni inte använder `sf::Sprite` och `sf::RenderWindow`s draw-funktion. Jag säger inte att ni gör något fel, utan jag förstår bara inte riktigt varför.
