---
layout: post
title: "Comment bien calculer le poids d'un selecteur CSS"
date: 2014-06-18 10:13
comments: true
categories: Dev
---



<p class="foreword">
Voici un article qui vous expliquera le poids des CSS. Cela est utile quand vous voulez surcharger un style, comprendre quel style s'applique à quel élément et pourquoi un style s'applique plutôt qu'un autre.
</p>
<!-- more -->

Quand vous reprenez un projet existant, qui date déjà un peu, vous vous retrouvez souvent avec des déclarations CSS à rallonge et ne savait plus quoi s'applique à quoi.

~~~
.container .info.test .spanner .wrapper .block div.pictos #jailanausee { color : #000; }
~~~
{:lang="css"}

Pour éviter je genre de massacre, il est d'une extrème importance de comprendre quel style va s'appliquer à quel élement, et dans quel ordre.
Prenons un exemple. Parmi ces quelques déclarations, laquelle va remporter le combat et s'appliquer au span. Quelle va être la couleur du texte du span : rouge, bleu, vert ou jaune ?

~~~
#idSpan { color: red; }
span.class-span { color: orange; }
.bloc.info span { color: yellow; }
.container .bloc.info .class-txt span.class-span { color: green; }
span { color: blue !important }
~~~
{:lang="css"}


~~~
<div class="container">
	<div class="block info">
		<span class="class-span" id="idSpan" style="color:purple;">
			je suis d'une certaine couleur
		</span>
	</div>
</div>
~~~
{:lang="html"}

La réponse est bleu ! [La preuve ici](http://jsfiddle.net/ptitgraig/L8CUS/2/).

Voici un court explicatif de l'ordre d'application des styles sur le span.

<table>
	</tr>
		<td>
			!important
		</td>
		<td>
			attribut style (style="")
		</td>
		<td>
			nombre d'id (#)
		</td>
		<td>
			nombre de classes (.)
		</td>
		<td>
			nombre de balises
		</td>
	</tr>
	<tr>
		<td>
			gagne sur tous les autres style, même seul
		</td>
		<td>
			0 .. n
		</td>
		<td>
			0 .. n
		</td>
		<td>
			0 .. n
		</td>
		<td>
			0 .. n
		</td>
	</tr>
</table>


Au final, vous vous trouvez avec un chiffre entre 0 et 19999. Reprenons notre exemple de départ. Sachant que l'attribut style s'applique toujours et annule les autres, nous allons nous en affranchir pour l'exemple.

<table>
	<tr>
		<th>Déclaration CSS</th>
		<th>!important</th>
		<th>nombre d'id (#)</th>
		<th>nombre de classes (.)</th>
		<th>nombre de balises</th>
		<th>poids</th>
	</tr>
	<tr>
		<td>#idSpan</td>
		<td>0</td>
		<td>1</td>
		<td>0</td>
		<td>0</td>
		<td>0100</td>
	</tr>
	<tr>
		<td>span.class-span</td>
		<td>0</td>
		<td>0</td>
		<td>1</td>
		<td>1</td>
		<td>0011</td>
	</tr>
	<tr>
		<td>.bloc.info span</td>
		<td>0</td>
		<td>0</td>
		<td>2</td>
		<td>1</td>
		<td>0021</td>
	</tr>
	<tr>
		<td>.container .bloc.info .class-txt span.class-span</td>
		<td>0</td>
		<td>0</td>
		<td>5</td>
		<td>1</td>
		<td>0051</td>
	</tr>
	<tr>
		<td>span (avec !important)</td>
		<td>1</td>
		<td>0</td>
		<td>0</td>
		<td>1</td>
		<td>1001</td>
	</tr>
</table>

Au final vous voyez l'ordre d'importance se déssiner :

1. span (avec !important) /\* 1001 \*/
2. \#idSpan /\* 0100 \*/
3. .container .bloc.info .class-txt span.class-span /\* 0051 \*/
4. .bloc.info span /\* 0021 \*/
5. span.class-span /\* 0011 \*/







