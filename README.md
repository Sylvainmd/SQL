# SQL
###

## TP1 

-- 1
```
select nom, prenom 
from personne
order by nom, prenom;
```
-- 2
```
select s.libelle, c.nblit
from chambre c
inner join service s on c.nuserv = s.nuserv
where c.nuch = 11;
```
-- 3
```
select nuch
from hospitalisation
where nbjour = (select max(nbjour)
            	  from hospitalisation);
```
-- 4
```
select p.nom, p.prenom
from personne p
inner join hospitalisation h on p.nupers = h.nupers
where h.datedeb = '09/01/2022';
```
-- 5
```
select s.libelle
from service s
inner join chambre c on s.nuserv = c.nuserv
inner join hospitalisation h on c.nuch = h.nuch
where h.datedeb = (select max (h1.datedeb) from hospitalisation h1);
```
-- 6
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
-- 7
```
select avg(h.nbjour)
from hospitalisation h
inner join personne p on p.nupers = h.nupers
where p.nom = 'MOULY'
and p.prenom = 'Florent';
```
-- 8
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
-- 9
```
select s.libelle
from service s
inner join chambre c on s.nuserv = c.nuserv
group by c.nuserv, s.libelle
having sum(c.nblit) = (select min(sum(c1.nblit))
                             from chambre c1
                             group by c1.nuserv);
```
--10
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
--ou 
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
--11
```
select p.nom, p.prenom, p.sexe, sum(h.nbjour)
from personne p left join hospitalisation h on p.nupers = h.nupers
group by p.nom, p.prenom, p.sexe
order by p.sexe, p.nom;
```
--12
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
--ou
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
--ou
```
select c.nuch
from chambre c left join service s on s.nuserv = c.nuserv and s.libelle = 'Dermatologie'
left join hospitalisation h on c.nuch = h.nuch
where h.nupers is null and s.libelle is not null
order by 1;
```
-- 13
```
select p.nom, p.prenom, s.libelle, round(avg(h.nbjour), 2)
from personne p
inner join hospitalisation h on p.nupers = h.nupers
inner join chambre c on c.nuch = h.nuch
inner join service s on s.nuserv = c.nuserv
where p.sexe = 'F'
group by p.nom, p.prenom, s.libelle;
```
-- 14
```
select p.nom, p.prenom, h.nuch
from personne p
inner join hospitalisation h on p.nupers = h.nupers
where '12/01/2021' between h.datedeb AND (h.datedeb + nbjour);
```
