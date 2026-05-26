# Lab 8 - Securite des applications mobiles

Ce depot documente un travail d'analyse statique realise sur l'APK pedagogique **DIVA.apk** dans le cadre du Lab 8 de securite des applications mobiles. Le README resume les captures d'ecran produites pendant le lab : preparation du perimetre, collecte d'informations, utilisation de BeVigil et analyse de l'application avec Yaazhini.

## Objectif du lab

L'objectif est d'analyser la posture de securite et l'exposition d'une application Android fournie dans un contexte pedagogique. Le travail reste limite a l'analyse autorisee de l'APK, sans exploitation active, sans intrusion et sans contournement de mecanismes de securite.

## Cible analysee

- **Application :** DIVA.apk
- **Type d'artefact :** APK pedagogique fourni par l'enseignant
- **Contexte :** cours / travaux pratiques de securite mobile
- **Environnement :** Windows
- **Outils visibles dans les captures :** PowerShell, BeVigil, Yaazhini

## Resume des resultats visibles

Les captures montrent une demarche structuree : creation d'un perimetre d'analyse, calcul d'une empreinte SHA-256 de l'APK, recherche de l'application dans BeVigil, puis analyse statique dans Yaazhini. BeVigil ne retourne aucun resultat pour `DIVA.apk`, tandis que Yaazhini identifie plusieurs points de vigilance : communications non securisees, application debuggable, sauvegarde Android active, export incorrect de providers, JavaScript active dans une WebView et usage du stockage externe.

Ces elements doivent etre consideres comme des constats de triage issus d'outils d'analyse statique. Ils servent a orienter la verification manuelle et la cartographie OWASP, mais ne constituent pas a eux seuls une preuve d'exploitation.

## Description complete des captures

### 1. Organisation du dossier de travail

Cette capture presente l'arborescence du lab. Elle montre une organisation par etapes avec un dossier de perimetre (`00-scope`) contenant `DIVA.apk` et `scope.md`, un dossier dedie a BeVigil (`01-bevigil`), un dossier dedie a Yaazhini (`02-yaazhini`), une phase de triage (`03-triage`) avec `owasp_mapping.md` et `triage.csv`, puis une phase de rapport (`04-report`) avec `rapport_final.md`. Le dossier contient aussi les captures, un fichier d'informations d'analyse, une checklist finale et un journal de commandes.

### 2. Definition du perimetre autorise

Cette capture montre la creation du fichier `analyse_info.txt` depuis PowerShell. Le contenu definit le perimetre de l'analyse : la cible autorisee est `DIVA.apk`, le projet est une application pedagogique de securite mobile et la source est le Lab 8. Les limites explicites indiquent qu'il n'y a pas d'exploitation des vulnerabilites, pas de tests intrusifs, pas de contournement actif et pas de ciblage d'applications non autorisees.

### 3. Informations generales de l'analyse

Cette capture poursuit la generation de `analyse_info.txt`. Elle ajoute les champs de suivi : date de l'analyse, nom de l'analyste, cible, type d'artefact, provenance, hash SHA-256 a renseigner, versions des outils BeVigil et Yaazhini, ainsi que l'environnement Windows.

### 4. Calcul de l'empreinte SHA-256

Cette capture montre l'utilisation de la commande PowerShell `Get-FileHash` sur `00-scope\DIVA.apk` avec l'algorithme `SHA256`. L'objectif est d'obtenir une empreinte unique de l'APK afin de garantir la tracabilite de l'artefact analyse et d'eviter toute confusion avec une autre version du fichier.

### 5. Page de recherche BeVigil

Cette capture affiche la page d'accueil de **CloudSEK BeVigil**, un service permettant de rechercher des informations publiques liees aux applications mobiles. La barre de recherche permet d'interroger la base BeVigil afin d'identifier des applications, des expositions, des jetons d'API ou d'autres indicateurs associes a une application mobile.

### 6. Recherche de DIVA.apk dans BeVigil

Cette capture montre une recherche de `DIVA.apk` dans BeVigil. Le resultat affiche **Found 0 Apps**, ce qui signifie que l'application n'est pas referencee dans les resultats BeVigil accessibles au moment de la recherche. Pour ce lab, ce resultat est coherent avec une application pedagogique locale qui n'est pas forcement publiee dans un magasin d'applications.

### 7. Gabarit de notes BeVigil

Cette capture montre la preparation du fichier `01-bevigil\bevigil_notes.md`. Le fichier est structure pour separer les faits certains, les hypotheses, les points d'interet, les domaines et sous-domaines, les endpoints et APIs, les URLs HTTP/HTTPS, les emails, les identifiants, les technologies detectees et le resultat general. Cette structure permet de conserver une trace propre des recherches OSINT ou d'exposition publique.

### 8. Telechargement de Yaazhini

Cette capture montre une page de commande ou de telechargement pour **Yaazhini Free Version**. Plusieurs fichiers sont disponibles, notamment l'installateur Windows `Yaazhini Setup 2.0.2 for Window OS.exe`, une version macOS et un fichier `Volki-Pro`. Elle documente l'etape d'obtention de l'outil d'analyse statique utilise ensuite sur l'APK.

### 9. Resume de l'application dans Yaazhini

Cette capture presente le resume genere par **Yaazhini Mobile APK Scanner**. L'outil identifie l'application analysee comme `DIVA88`, avec le package Android `jakhar.aseem.diva`. Les informations visibles indiquent une version `1.0`, un SDK minimum `15`, un SDK cible `23` et une taille d'environ `1.4 MB`. La date de scan affichee est le 1er mai 2026 a 23:15.

### 10. Permissions Android detectees

Cette capture liste les permissions Android detectees par Yaazhini. Trois permissions sont visibles : `WRITE_EXTERNAL_STORAGE`, `READ_EXTERNAL_STORAGE` et `INTERNET`. Les deux premieres concernent l'ecriture et la lecture sur le stockage externe, ce qui peut exposer des donnees si l'application y place des informations sensibles. La permission `INTERNET` autorise l'application a creer des connexions reseau.

### 11. Hosts et URLs detectes

Cette capture presente les hosts et URLs extraits par Yaazhini. On y voit `http://schemas.android.com` avec les chemins Android `apk/res/android` et `apk/res-auto`, ainsi que `http://payatu.com`, associe a un serveur Cloudflare. La presence d'URLs en HTTP est un point d'attention, car les communications non chiffrees peuvent exposer les donnees a l'interception ou a l'alteration.

### 12. Synthese des risques Yaazhini

Cette derniere capture montre la synthese des vulnerabilites ou alertes classees par niveau de severite. Yaazhini remonte un point **High** lie a une communication non securisee, trois points **Medium** concernant le mode debuggable, la sauvegarde Android et l'export incorrect de providers, un point **Low** sur JavaScript active dans une WebView, ainsi que des avertissements lies au stockage externe Android.

## Points de securite a retenir

- L'usage du stockage externe doit etre examine, surtout si des donnees sensibles y sont lues ou ecrites.
- Les communications HTTP doivent etre remplacees par HTTPS lorsque des donnees applicatives transitent sur le reseau.
- Le mode debuggable ne doit pas etre active dans une version de production.
- La configuration de sauvegarde Android doit etre controlee pour eviter l'extraction de donnees applicatives.
- Les composants exportes, notamment les providers, doivent etre justifies et proteges par des permissions adaptees.
- L'activation de JavaScript dans une WebView doit etre limitee aux cas necessaires et accompagnee de controles stricts.

## Conclusion

Les captures documentent une analyse statique complete et reproductible de l'APK DIVA dans un cadre pedagogique. La demarche commence par la definition du perimetre, continue avec l'identification de l'artefact et la recherche d'exposition publique, puis aboutit a une synthese des risques detectes par Yaazhini. Les resultats mettent en evidence plusieurs points de vigilance classiques en securite Android, utiles pour alimenter un rapport final et une cartographie OWASP Mobile.
