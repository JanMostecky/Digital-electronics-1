# Tachometr pro jízdní kolo - oprava

Vypracovali: Jan Mostecký (221478), Klimeš Jiří (212561), Němec Petr (221480), 

[Odkaz na github složku](https://github.com/JanMostecky/Digital-electronics-1/tree/main/projekt%20-%20oprava)

[Původní verze projektu](https://github.com/JanMostecky/Digital-electronics-1/tree/main/project)

------------------------------

## Cíl Práce

Cílem práce bylo opravit původní neuznané řešení návrhu cyklo-tachometru. 

Realizovat jsme se rozhodli následující funkce tachometru: Měření rychlosti a měření ujeté vzdálenosti. 
Pro měření rychlosti byly zvoleny kilometry v hodině a pro ujetou vzdálenost kilometry. Obě jednotky budou zobrazeny čtyřmístným číslem obsahujícím desítky metrů, stovky metrů, kilometry a desítky kilometrů. 

-----------------------------

## Použitý Hardware

Vytvořený kód měl být implementovatelný na desky Arty A7 - 35T, or Arty A7 - 100T. Pro tento projek byla zvolena deska Arty A7 - 35T.

Odkaz na stránky výrobce desky: Artix-7 FPGA Development Board:
https://store.digilentinc.com/arty-a7-artix-7-fpga-development-board/

Technická dokumentace desky, uživatelské fórum: 
https://reference.digilentinc.com/reference/programmable-logic/arty-a7/start

Manuál:
https://reference.digilentinc.com/reference/programmable-logic/arty-a7/reference-manual

Schéma:
https://reference.digilentinc.com/_media/reference/programmable-logic/arty-a7/arty_a7_sch.pdf

-----------------------------

## Teoretický návrh tachometru

Tachometr je ovládán jedním přepínačem a jedním tlačítkem

- Přepínač: 
Přepínání mezi zobrazovanými veličinami. P/okud je přepínač v "nule" zobrazí se ujetá vzdálenost. Když je přepnut do "jedničky" na display se zobrazí momentální rychlost. 

- Tlačítko:
Resetování celého zařízení, vynulování přičítání ujeté vzdálenosti. 

Zobrazení na display: 
Přepínání zobrazených veličin na display zařizuje stavový automat ovládaný přepínačem. Jednotlivé číslice pak  4 to 1 multiplexor - ovládaný čítečem - posílá na jednotlivé displaye. Rychlost přepínaných číslic je větší než rozlišovací schopnost lidského oka a proto se číslice jeví zobrazeny naráz. (Postup pro zobrazení dat ze cvičení). 

### Schéma top modulu

shéma top modulu s vyznačenými signály a dílčími moduly

![topmodul]()

------------------------

## Design moduly a jejich popis

### Velocity


### Distance

### Clock enable

### Count up down

### FSM

### mux_4_to_1

### hex_to_7seg


## Testbench moduly a simulace

### Velocity

### Distance

### Count up down

### FSM

### mux_4_to_1

### hex_to_7seg

## Top modul

### Design

### Testbench



