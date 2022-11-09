# SQL
###

## TP1

### 1
```
select libserv 
from service 
order by libserv;
```
### 2
```
select e.nom, e.prenom 
from employe e, fonction f
where e.numfonct = f.numfonct 
and f.libfonct = 'Chef de Projet'; 
```
### 3
```
select libfonct 
from fonction f
where f.salaire = (select max(f1.salaire) from fonction f1);
```
### 4
```
select nom, prenom 
from employe e 
where e.numfonct = (select e1.numfonct from employe e1 
		    where e1.nom = 'Durant' 
		    and e1.prenom = 'Pierre');
```
### 5
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
### 6
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
### 7
```
select libserv 
from service s
where not exists (select null from employe e, fonction f 
		  where f.numfonct = e.numfonct 
		  and f.libfonct = 'Directeur' 
		  and s.numserv = e.numserv);
```
### 8
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
### 9
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
### 10
```
select s.libserv 
from service s, employe e
where s.numserv = e.numserv
group by s.libserv
having count(*) = (select max(count(*)) 
		   from employe e1 
		   group by e1.numserv);
```
### 11
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
### 12
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
### 13
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
### 14
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
### 15
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

### 1
```
select nom, prenom 
from personne
order by nom, prenom;
```
### 2
```
select s.libelle, c.nblit
from chambre c
inner join service s on c.nuserv = s.nuserv
where c.nuch = 11;
```
### 3
```
select nuch
from hospitalisation
where nbjour = (select max(nbjour)
            	from hospitalisation);
```
### 4
```
select p.nom, p.prenom
from personne p
inner join hospitalisation h on p.nupers = h.nupers
where h.datedeb = '09/01/2022';
```
### 5
```
select s.libelle
from service s
inner join chambre c on s.nuserv = c.nuserv
inner join hospitalisation h on c.nuch = h.nuch
where h.datedeb = (select max (h1.datedeb) 
		   from hospitalisation h1);
```
### 6
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
### 7
```
select avg(h.nbjour)
from hospitalisation h
inner join personne p on p.nupers = h.nupers
where p.nom = 'MOULY'
and p.prenom = 'Florent';
```
### 8
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
### 9
```
select s.libelle
from service s
inner join chambre c on s.nuserv = c.nuserv
group by c.nuserv, s.libelle
having sum(c.nblit) = (select min(sum(c1.nblit))
                       from chambre c1
                       group by c1.nuserv);
```
### 10
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
### 11
```
select p.nom, p.prenom, p.sexe, sum(h.nbjour)
from personne p 
left join hospitalisation h on p.nupers = h.nupers
group by p.nom, p.prenom, p.sexe
order by p.sexe, p.nom;
```
### 12
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
### 13
```
select p.nom, p.prenom, s.libelle, round(avg(h.nbjour), 2)
from personne p
inner join hospitalisation h on p.nupers = h.nupers
inner join chambre c on c.nuch = h.nuch
inner join service s on s.nuserv = c.nuserv
where p.sexe = 'F'
group by p.nom, p.prenom, s.libelle;
```
### 14
```
select p.nom, p.prenom, h.nuch
from personne p
inner join hospitalisation h on p.nupers = h.nupers
where '12/01/2021' between h.datedeb AND (h.datedeb + nbjour);
```
