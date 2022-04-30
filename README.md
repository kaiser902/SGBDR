<!-- #######  YAY, I AM THE SOURCE EDITOR! #########-->
<p>--Q1<br />CREATE DATABASE inscription<br />ON Primary<br />(<br />Name = bd_inscription_data,<br />filename = 'G:\cours\bd_inscription_data.mdf',<br />size = 4 MB,<br />Maxsize = 6MB,<br />Filegrowth = 2MB<br />)<br />log on<br />(<br />Name = bd_inscription_log,<br />filename = 'G:\cours\bd_inscription_data.ldf',<br />size = 4 MB,<br />Maxsize = 6MB,<br />Filegrowth = 2MB<br />)</p>
<p>--Q2<br />EXEC sp_renamedb 'inscription','inscriptions'<br />--Q3<br />CREATE TABLE LYCEE<br />(<br />NumLycee int primary key identity(1,1),<br />NomLycee nvarchar(50) not null,<br />AdrLycee nvarchar(50)<br />)<br />CREATE TABLE BAC<br />(<br />CodeBac nvarchar(10) primary key,<br />IntituleBac nvarchar(50) not null<br />)</p>
<p>CREATE TABLE LANGUE<br />(<br />CodeLangue nvarchar(10) primary key,<br />LibelleLangue nvarchar(50) not null<br />)</p>
<p>CREATE TABLE Etudiant<br />(<br />CIN nvarchar(50) Primary Key,<br />NomEleve nvarchar(50) not null,<br />PrenomEleve nvarchar(50) not null,<br />DateNais Datetime not null,<br />AdresseEleve varchar(50),<br />NumLycee int foreign key references LYCEE(NumLycee) not null,<br />CodeBac nvarchar(10) foreign key references BAC(CodeBac) not null,<br />CodeLangue nvarchar(10) foreign key references LANGUE(CodeLangue) not null<br />)<br />CREATE TABLE DOSSIER<br />(<br />NumDossier int primary key identity(1,1),<br />DateArrivee Datetime not null default getdate(),<br />DateReponse DateTime,<br />EtatDossier nvarchar(150) check(EtatDossier in ('En Cours de traitement','complet','incomplet')),<br />Decision nvarchar(150) check(Decision in ('Admis En liste Principal','Admis','Refuser')), <br />CinEtudiant nvarchar(50) foreign key references Etudiant(CIN),<br />observation nvarchar(100)<br />)<br />--Q4<br />EXEC sp_rename 'LYCEE','lycees'<br />EXEC sp_rename 'LANGUE','langues'<br />EXEC sp_rename 'DOSSIER','dossiers'<br />EXEC sp_rename 'ETUDIANT','etudiants'<br />--Q5<br />EXEC sp_rename 'etudiants.DateNais','DateNaissance','column'<br />--Q6<br />alter table dossier<br />add Rang varchar(2) check(Rang like '[ABC],[1-9]')<br />--Q7<br />alter table ETUDIANT <br />ADD CONSTRAINT cns_dateAccepte check ((datediff(day,DateNaissance,'15/09/'+convert(nvarchar(20),(year(getdate()))))&gt;=8401))<br />--Q8<br />alter table etudiants<br />alter column AdresseEleve nvarchar(150)<br />--Q9<br />insert into Lycees(NomLycee,AdrLycee) values ('SEFROU','199 rue ben seffar')<br />insert into Lycees(NomLycee,AdrLycee) values ('Fes','199 lot. Ratibiya')</p>
<p>insert into BAC(CodeBac,IntituleBac) values ('MA123456','SVT')<br />insert into BAC(CodeBac,IntituleBac) values ('MA123456789','pc')</p>
<p>insert into langues(CodeLangue,LibelleLangue) values ('MA1','Math')<br />insert into langues(CodeLangue,LibelleLangue) values ('MA2','Physic')</p>
<p>insert into etudiants(NomEleve,PrenomEleve,DateNaissance,AdresseEleve,NumLycee,CodeBac,CodeLangue) values ('ANAS','AIT HAMMOU','21-09-1994','199 rue ben seffar',1,'MA123456','MA1')</p>
<p>-- Q10<br />ALTER TABLE dossiers<br />DROP column Observation <br />--Q11<br />ALTER TABLE dossiers<br />ADD DEFAULT ' En cours de traitement ' FOR EtatDossier<br />--12<br />update DOSSIER<br />set Decision='refuser'<br />where EtatDossier='incomplet'<br />--Q13<br />Select NumDossier, CIN <br />from etudiants e join dossiers d on e.cin = d.CinEtudiant<br />where d.cinEtudiant = E.CIN and d.decision='refuser'<br />--Q14<br />select [NomEleve],[PrenomEleve] from etudiants e join dossiers d on e.cin = d.CinEtudiant<br />where DATEDIFF(day,d.DateArrivee,getdate()) &lt;= 7<br />--Q15<br />select * from etudiants e inner join BAC b on e.CodeBac = b.CodeBac<br />inner join langue l on e.CodeLangue = l.CodeLangue<br />inner join lycees ly on e.NumLycee = ly.NumLycee<br />where ly.NomLycee = 'Lalla salma' and b.IntituleBac = 'Electrotechnique' and l.LibelleLangue = 'Francais'<br />--Q16<br />select min(DATEDIFF(year,DateNaissance,GETDATE())) from etudiants e join lycees ly on e.NumLycee = ly.NumLycee<br />where ly.NomLycee = 'LAHCEN LYOUSSI'<br />--Q17<br />select *<br />delete from Etudiants<br />where NumLycee = (select Numlycee where Nomlycee= 'bir nazaran')<br />--Q19<br />select *<br />from DOSSIER <br />where DATENAME( MONTH,month(DateArrivee)) = 'octobre' and (year(DateArrivee)=2019)<br />--Q18<br />select *,ly.NomLycee as[Nom De Lycee] from etudiants e join lycees ly on e.NumLycee = ly.NumLycee<br />where ly.AdrLycee like null<br />--Q20<br />select * from etudiants<br />where (DATEDIFF(year,DateNaissance,getdate()) between 18 and 22 )</p>
<p>--Q21</p>
<p>--Q23<br />select count(*) from BAC<br />--Q24<br />select count(*) from BAC b inner join etudiants e on e.CodeBac = b.CodeBac<br />inner join dossiers d on d.CinEtudiant = e.CIN<br />where Decision in ('En Cours de traitement','complet','incomplet')<br />--Q25<br />select * from etudiants e join lycees ly on e.NumLycee = ly.NumLycee<br />where ly.NomLycee = 'LALLA SALMA' and (DATEDIFF(year,DateNaissance,getdate())&gt;All(select * from etudiants where ly.NomLycee='Lahcen LYOUSSI')</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
