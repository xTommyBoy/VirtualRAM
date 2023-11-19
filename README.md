# VirtualRAM
WU de VirtualRAM

### Catégorie :
Reverse/Forensics

### Flag : 
```INTIGRITI{H3r0_0F_t1M3}```

### Auteur du challenge :
Mohamed Adil

### Auteur du WU :
moi(ScottKushy)

### Desc : 
I wonder what the old man is talking about.

# Write Up:

Sur ce coup ci, le challenge et l'énnoncé de celui-ci ne nous avance pas vraiment à grand chose, c'est en parti fait exprès, vous allez comprendre pourquoi.

### Identification : 
Après avoir lu l'énnoncé et avoir téléchargé le fichier ZIP, après extraction, on obtiens un fichier avec aucune extension et pour couronner le tout dans ce challenge du brouillard complet, ma madeleine de proust qu'est Detect-It-Easy(DIE Pour les intimes)

https://github.com/horsicq/Detect-It-Easy

Ne m'aide pas du tout 🙁.

![image](https://github.com/xTommyBoy/VirtualRAM/assets/66128183/058fe9d3-cf05-4ed0-87dd-1e03c1d6da49)

C'est alors que me viens l'une des idées "réflexes" (que je fais limite machinalement maintenant) qui est de regarder le fichier via un ```HEX editor```, en scrollant un peu on tombe sur ça :

![image](https://github.com/xTommyBoy/VirtualRAM/assets/66128183/cbd9be35-6ab4-43bf-8558-d5c6db469d03)

Une en-tête ```VIRTUALRAM``` ? Kessecé ? 
Une chose est sure, c'est bien l'en-tête d'un fichier classique. Mmmh classique ? pas tout à fait.

### Recherche :
Après maintes et maintes recherches sur Google et après avoir perdu foi en l'humanité, impossible de trouver cette fameuse en-tête quelque part (Même ChatGPT ne savais pas ce que c'était).

C'est ici que commence la partie intéressante, dans un dernier élan de réflexion je pense à ce magnifique tool qui est surement mon 2ème outil préféré je parle de :

#### Binwalk ! 
https://github.com/ReFirmLabs/binwalk

Je me demande tout simplement si il ne serait pas en capacité d'identifier l'en-tête de ce fichier (car binwalk identifie énormément d'en-têtes).

Un petit coup de binwalk (en forcing car un peu capricieux sur ce coup la) et : 

![image](https://github.com/xTommyBoy/VirtualRAM/assets/66128183/f7cf4653-7198-4137-8cd8-733babad9dad)

Bingo ! On trouve enfin d'ou viens l'en-tête de ce fichier, il s'agit d'une ROM Gameboy ? Interessant.

En cherchant bien sur Google on tombe sur un émulateur de GB (pour GameBoy au cas ou) qui permet de faire énormément de choses en terme de debug :
#### BGB
https://bgb.bircd.org/#downloads

### Reverse/Forensic : 
En lançant le jeu on à un petit écran d'accueil avec le nom du CTF :

![image](https://github.com/xTommyBoy/VirtualRAM/assets/66128183/f3ad1ea8-b3e2-4ccf-98fd-979c5d39b16c)

Un menu avec le nom du challenge avec une création de partie (Le créateur du challenge c'est vraiment fait chier à faire une sauvegarde je déconne pas) :

![image](https://github.com/xTommyBoy/VirtualRAM/assets/66128183/b6d67c4e-c800-4682-b388-938461550657)

Une fois qu'on lance la partie on atterrit sur un jeu bizarre où on manipule une princesse (je crois ?) qui est totalement bloquée après quelques pas:

![image](https://github.com/xTommyBoy/VirtualRAM/assets/66128183/1e1b5dd9-3169-42ff-82d6-1528f7d265ef)

Après reflexion, j'imagine qu'on peux sortir du mur via cheat engine ou via le créateur de cheat que possède BGB mais je n'ai pas procédé comme cela :

En ce rappelant du nom du challenge "VirtualRAM" (donc VRAM), on essaye donc de regarder si on ne peux pas récupérer la VRAM du jeu. En regardant la doc de BGB, on s'aperçoit que si, même mieux il transforme la VRAM en image et l'énumère dans les assets du jeu :

https://github.com/taylus/gb-rom-viewer/blob/master/VRAM_dumping.md

C'est à partir de la que viens le plus amusant ! (sarcastique bien sur...).

### Assets : 

En utilisant cette feature, on trouve tout simplement à partir de la partie jouable, le Flag ! 

![image](https://github.com/xTommyBoy/VirtualRAM/assets/66128183/c6d8a500-0148-46db-9b8b-84c37f0c3c30)

Super ! mais... ? pourquoi le flag est tout décalé ? Et en plus il m'est impossible de bouger les parties d'images dans les grilles via BGB, QUEL EST CET ARNAQUE !!!

## Explication : 

Pour expliquer pourquoi ces assets ne sont pas alignés correctement, tout part de la fonction de dumping dans BGB, le dumper VRAM de BGB va chercher dans la ROM du jeu toutes les parties visuelles sur lesquelles la ROM est utilisé dans la mémoire (ici le côté partie jouable du jeu) et va constituer tout les 16 octets une partie de ce bout de ROM en image (asset mis en tuile sur une grille). Si une des données graphiques ne commencent pas à une adresse divisible par 16 octets, elles seront donc décalées un peu soit avant ou soit après, Elles sont pour certaines inutilisables ou non dumpés par le dumper en question. (j'espere ne pas vous avoir perdu)

### Image Fixing : 

Bon c'est super tout ça mais que faire ? 
Deux choix s'offrent à nous, soit :
1. Dev une IA capable de découper et ranger d'elle même les parties du flag corrèctement (surement avec OpenCV et Tesseract) (chose que je suis incapable de faire).
2. Dev 2 scripts en python (ou ce que vous voulez) qui servent à découper l'image par ces dimensions et par les coordonnées des grilles données pour ensuite avec le deuxième script recoller par ordre numérique les parties du Flag (Petit clin d'oeuil au FCSC pour m'avoir appris ça):

https://cdn.discordapp.com/attachments/1075497745617530931/1175649224411594752/cropper.py?ex=656bffa9&is=65598aa9&hm=63044677a0265e152c2214be921e84b87a3aa53fdaa265fd8a4f6b4655de88f9&
(découpeur)

https://cdn.discordapp.com/attachments/1075497745617530931/1175649829880332369/paster.py?ex=656c0039&is=65598b39&hm=94c6d81d0aeb296b23bb4dc0ddc86310632eec286eef73ece3a78c47ec25105c&
(colleur, en collab avec l'aide de ChatGPT merci à lui).

3. faire ça manuellement.

Pour ma part ce sera fait manuellement par manque de temps (et surtout par flemme de dev, flemme que je n'ai pas lors de mon explication car j'aime apprendre des trucs aux gens qui prennent la peine de me lire ❤️).

Via ce site : 

https://alexyuisingwu.github.io/sliding-puzzle-solver/

Finalement après 30 minutes d'huile de coude environ, on arrive finalement à lire le flag ! :

![image](https://github.com/xTommyBoy/VirtualRAM/assets/66128183/341c27ba-1976-44b6-a7d0-0b4cd0373a7b)

## Conclusion : 

Après coup ce chall est très formatteur, non seulement culturellement mais aussi experimentalement car on apprend un peu à lire, à comprendre et à "fouiller" dans ces petites architectures.
C'est aussi un chall plutot amusant, cepandant je l'aurais plus mis dans la catégorie Misc mais bon, c'est pas moi qui ai fait le ctf ¯\_(ツ)_/¯.

Si vous voulez un peu de doc sur ce qu'est la ROM et la VRAM tout est ici :
https://gbdev.io/pandocs/Accessing_VRAM_and_OAM#:~:text=When%20the%20PPU%20is%20drawing,ignored%20(data%20remains%20unchanged).

https://mgba-emu.github.io/gbdoc/


### Merci de m'avoir lu, à plus les moldus ! ^^
