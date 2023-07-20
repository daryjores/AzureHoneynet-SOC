# Azure Honeynet & SOC Project: Cyber Attacks in Real Time
![Cloud Honeynet / SOC]([https://i.imgur.com/7s0rHfn.png](https://www.joshmadakor.tech/cyber/assets/img/project.png))

## Introduction

 Dans ce projet, je simule un honeynet à petite échelle qui attire le trafic réel d'attaquants du monde entier via Microsoft Azure. L'objectif de ce projet est de démontrer les meilleures pratiques de sécurité, les tactiques de réponse aux incidents et les effets du durcissement d'un environnement. Nous y parviendrons en déployant intentionnellement des machines virtuelles qui n'ont aucune protection contre l'internet public afin d'attirer des hackers dans notre environnement. Ensuite, après l'ingestion de certaines sources de journaux dans Log Analytics Workspace, Microsoft Sentinel interviendra pour créer des cartes d'attaques, des alertes et des incidents. Afin de présenter les mesures avant et après le durcissement de l'environnement sur la base des incidents générés à partir de la capture de 24 heures. 

## Composants Azure utilisés

- Azure Virtual Network (VNet)
- Azure Network Security Group (NSG)
- Virtual Machines (2x Windows, 1x Linux)
- Log Analytics Workspace with Kusto Query Language (KQL) Queries
- Azure Key Vault 
- Azure Storage Account 
- Microsoft Sentinel 
- Microsoft Defender for Cloud 


## Architecture Before Hardening
![Architecture Diagram](https://i.imgur.com/c2E0AtK.png)

 - Dans la phase "AVANT" du projet, toutes les ressources ont été initialement déployées dans l'espoir que l'attraction serait obtenue à partir de l'internet public. Les machines virtuelles avaient leurs groupes de sécurité réseau (NSG) et leurs pare-feu intégrés grands ouverts, permettant un accès illimité à partir de n'importe quelle source. En outre, toutes les autres ressources, telles que les comptes de stockage et les bases de données, ont été déployées avec des points d'extrémité publics visibles sur l'internet, sans utiliser de points d'extrémité privés pour plus de sécurité. Ces machines ont ensuite été laissées à la disposition du public pendant 24 heures afin de générer les cartes d'attaques mentionnées précédemment. 
 <br />
 
 <b>Cette carte des attaques montre le nombre d'incidents générés en laissant le groupe de sécurité du réseau (NSG) ouvert. </b>
 
   ![NSG Allowed Inbound Malicious Flows](https://i.imgur.com/bNOtiKt.png)<br>

 <br />
 <br />
 
 <b>Cette carte des attaques met en évidence les incidents liés aux échecs d'authentification syslog rencontrés par le serveur Linux. </b>
 
![Linux Syslog Auth Failures](https://i.imgur.com/nFR0Ehf.png)<br>

 <br />
 <br />
 
 <b>Cette carte d'attaque montre les échecs de RDP et de SMB contre la machine Window..</b>
 
![Windows RDP/SMB Auth Failures](https://i.imgur.com/4bylhdW.png)<br>

 <br />
 <br />
 
 <b>Cette carte des attaques présente les échecs contre le serveur MSQL..</b>
 
![MSSQL](https://i.imgur.com/TYIlNVI.png)<br>

 <br />
 <br />
 
 ## Après renforcement des mesures et contrôles de sécurité

Dans la phase "APRÈS", sur la base des incidents créés à partir de la capture des 24 heures "avant", j'ai mis en œuvre des mesures de renforcement et des contrôles de sécurité afin d'améliorer la sécurité de l'environnement contre les attaquants.<br /> 
Ces améliorations sont les suivantes

- <b>Groupes de sécurité du réseau (NSG)</b> : J'ai renforcé les NSG en ne permettant qu'à ma propre adresse IP publique de passer, sinon tout le reste du trafic serait bloqué par les nouveaux paramètres créés.

- <b>Pare-feu intégrés</b> : Dans mes machines virtuelles, j'ai configuré les pare-feu intégrés de manière à ce qu'ils refusent l'accès aux utilisateurs non autorisés. 

- <b>Private Endpoints</b> : Pour d'autres ressources Azure, j'ai remplacé les points d'extrémité publics par des points d'extrémité privés. Cela a permis de garantir que l'accès aux ressources sensibles, telles que les comptes de stockage et les bases de données, était limité au seul réseau virtuel.

## Cartes d'attaques après renforcement / contrôles de sécurité

<br />

```Toutes les requêtes cartographiques n'ont donné aucun résultat en raison de l'absence d'activité malveillante pendant les 24 heures qui ont suivi le durcissement..```

 <br />
 
## Mesures avant  renforcement /  contrôles de sécurité

The following table shows the metrics we measured in our insecure environment for 24 hours:<br/>
Start Time 2023-05-03 09:15 AM<br/>
Stop Time 2023-05-04 09:15 AM

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent (Windows VM)            | 4358
| Syslog (Linux VM)                   | 2345
| SecurityAlert (Microsoft Defender for Cloud            | 6
| SecurityIncident (Sentinel Incidents)        | 73
| NSG Inbound Malicious Flows Allowed | 103



## Mesures après renforcement / contrôles de sécurité

Le tableau suivant présente les mesures effectuées dans notre environnement sécurisé pendant 24 heures:<br/>
Start Time 2023-05-04 4:25 PM<br/>
Stop Time	2023-05-05 4:25 PM


| Metric                   | Count
| ------------------------ | -----
| SecurityEvent (Windows VM)            | 2364
| Syslog (Linux VM)                   | 24
| SecurityAlert (Microsoft Defender for Cloud            | 0
| SecurityIncident (Sentinel Incidents)        | 0
| NSG Inbound Malicious Flows Allowed | 0

<br/>


## Utilisation du guide de traitement des incidents informatiques NIST 800.61r2

Pour chaque attaque simulée, je me suis exercé à répondre aux incidents conformément à la norme NIST SP 800-61 r2.

![NIST 800.61](https://i.imgur.com/6PTG7c0l.png)

Chaque organisation dispose de politiques relatives à la réponse à un incident qu'il convient de suivre. Cet événement n'est qu'un aperçu des actions possibles en cas de détection d'un logiciel malveillant sur un ordinateur.

#### Preparation

- Le laboratoire Azure a été configuré pour ingérer tous les journaux dans Log Analytics Workspace, Sentinel et Defender ont été configurés et des règles d'alerte ont été mises en place.

#### Détection et analyse

- Un logiciel malveillant a été détecté sur un poste de travail et risque de compromettre la confidentialité, l'intégrité ou la disponibilité du système et des données.
- L'alerte a été attribuée à un propriétaire, la gravité a été fixée à "élevée" et l'état à "actif"
- Identification du compte utilisateur principal du système et de tous les systèmes concernés.
- Une analyse complète du système a été effectuée à l'aide d'un logiciel antivirus à jour afin d'identifier le logiciel malveillant.
- Vérification de l'authenticité de l'alerte en tant que "True Positive".
- Envoyé des notifications au personnel approprié, conformément aux politiques de communication de l'organisation.

#### Confinement, éradication et restauration

- Le système infecté et tout autre système infecté par le logiciel malveillant ont été mis en quarantaine.
- Si le logiciel malveillant n'a pas pu être supprimé ou si le système a été endommagé, il a été arrêté et déconnecté du réseau.
- En fonction des politiques de l'organisation, les systèmes affectés pourraient être restaurés dans un état connu, tel qu'une image système ou une installation propre du système d'exploitation et des applications. Il est également possible d'utiliser une solution antivirus à jour pour nettoyer les systèmes. 

#### Activité post-incident

- Dans ce cas simulé, un employé avait téléchargé un jeu contenant un logiciel malveillant. 
- Toutes les informations ont été rassemblées et analysées afin de déterminer la cause première, l'étendue des dommages et l'efficacité de la réponse. 
- Le rapport est diffusé à toutes les parties prenantes.
- Des mesures correctives sont mises en œuvre pour remédier à la cause première.
- Un examen des enseignements tirés de l'incident a été effectué.

<br />

## Conclusion

Dans ce projet, un mini honeynet a été construit dans Microsoft Azure et les sources de logs ont été intégrées dans un espace de travail Log Analytics. Microsoft Sentinel a été utilisé pour déclencher des alertes et créer des incidents sur la base des journaux ingérés. En outre, les paramètres ont été mesurés dans l'environnement non sécurisé avant l'application des contrôles de sécurité, puis après la mise en œuvre des mesures de sécurité. La réduction significative des événements et des incidents de sécurité après la mise en œuvre des contrôles de sécurité met en évidence l'efficacité de ces derniers dans la protection de l'environnement.

Il convient de noter que si les ressources du réseau étaient fortement utilisées par des utilisateurs réguliers, il est probable qu'un plus grand nombre d'événements et d'alertes de sécurité auraient été générés au cours des 24 heures qui ont suivi la mise en œuvre des contrôles de sécurité.

