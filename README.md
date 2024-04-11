# Baza_de_date_MySQL_Dino
1.	Descriere bazei de date
Enciclopedia se ocupă de catalogarea animalelor dispărute. 

Enciclopedia are mai multe animale preistorice. Pentru fiecare animal preistoric se rețin ordinul, subordinul, suprafamilia, familia, subfamilia, genul și specia.

Fiecare animal are câte un singur rol în ecosistem pentru fiecare animal. Rolul este descris de atributele prădător/pradă, poziția în lanțul trofic și dietă.

Animalele din enciclopedie aparțin unui singur mediu. Fiecare mediu este caracterizat de o țară, perioadă geologică și teren (ex: acvatic, terestru_deșertic, terestru_polar) și conține cel puțin un animal.

Fiecare animal a fost descoperită de cel puțin un paleontolog, care la rândul lui a trebuit să fi descoperit cel puțin un animal. Pentru fiecare paleontolog este reținut numele, prenumele, data și locația descoperirii, precum și numele muzeelor ce conțin specimene. 

Fiecare animal e caracterizată de o biomecanică, aceasta constă în viteză, dimensiuni (lungime, înălțime, greutate, anvergură (la zburătoare) ) și forța mușcăturii.








2.	Schema Relațională
 
3.	Tipuri date și constrângeri identitate
Animal_preistoric
Animal_ID, Mediu_ID = int
Specie (doar acesta e unique), ordin, subordin, suprafamilia, familia, subfamilia, gen=nvarchar(50), %niciunul nu permite NULL%


Paleontolog
Paleo_ID=int (primary key)
Nume, prenume=nvarchar(50)
Data_descoperirii=date, default data curenta
Locul_descoperirii=nvarchar(50)
Muzee_posesoare= nvarchar(50)
Mediu
Mediu_ID=int (primary key)
Animal_ID= int(foreign key)
Țară, teren, perioadă_geografică=nvarchar(50)
Rol_ecosistem
Rol_ID = int (primary key)
Animal_ID = int(foreign key)
Prădător, pradă=char(2)
Poziție_lanț_trofic=nvarchar(50)
Dietă=varchar(50)
Biomecanică
Biomec_ID= int (primary key)
Animal_ID =int(foreign key)
forța_mușcăturii, greutate = int
Viteză, anvergură, lungime, înălțime=tinyint



4.	Funcționarea
Backend-ul este construit în Flask, utilizând json, iar Frontend-ul în html și css. Operațiile funcționează pe bază de approute.
5.	Interogări
Interogările simple

1.	Interogare pentru a găsi speciile de dinozauri și perioada geologică în care au trăit:
SELECT a.specie, m.perioada_geologica
FROM Animal_preistoric a
JOIN Mediu m ON a.Mediu_ID = m.Mediu_ID;

2.	Interogare pentru a afla biomecanica fiecărui dinozaur:
SELECT a.specie, b.greutate, b.viteza
FROM Animal_preistoric a
JOIN Biomecanica b ON a.Animal_ID = b.Biomec_ID;

3.	Interogare pentru a găsi informații despre habitatul dinozaurilor:
SELECT a.specie, re.pozitie_lant_trofic, re.`pradator/prada`
FROM Animal_preistoric a
JOIN Rol_ecosistem re ON a.Animal_ID = re.Rol_ID;

4.	Interogare pentru a asocia informații biomecanice cu mediul în care au trăit dinozaurii:
SELECT a.specie, b.lungime, b.inaltime, m.teren
FROM Animal_preistoric a
JOIN Biomecanica b ON a.Animal_ID = b.Biomec_ID
JOIN Mediu m ON a.Mediu_ID = m.Mediu_ID;




5.	Interogare pentru a identifica dinozaurii și paleontologii care i-au descoperit:
SELECT a.specie, p.nume, p.prenume
FROM Animal_preistoric a
JOIN Paleo_Animal pa ON a.Animal_ID = pa.Animal_ID
JOIN Paleontolog p ON pa.Paleo_ID = p.Paleo_ID;


6.	(*Cu parametru variabil*)
 Interogare pentru a găsi dinozauri pe baza unei greutăți minime și maxime:
SELECT a.specie, b.greutate
FROM Animal_preistoric a
JOIN Biomecanica b ON a.Animal_ID = b.Biomec_ID
WHERE b.greutate BETWEEN ? AND ?;

Interogările complexe
•	Interogare pentru a identifica dinozaurii cu o greutate mai mare decât media greutăților înregistrate:
SELECT a.specie
FROM Animal_preistoric a
JOIN Biomecanica b ON a.Animal_ID = b.Biomec_ID
WHERE b.greutate > (
    SELECT AVG(greutate)
    FROM Biomecanica);




•	Interogare pentru a afla dinozaurii descoperiți într-o anumită perioadă de timp:

SELECT a.specie
FROM Animal_preistoric a
JOIN Paleo_Animal pa ON a.Animal_ID = pa.Animal_ID
JOIN Paleontolog p ON pa.Paleo_ID = p.Paleo_ID
WHERE p.data_descoperirii BETWEEN '1800-01-01' AND '1900-12-31';

•	Interogare pentru a găsi dinozaurii care trăiesc în aceeași țară ca un anumit dinozaur specificat:
SELECT a.specie
FROM Animal_preistoric a
WHERE a.Mediu_ID IN (
    SELECT m.Mediu_ID
    FROM Mediu m
    JOIN Animal_preistoric ap ON m.Mediu_ID = ap.Mediu_ID
    WHERE ap.specie = 'Rex'
);

•	(*Cu parametru variabil*)
Interogare pentru a găsi dinozaurii care au o anumită lungime:
SELECT a.specie
FROM Animal_preistoric a
JOIN Biomecanica b ON a.Animal_ID = b.Biomec_ID
WHERE b.lungime = ?;





