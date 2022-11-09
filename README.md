# SQL


## TP1

### Schéma relationnel de la base :
FONCTION (Numfonct, libfonct, salaire)<br/>
SERVICE	(Numserv, libserv)<br/>
EMPLOYE	(Numemp, nom, prenom, numfonct, numserv)<br/>

### 1.	Afficher par ordre alphabétique les services de l’entreprise.
```
select libserv 
from service 
order by libserv;
```
### 2.	Afficher le nom et prénom des personnes qui sont « Chef de Projet ».
```
select e.nom, e.prenom 
from employe e, fonction f
where e.numfonct = f.numfonct 
and f.libfonct = 'Chef de Projet'; 
```
### 3.	Afficher le libellé des fonctions qui ont le plus haut salaire.
```
select libfonct 
from fonction f
where f.salaire = (select max(f1.salaire) from fonction f1);
```
### 4.	Afficher les employés qui ont la même fonction que Durant, Pierre.
```
select nom, prenom 
from employe e 
where e.numfonct = (select e1.numfonct from employe e1 
		    where e1.nom = 'Durant' 
		    and e1.prenom = 'Pierre');
```
### 5.	Afficher le libellé des services qui n’ont aucun employé.
```
select libserv 
from service s
where not exists (select null 
		  from employe e 
		  where e.numserv = s.numserv);
```
#### ou
```
select libserv 
from service s
where s.numserv not in (select e.numserv 
			from employe e 
			where e.numserv = s.numserv);
```
#### ou
```
select libserv 
from service s, employe e 
where e.numserv(+) = s.numserv
and e.numserv is null;
```
#### ou
```
select libserv 
from service s 
left join employe e on e.numserv = s.numserv
where e.numserv is null;
```
### 6.	Afficher les libellés des services qui ont des fonctions d’analyste et des fonctions de directeurs.
```
select libserv 
from service s , employe e, fonction f
where e.numserv = s.numserv 
and e.numfonct = f.numfonct 
and f.libfonct = 'Analyste'
INTERSECT 
select libserv 
from service s , employe e, fonction f 
where e.numserv = s.numserv 
and e.numfonct = f.numfonct 
and f.libfonct = 'Directeur';
```
### 7.	Afficher le service qui n’a aucun directeur.
```
select libserv 
from service s
where not exists (select null from employe e, fonction f 
		  where f.numfonct = e.numfonct 
		  and f.libfonct = 'Directeur' 
		  and s.numserv = e.numserv);
```
### 8.	Afficher par fonction, le nombre d’employés. Assurez-vous d’afficher toutes les fonctions.
```
select f.libfonct, (select count(*) 
		    from employe e 
		    where e.numfonct = f.numfonct)
from fonction f;
```
#### ou
```
select f.libfonct, count(e.numemp)
from employe e, fonction f 
where e.numfonct(+) = f.numfonct
group by f.libfonct;
```
### 9.	Afficher pour chaque service, le nombre d’employés. Classez le résultat par ordre décroissant du nombre d’employé.
```
select s.libserv, (select count(*) 
		   from employe e 
		   where e.numserv = s.numserv) nb 
from service s
order by nb desc;
```
#### ou
```
select s.libserv, count(e.numemp) nb
from employe e, service s
where e.numserv(+) = s.numserv
group by s.libserv
order by nb desc;
```
### 10.	Afficher le libelle du service qui a le plus grand nombre d’employés.
```
select s.libserv 
from service s, employe e
where s.numserv = e.numserv
group by s.libserv
having count(*) = (select max(count(*)) 
		   from employe e1 
		   group by e1.numserv);
```
### 11.	Afficher la liste des services qui ont plus d’employés que la moyenne.
```
select s.libserv 
from service s, employe e
where s.numserv = e.numserv
group by s.libserv
having count(*) > (select avg(count(e1.numserv)) 
		   from employe e1, service s1 
		   where s1.numserv = e1.numserv(+) 
		   group by s1.numserv);
```
### 12.	Afficher, pour chaque service, l’employé qui a le salaire le plus élevé.
```
select s.libserv, e.nom, e.prenom, f.salaire
from employe e, service s, fonction f
where e.numserv = s.numserv 
and e.numfonct = f.numfonct
and (e.numserv, f.salaire) in (select e1.numserv, max(f1.salaire) 
                               from employe e1, fonction f1 
                               where e1.numfonct = f1.numfonct 
			       group by e1.numserv);
```
#### ou
```
select s.libserv, e.nom, e.prenom, f.salaire
from employe e, service s, fonction f
where e.numserv = s.numserv 
and e.numfonct = f.numfonct
and f.salaire = (select max (f1.salaire) 
		 from employe e1, fonction f1 
		 where e1.numfonct = f1.numfonct 
		 and e1.numserv = s.numserv);
```
### 13.	Afficher, pour chaque service, la fonction la plus représentée.
```
select s.libserv, f.libfonct, count(*)
from employe e, service s, fonction f
where e.numserv = s.numserv
and e.numfonct = f.numfonct
group by s.libserv, f.libfonct, e.numserv
having count(*) = (select max(count(*)) 
		   from employe e1 
		   where e1.numserv = e.numserv 
		   group by e1.numfonct)
order by s.libserv;
```
### 14.	Afficher, pour chacune des fonctions, le service qui a le plus grand nombre d’employés dans cette fonction.
```
select f.libfonct, s.libserv,  count(*)
from employe e, service s, fonction f
where e.numserv = s.numserv
and e.numfonct = f.numfonct
group by s.libserv, f.libfonct, e.numfonct
having count(*) = (select max(count(*)) 
		   from employe e1 
		   where e1.numfonct = e.numfonct 
		   group by e1.numserv)
order by f.libfonct;
```
### 15.	Afficher le service dont la masse salariale est la plus grande.
```
select libserv, sum(salaire)
from employe e, service s, fonction f 
where e.numserv = s.numserv 
and e.numfonct = f.numfonct
group by libserv
having sum(salaire) = (select max(sum(salaire)) 
		       from employe e1, fonction f1
		       where e1.numfonct = f1.numfonct
		       group by e1.numserv)
```
###

## TP2 

### Schémas relationnel de la base :
PERSONNE (Nupers, nom, prenom, sexe)<br/>
SERVICE (Nuserv,libelle)<br/>
CHAMBRE (Nuch, nuserv, nblit)<br/>
HOSPITALISATION (Nupers, Datedeb, nuch, nbjour)<br/>

### 1  Afficher les nom et prénom des personnes, par ordre alphabétique du nom puis du prénom.
```
select nom, prenom 
from personne
order by nom, prenom;
```
### 2 Afficher le libellé du service et le nombre de lits de la chambre 11.
```
select s.libelle, c.nblit
from chambre c
inner join service s on c.nuserv = s.nuserv
where c.nuch = 11;
```
### 3 Afficher le(s) numéro(s) de la (des) chambre(s) de l’hospitalisation la plus longue.
```
select nuch
from hospitalisation
where nbjour = (select max(nbjour)
            	from hospitalisation);
```
### 4 Afficher les nom et prénom de la (des) personne(s) hospitalisée(s) à partir du 09/01/2022.
```
select p.nom, p.prenom
from personne p
inner join hospitalisation h on p.nupers = h.nupers
where h.datedeb = '09/01/2022';
```
### 5  Afficher le libellé du service de l’hospitalisation la plus récente (date de début la plus grande).
```
select s.libelle
from service s
inner join chambre c on s.nuserv = c.nuserv
inner join hospitalisation h on c.nuch = h.nuch
where h.datedeb = (select max (h1.datedeb) 
		   from hospitalisation h1);
```
### 6 Afficher la date de fin d’hospitalisation du séjour le plus long de LIENHART Jo (date de fin = date de début + nbjour).
```
select h.datedeb + h.nbjour,h.nbjour
from hospitalisation h
inner join personne p on p.nupers = h.nupers
where p.nom = 'LIENHART'
and p.prenom = 'Jo'
and nbjour = (select max(h1.nbjour)
              from hospitalisation h1
              inner join personne p1 on p1.nupers = h1.nupers
              where p.nupers = p1.nupers);
```
### 7 Afficher le nombre de jours moyen des hospitalisations de MOULY Florent.
```
select avg(h.nbjour)
from hospitalisation h
inner join personne p on p.nupers = h.nupers
where p.nom = 'MOULY'
and p.prenom = 'Florent';
```
### 8 Afficher les nom et prénom de la (des) personne(s) de sexe M qui a (ont) été hospitalisée(s) le plus grand nombre de fois.
```
select distinct p.nom, p.prenom
from personne p
inner join hospitalisation h on p.nupers = h.nupers
where p.sexe = 'M'
group by p.nupers, p.nom, p.prenom
having count(*) = (select max(count(*))
		   from personne p1
		   inner join hospitalisation h1 on p1.nupers = h1.nupers
		   where p1.sexe = 'M'
		   group by h1.nupers);
                              
```
### 9 Afficher le libellé du (des) service(s) ayant le plus petit nombre de lits.
```
select s.libelle
from service s
inner join chambre c on s.nuserv = c.nuserv
group by c.nuserv, s.libelle
having sum(c.nblit) = (select min(sum(c1.nblit))
                       from chambre c1
                       group by c1.nuserv);
```
### 10 Afficher les libellés des services dans lesquels ont été hospitalisés DUMA Luc et BETS Philippe.
```
select s.libelle
from service s
inner join chambre c on s.nuserv = c.nuserv
inner join hospitalisation h on c.nuch = h.nuch
inner join personne p on p.nupers = h.nupers
where p.nom = 'DUMA'
and p.prenom = 'Luc'

intersect

select s1.libelle
from service s1
inner join chambre c1 on s1.nuserv = c1.nuserv
inner join hospitalisation h1 on c1.nuch = h1.nuch
inner join personne p1 on p1.nupers = h1.nupers
where p1.nom = 'BETS'
and p1.prenom = 'Philippe';
```
#### ou 
```
select s.libelle from service s 
where s.nuserv in (select c.nuserv from 
		   chambre c
		   inner join hospitalisation h on c.nuch = h.nuch
		   inner join personne p on p.nupers = h.nupers
		   where p.nom = 'DUMA'
		   and p.prenom = 'Luc')
and s.nuserv in (select c1.nuserv from 
		 chambre c1
		 inner join hospitalisation h1 on c1.nuch = h1.nuch
		 inner join personne p1 on p1.nupers = h1.nupers
		 where p1.nom = 'BETS'
		 and p1.prenom = 'Philippe');
```
### 11 Afficher pour chaque personne (nom, prénom), la durée totale de ses hospitalisations. Vous afficherez toutes les personnes, triées par sexe puis nom croissant.
```
select p.nom, p.prenom, p.sexe, sum(h.nbjour)
from personne p 
left join hospitalisation h on p.nupers = h.nupers
group by p.nom, p.prenom, p.sexe
order by p.sexe, p.nom;
```
### 12 Afficher le numéro de la chambre du service Dermatologie qui n’a jamais été utilisée.
```
select c.nuch
from chambre c
inner join service s on s.nuserv = c.nuserv
where s.libelle = 'Dermatologie'
and c.nuch not in (select h1.nuch
                   from hospitalisation h1
                   inner join chambre c1 on c1.nuch = h1.nuch
                   inner join service s1 on s1.nuserv = c1.nuserv
                   where s1.libelle = 'Dermatologie');
```
#### ou
```
select c.nuch
from chambre c
inner join service s on s.nuserv = c.nuserv
where s.libelle = 'Dermatologie'

minus

select h1.nuch
from hospitalisation h1
inner join chambre c1 on c1.nuch = h1.nuch
inner join service s1 on s1.nuserv = c1.nuserv
where s1.libelle = 'Dermatologie';
```
#### ou
```
select c.nuch
from chambre c 
left join service s on s.nuserv = c.nuserv 
and s.libelle = 'Dermatologie'
left join hospitalisation h on c.nuch = h.nuch
where h.nupers is null 
and s.libelle is not null
order by 1;
```
### 13 Afficher pour chaque personne de sexe F, la moyenne des durées d’hospitalisation par service.
```
select p.nom, p.prenom, s.libelle, round(avg(h.nbjour), 2)
from personne p
inner join hospitalisation h on p.nupers = h.nupers
inner join chambre c on c.nuch = h.nuch
inner join service s on s.nuserv = c.nuserv
where p.sexe = 'F'
group by p.nom, p.prenom, s.libelle;
```
### 14 Afficher la liste des personnes et leur numéro de chambre, en cours d’hospitalisation le 12/01/2022
```
select p.nom, p.prenom, h.nuch
from personne p
inner join hospitalisation h on p.nupers = h.nupers
where '12/01/2021' between h.datedeb AND (h.datedeb + nbjour);
```
###

## TP3

### Schémas relationnel de la base :
PERSONNES (Nupers, nom, prenom, age)<br/>
AVION (Nuavi, typeavi, nbpersmaxi)<br/>
VOL (Nuvol, nuavi, datevol, villedep, villearr, distance)<br/>
VOYAGE (Nuvol, Nupers)<br/>

### 1 Afficher le nom et prénom des personnes, par ordre alphabétique décroissant du nom
```
SELECT p.nom, p.prenom 
FROM personnes p 
ORDER BY p.nom DESC;
```
### 2 Afficher le type de l’avion et le nombre de passagers maximum de l’avion n°4.
```
SELECT a.typeavi, a.nbpersmaxi
FROM avion a
WHERE a.nuavi = 2;
```
### 3 Afficher le type de l’avion qui réalise le vol le plus court
```
SELECT a.typeavi 
FROM avion a INNER JOIN vol v ON a.nuavi = v.nuavi
WHERE v.distance = (SELECT MIN(v1.distance) FROM vol v1);
```
### 4 Afficher le nom et prénom des personnes présentes sur le vol du 16/08/2022
```
SELECT p.nom, p.prenom 
FROM personnes p INNER JOIN voyage v ON v.nupers = p.nupers 
JOIN vol vo ON v.nuvol = vo.nuvol
WHERE vo.datevol = '16/08/2022';
```
### 5 Afficher la date du vol le plus court de l’avion de type F-Jet.
```
SELECT v1.datevol
FROM vol v INNER JOIN AVION a on a.nuavi = v.nuavi 
and a.typeavi = 'F-Jet'
AND v.distance = (SELECT MIN(v1.distance) 
				  FROM vol v1 INNER JOIN avion a1 ON v1.nuavi = a1.nuavi
				  WHERE a1.typeavi = 'F-Jet');
```
 
### 6 Afficher la distance moyenne des vols de l’avion de type A330
```
SELECT AVG(v.distance) 
FROM vol v INNER JOIN avion a ON v.nuavi = a.nuavi
WHERE a.typeavi = 'A330';
```
### 7 Afficher les nom et prénom de la (des) personne(s) de plus de 50 ans qui a (ont) fait le plus grand nombre de vols.
```
SELECT p.nom, p.prenom 
FROM personnes p INNER JOIN voyage v ON v.nupers = p.nupers
where p.age > 50
GROUP BY p.nom, p.prenom
HAVING COUNT(*) = (SELECT MAX(COUNT(*)) FROM voyage v1 INNER JOIN personnes p1 ON v1.nupers = p1.nupers
			where p1.age > 50
			GROUP BY v1.nupers );
```

### 8 Afficher le type de l’avion qui a parcours la plus grande distance totale.

```
SELECT a.typeavi 
FROM avion a INNER JOIN vol v ON a.nuavi = v.nuavi
GROUP BY a.typeavi
HAVING SUM(v.distance) = (SELECT max(SUM(v1.distance)) FROM vol v1
                          GROUP BY v1.nuavi);
```
### 9 Afficher le(s) type(s) de l’avion sur lequel (lesquels) VOGEL Jean et PONSONNET Pierre ont voyagé
```
SELECT a.typeavi 
FROM avion a INNER JOIN vol v ON a.nuavi = v.nuavi 
INNER JOIN voyage vo ON v.nuvol = vo.nuvol
JOIN personnes p ON vo.nupers = p.nupers
WHERE p.nom = 'VOGEL' AND p.prenom = 'Jean'
INTERSECT 
SELECT a.typeavi 
FROM avion a INNER JOIN vol v ON a.nuavi = v.nuavi 
INNER JOIN voyage vo ON v.nuvol = vo.nuvol
JOIN personnes p ON vo.nupers = p.nupers
WHERE p.nom = 'PONSONNET' AND p.prenom = 'Pierre';
```

### 10 Afficher le type de l’avion qui a fait le vol le plus long avec la personne la plus âgé à son bord.
```
select a.typeavi from personnes p 
inner join voyage v on v.nupers = p.nupers 
inner join vol vo ON v.nuvol = vo.nuvol 
inner join avion a on vo.nuavi = a.nuavi
where p.age = (select max (p1.age) from personnes p1)
and vo.distance = (select max(vo1.distance) from voyage v1 
			inner join vol vo1 ON v1.nuvol = vo1.nuvol 
			where v1.nupers = p.nupers);
```
					  
### 11 Afficher pour chaque personne (nom, prénom), la distance totale de leurs vols. Vous afficherez toutes les personnes triées par âge décroissant.
```
SELECT p.nom, p.prenom, SUM(vo.distance) 
FROM personnes p LEFT JOIN voyage v ON v.nupers = p.nupers
LEFT JOIN vol vo ON v.nuvol = vo.nuvol
GROUP BY p.nom, p.prenom;
```

### 12 Afficher le nom et prénom des personnes de moins de 60 ans qui n’ont pas fait de voyage sur l’avion de type Falcon.
```
SELECT p.nom, p.prenom 
FROM personnes p 
WHERE nupers NOT IN (SELECT nupers 
                      FROM voyage v INNER JOIN vol vo ON v.nuvol = vo.nuvol 
                      INNER JOIN avion a ON vo.nuavi = a.nuavi
                      WHERE a.typeavi = 'Falcon');
```
#### ou
```
SELECT p.nom, p.prenom 
FROM personnes p 
WHERE NOT exists (SELECT * 
                      FROM voyage v INNER JOIN vol vo ON v.nuvol = vo.nuvol 
                      INNER JOIN avion a ON vo.nuavi = a.nuavi
                      and p.nupers = v.nupers
                      WHERE a.typeavi = 'Falcon');
```
#### ou
```
SELECT p.nom, p.prenom 
FROM personnes p 
minus 
SELECT p1.nom, p1.prenom 
FROM personnes p1 INNER JOIN voyage v ON p1.nupers = v.nupers INNER JOIN vol vo ON v.nuvol = vo.nuvol 
                      INNER JOIN avion a ON vo.nuavi = a.nuavi
                      and p.nupers = v.nupers
                      WHERE a.typeavi = 'Falcon';
```
#### 13 Afficher l’âge moyen des personnes par avion, pour les passagers des vols de plus de 500km.

```
select a.typeavi, avg(p.age)
from personne p 
INNER JOIN voyage v ON p1.nupers = v.nupers 
INNER JOIN vol vo ON v.nuvol = vo.nuvol 
INNER JOIN avion a ON vo.nuavi = a.nuavi
WHERE vo.distance > 500
group by a.typeavi;
```

### 14 Afficher la liste des personnes qui ont pris un vol au départ de Paris et un vol au départ de Lyon au mois de juin 2022
```
SELECT p.nom, p.prenom 
FROM personnes p INNER JOIN voyage v ON v.nupers = p.nupers 
JOIN vol vo ON v.nuvol = vo.nuvol
WHERE vo.villedep = 'Paris' AND vo.datevol BETWEEN '01/06/2019' AND '30/06/2022'
INTERSECT SELECT nom,prenom 
FROM personnes p INNER JOIN voyage v ON v.nupers = p.nupers 
JOIN vol vo ON v.nuvol = vo.nuvol
WHERE vo.villedep = 'Lyon' AND vo.datevol BETWEEN '01/06/2019' AND '30/06/2022';
```
###

## TP4 !!! Certaines requêtes sont susceptible de ne pas fonctionner !!!
ps: merci Léo pour la correction

### Schéma relationnel de la base :
CLIENT (Numcli, nom, prenom)<br/>
PRODUIT (Numprod, libelle, prix)<br/>
FACTURE (Numfact, Numcli, datefact)<br/>
LIGNEFACTURE (Numfact, Numprod, qte)<br/>

### 1 Afficher, par ordre alphabétique du nom puis du prénom, le nom et le prénom des clients
```
select nom,prenom from client
order by nom,prenom;
```

### 2 Afficher le libelle du produit le plus cher
```
select libelle from produit
where prix=(select max(prix) 
	    from produit);
```

### 3 Afficher pour chaque client (Nom, prénom), le nombre de facture. On affichera tous les clients
```
select nom,prenom,count(numfact) 
from client c,facture f
where c.numcli=f.numcli(+)
group by nom,prenom;
```

### 4 Afficher pour chaque facture, le montant total
```
select numfact,(sum(prix)) 
from lignefacture lf,produit p
where lf.numprod=p.numprod
group by numfact;
```

### 5 Afficher le nom et prénom des clients qui n’ont pas de facture.
```
select nom,prenom 
from client c,facture f
where c.numcli=f.numcli(+)
and c.numcli not in (select c.numcli 
		     from client c, facture f
		     where c.numcli=f.numcli);
```

### 6 Afficher le libelle des produits communs à la facture n°3 et n°4
```
select libelle 
from produit p,lignefacture lf
where p.numprod=lf.numprod
and numfact=3

intersect

select libelle 
from produit p,lignefacture lf
where p.numprod=lf.numprod
and numfact=4;

```

### 7 Afficher le client (Nom, prénom), qui a acheté tous les produits dans la même facture.
```
select nom,prenom,count(*) 
from client c,facture f,lignefacture lf
where c.numcli=f.numcli 
and f.numfact=lf.numfact
group by nom,prenom,f.numfact
having count(*)=(select count(*) 
		 from produit);
```

### 8 Afficher le libelle du produit qui est sur toutes les factures.
```
select libelle,count(*) 
from produit p,lignefacture lf
where p.numprod=lf.numprod
group by libelle
having count(*)=(select count(*) 
		 from facture);
```

### 9 Afficher le libelle du produit dont la quantité achetée est la plus grande. (Toutes les factures confondues)
```
select libelle,sum(qte) 
from produit p,lignefacture lf
where p.numprod=lf.numprod
group by libelle
having sum(qte)=(select (max(sum(qte))) 
		 from lignefacture 
		 group by numprod);
```

### 10 Afficher pour chaque facture, le libelle du produit le plus acheté.
```
select numfact,libelle 
from lignefacture lf,produit p
where lf.numprod=p.numprod
and qte=(select max(qte) 
	 from lignefacture lf1
	 where lf.numfact=lf1.numfact);
```
 
### 11 Afficher le nom et prénom du client qui a la facture la plus chère
```
select nom,prenom 
from client c,facture f,lignefacture lf,produit p
where c.numcli=f.numcli 
and f.numfact=lf.numfact 
and p.numprod=lf.numprod
group by nom,prenom,f.numfact
having sum(qte*prix)=(select max(sum(qte*prix)) 
		      from lignefacture lf,produit p
		      where p.numprod=lf.numprod 
		      group by numfact);
```

### 12  Afficher pour chaque client, le numéro de la facture la moins chère.

```
select nom,prenom,f.numfact 
from client c,facture f,lignefacture lf,produit p
where c.numcli=f.numcli 
and f.numfact=lf.numfact 
and lf.numprod=p.numprod
group by nom,prenom,f.numfact,c.numcli
having sum(qte*prix)=(select min(sum(qte*prix)) 
		      from lignefacture lf1,produit p1,client c1,facture f1
		      where lf1.numfact=f1.numfact 
		      and f1.numcli=c1.numcli 
		      and lf1.numprod=p1.numprod 
		      and c1.numcli=c.numcli
		      group by f1.numfact,c1.numcli);
```

### 13 Afficher les produits qui ne sont ni sur la facture n°2, ni sur la facture n°3.
```
select libelle 
from produit p
where libelle not in (select libelle 
		      from produit p,lignefacture lf
		      where p.numprod=lf.numprod 
		      and numfact=2)
		      
intersect

select libelle 
from produit p
where libelle not in (select libelle 
		      from produit p,lignefacture lf
		      where p.numprod=lf.numprod 
		      and numfact=3);
```

### 14 Afficher la quantité d’article de chaque facture.
```
select numfact,sum(qte) 
from lignefacture
group by numfact
order by numfact;
```

### 15 Afficher les produits n’ayant pas été vendu le 15/09/2022.
```
select libelle 
from produit

minus

(select libelle 
 from produit p,lignefacture lf,facture f
 where p.numprod=lf.numprod 
 and lf.numfact=f.numfact
 and datefact='15/09/2022');
```

### 16 Afficher les clients qui n’ont pas de facture le 15/09/2022, ni le 17/07/2022.

```
select nom,prenom 
from client

minus

(select nom,prenom 
 from client c,facture f 
 where c.numcli=f.numcli
 and datefact='15/09/2022')
 
intersect
select nom,prenom 
from client

minus

(select nom,prenom 
 from client c,facture f 
 where c.numcli=f.numcli
 and datefact='17/07/2022');
```

### 17 Afficher la quantité du produit le moins cher de la facture la plus la plus chère.
```
select qte 
from lignefacture lf,produit p
where lf.numprod=p.numprod
and prix=(select min(prix) 
	  from produit p,lignefacture lf1
	  where p.numprod=lf1.numprod 
	  and lf.numfact=lf1.numfact)
and numfact in (select numfact 
		from lignefacture lf,produit p
		where lf.numprod=p.numprod
		group by numfact
		having sum(qte*prix)=(select max(sum(qte*prix)) 
				      from lignefacture lf,produit p
				      where p.numprod=lf.numprod 
				      group by numfact));
```

### 18 Afficher les produits qui ne sont pas présent sur la facture la moins chère du client n°4.
 NE FONCTIONNE PAS
```
select libelle 
from produit
where libelle not in (select libelle 
		      from produit p,lignefacture lf,facture f,client c
		      where p.numprod=lf.numprod 
		      and lf.numfact=f.numfact 
		      and f.numcli=c.numcli
		      and c.numcli=4)
group by libelle,lf.numfact
having sum(qte*prix)=(select min(sum(qte*prix)) 
		      from lignefacture lf,produit p, facture f,client c
		      where p.numprod=lf.numprod 
		      and lf.numfact=f.numfact 
		      and f.numcli=c.numcli
		      and c.numcli=4 
		      group by libelle,lf.numfact));
```

### 19 Afficher la quantité moyenne des produits sur la facture ayant la quantité de produit vendu la plus élevée. 
 NE FONCTIONNE PAS
```
select avg(qte) 
from lignefacture lf
group by numfact
having count(*)=(select max(count(*)) 
		 from lignefacture 
		 group by numfact);
```

### 20 Afficher pour chaque facture, la liste des produits qui ne sont pas présent sur celle-ci
 NE FONCTIONNE PEUT ETRE PAS (j'ai pas essayé)
```
select numfact,libelle 
from produit p,lignefacture lf
where p.numprod=lf.numprod
```
