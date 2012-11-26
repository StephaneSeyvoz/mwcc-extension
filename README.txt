/**
 * 
 * Copyright Assystem 2011
 * Author  : St�phane Seyvoz
 * Contact : sseyvoz@assystem.com 
 * 
 */
 
 This software is a plugin enabling IAR compiler and linker support for the
 Mindc toolchain (http://mind.ow2.org). It enables the use of IAR-specific
 preprocessing, compilation, and linking flags, as the original Mindc only
 supported GCC and LD.
 
 French detailed information from a discussion between Matthieu Leclercq
 (ST Microelectronics developer maintaining Mind) and me :
 
Par d�faut, MIND suppose que le compilateur C est compatible avec GCC, mais
il est possible de faire une extension � mindc qui change ce comportement.
Dans l�architecture de la toolchain il y a un composant qui s�appelle le
� CompilerWrapper �. Par d�faut il n�y a qu�une seule impl�mentation de ce
composant qui est � GccCompilerWrapper � mais il est possible d�en impl�menter
d�autres pour des suites d�outil de compil qui ne sont pas compatible avec GCC.

Il faut donc d�velopper une extension de mindc. Ci-joint un squelette pour
cette extension. Il contient :

- 	Une classe IARCompilerWrapper qui �tend le GCCCompilerWrapper et qui doit
	�tre compl�t�e pour adapter la ligne de commande g�n�r�e pour ex�cuter le
	pr�processeur, le compilateur et le linker.
- 	Une classe IARCompilerModule. La toolchain MIND utilise un outil
	d�injection de d�pendance nomm� �Google Guice�. Cette classe
	IARCompilerModule est un module Guice qui sp�cifie que l�interface
	� CompilerWrapper � est impl�ment�e par la class � IARCompilerWrapper �.
	Ainsi les composants de la toolchain MIND qui utilise le CompilerWrapper,
	utiliseront une instance de cette nouvelle classe.
- 	Un fichier �mind-plugin.xml�. C�est le descripteur de l�extension. Le
	syst�me de plugin de MIND reprend les concepts d�extensions et de points
	d�extension de Eclipse. Dans ce fichiers ont d�crit donc les extensions
	qui se branchent sur la toolchain. Ce fichiers contient 2 extensions :
	
	o   La premi�re ajoute le flag ��iar� sur la ligne de commande �mindc�.
		Avec cette extension, �mindc� peut reconnaitre le flag ��iar� sur la
		ligne de commande.
	o   La deuxi�me ajoute le module Guice � IARCompilerModule �. Ce module
		surcharge le module � CommonBackendModule � afin de remplacer
		l�impl�mentation par d�faut de l�interface � CompilerWrapper � qui est
		sp�cifi�e dans ce module. De plus, cette deuxi�me extension n�est
		active que si le flag ��iar� est pr�sent sur la ligne de commande de
		mindc. Ceci permet d�utiliser le IARCompilerWrapper si et seulement si
		le flag ��iar� est sp�cifi� sur la ligne de commande de mindc.

Pour utiliser cette extension il suffit de la compiler avec maven �mvn package�
et de placer le fichier JAR produit dans le r�pertoire �ext� de mind. Ensuite
il faut lancer mindc avec l�option ��iar�.