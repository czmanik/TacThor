# TacThor

**TacThor** je offline-first velitelský systém pro airsoft / LARP operace, navržený pro prostředí se slabým nebo žádným signálem.

TacThor = Tactical + Thor  
(neoficiálně: „Traktor“ 😄)

## Hlavní cíl

Zlepšit velení v terénu:

- lepší přehled o jednotkách
- rychlejší rozhodování
- offline fungování
- méně chaosu v komunikaci

## Klíčové vlastnosti

- offline-first architektura
- event-based sync
- ultra low bandwidth režim
- GPS tracking s úsporou baterie
- Big Picture (automatická interpretace situace)
- QR systém pro herní logiku
- práce s nepřítelem (intel, směry, odhady)

## Architektura

Mobile (Flutter)  
→ API (Node/NestJS)  
→ PostgreSQL + PostGIS  
→ Web dashboard (Command / GM)

## Stav

🚧 MVP ve vývoji  
🎯 první nasazení: Event Zero

## Filosofie

TacThor není mapa.  
Je to nástroj pro rozhodování. 
